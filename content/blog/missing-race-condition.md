---
title: "The Case of the Missing Race Condition"
description: "Stop being correct dammit!"
date: 2025-12-16T20:00:00Z
tags:
  - programming
  - computer systems
  - operating systems
---
One of the more interesting programming projects for the [OSTEP](https://pages.cs.wisc.edu/~remzi/OSTEP/) book is the [xv6 Kernel Threads](https://github.com/remzi-arpacidusseau/ostep-projects/tree/master/concurrency-xv6-threads#kernel-threads)
project, which has you implement actual multi-threading support for user-level programs in the xv6 kernel, as well as a small library to support it,
locks and all. From my understanding, this is a very popular type of lab for university courses in Operating Systems, and it is certainly a great project
for learning the low-level building blocks of concurrent programming.

Unfortunately, no test code for this project has been released yet, so if you're doing this project on your own, and you
want to verify the correctness of your work (which you should), then you have to write your own.

Given the problem statement, it's actually quite straightforward to figure what needs to be verified and how:
 1. the clone syscall creates a new thread that runs the code at the supplied address
 2. the new thread shares its memory address space with its parent
 3. the provided stack address is the same that gets returned from the corresponding join
 4. the lock provides mutual exclusion and protects against race conditions

For statements 1 through 3, the verification process requires little more than setting variables and comparing them after a join. Easy.

The 4th statement is where it gets tricky. Race conditions, as it turns out, can be notoriously tricky to trigger, as they often have critical sections
(sections within which interleaving becomes a problem) that are just a few instructions in length.

Not to worry, I have just the thing: one of the most commonly used introductory examples of a data-race is the concurrent loop-counter. Simply run
`count++` in a loop, in multiple concurrent threads, where `count` is a shared variable. Just make sure to mark it `volatile`, so the compiler doesn't
trick you with a `mov $count_addr %eax, add $loop_count %eax, mov %eax $count_addr`.

Whenever I've run these examples natively, it has never taken more than a count of a few thousand across 2-4 threads to get a final count that's totally
wrong, especially on a multi-core CPU. So surely, it shouldn't be too hard to make the same thing happen inside xv6, right?

So that's the idea. Have some piece of code that definitely triggers the race-condition, and then have the same piece of code, but use
a lock to protect against the race condition, and verify that it has been resolved.

The test code for triggering the race-condition is dead simple, of course:
```c

void
counter(void *arg1, void *arg2)
{
  for (int i = 0; i < LOOPS; i++) {
    count++;
  }
  exit();
}

/* ---- ... ---- */

/* --- main --- */
  printf(1, "counter: [loops=%d threads=%d expected=%d]\n", LOOPS, THREADS, LOOPS * THREADS);
  printf(1, "counting without locks\n");
  printf(1, "starting count...\n");
  count = 0;
  int spawned[THREADS];
  int joined[THREADS];
  for (int i = 0; i < THREADS; i++) {
    spawned[i] = Thread_create(counter, (void *)i, (void *)0);
  }
  for (int i = 0; i < THREADS; i++) {
    joined[i] = Thread_join();
  }
  printf(1, "\nspawned: [ ");
  for (int i = 0; i < THREADS; i++) {
    printf(1, "%d ", spawned[i]);
  }
  printf(1, "]\n");
  printf(1, "joined:  [ ");
  for (int i = 0; i < THREADS; i++) {
    printf(1, "%d ", joined[i]);
  }
  printf(1, "]\n");
  printf(1, "count: %d\n", count);
  if (count == 0) {
    printf(1, "FAILURE: threads never started\n");
    exit();
  } else if (count != THREADS * LOOPS) {
    printf(1, "race condition! (this is ok)\n");
  } else {
    printf(1, "no race condition? odd...\n");
    printf(1, "not a critical failure, but might indicate a problem with the thread implementation\n");
  }

```

```txt
counter: [loops=500000000 threads=4 expected=2000000000]
counting without locks
starting count...

spawned: [ 8 9 10 11 ]
joined:  [ 9 11 10 8 ]
count: 2000000000
no race condition? odd...
```
Oh no.

So what's happening here? Did I do something wrong when implementing threads? Set up the stack wrong? Sharing registers? Well, those things  would
cause things to go wrong in entirely different, and far more dramatic ways than simply "the count isn't wrong and it's supposed to be wrong".

Maybe it's the scheduler! Maybe the thread somehow runs to completion before it ever gets interrupted, thus never triggering the race condition!

I tried so many things. I added print-statements in the scheduler to see if the threads got scheduled more than once (they did),
I tried using up to 32 threads, I tried adding CPU's to QEMU, I tried using arrays of count-variables to increase the likelyhood of bad interrupts,
I even modified the code that sets the timer interrupt vectors in order to reduce the timer and increase the amount of interrupts, but all *that*
accomplished was slowing down the operating system.

I chalked it up to quirks of the emulation (Professor Remzi appears to agree), and decided on a new approach: `printf`.

As it turns out, xv6-printf is *not* thread-safe. That makes sense, why would there be user-level thread-safety with no support for, well, threads?
But it gets even better. It turns out, that for every printed character, printf invokes a `write`-syscall of 1 byte, causing an interrupt. What this
means for our testing is, if we use printf in multiple concurrent threads, we get a critical section of code with guaranteed interrupts.
That is *exactly* what is needed.

I made three simple routines. One with no lock, one which locks for the whole loop, and one which locks for each call to printf:
```c
void
print_no_lock(void *arg1, void *arg2) {
    int id = (int)arg1;
    for (int i = 0; i < 5; i++) {
        printf(1, "[%d] print %d\n", id, i);
    }
    exit();
}
void
print_coarse_grained(void *arg1, void *arg2) {
    int id = (int)arg1;
    lock_t *lock = (lock_t *)arg2;
    lock_acquire(lock);
    for (int i = 0; i < 5; i++) {
        printf(1, "[%d] print %d\n", id, i);
    }
    lock_release(lock);
    exit();
}

void
print_fine_grained(void *arg1, void *arg2) {
    int id = (int)arg1;
    lock_t *lock = (lock_t *)arg2;
    for (int i = 0; i < 5; i++) {
        lock_acquire(lock);
        printf(1, "[%d] print %d\n", id, i);
        lock_release(lock);
    }
    exit();
}
```

And let's see those results:
```txt
First, print without locking
[0] print 0
[0] print 1
...
[1] [2] print 0
pri[nt[2] 3] print 0
[3] print 1
...
[2] print 4
Print coarsely grained with an initialized lock (lines may not interleave between threads)
[0] print 0
[0] print 1
[0] print 2
[0] print 3
[0] print 4
...
[3] print 4
Finally print finely grained (lines may interleave between threads, but no line may be garbled)
[0] print 0
[0] print 1
[1] print 0
[2] print 0
[3] print 0
[0] print 2
...
[3] print 4
DONE!
```
Perfect! And the lock works too!
