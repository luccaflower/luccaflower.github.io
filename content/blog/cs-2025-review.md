---
title: "2025 Computer Science Self-Study in Review"
description: "It's that time of the year again."
date: 2025-12-26T18:30:00Z
tags:
  - computer science
  - self-study
---

It didn't always feel like it, but 2025 was overall a pretty good year in terms of self-study. Despite a slump that lasted roughly four months,
I honestly think I made a lot of progress, and it almost feels a bit weird to look back at. I didn't quite finish everything I had planned to,
but that's ok. What it all comes down to is, the pace I was going at, coupled with a full-time job, was probably not sustainable, and I
really needed that break. And then, once I got back into a good routine, it was all smooth sailing again. So let's have a look at what I've done this year:

- **Discrete Mathematics by Epp, S.**: ✅ Completed
- **Introduction to Computer Systems by CMU**: ✅ Completed
- **Stanford Online Algorithms Specialization:** ⏩ In-progress, part 2/4
- **Operating Systems: Three Easy Pieces:** ⏩ In-progress, chapter 3?/44

## Discrete Mathematics
[Link](https://www.cengage.com/cgi-wadsworth/course_products_wp.pl?fid=M20b&product_isbn_issn=9781337694193)

'tis but a distant memory at this point. At least it feels like it. Finishing Epp was a long time coming, but I'm really glad I made it.
It bears repeating that this book taught me how to enjoy math. It does an excellent job building up a mathematical foundation for CS students.

I really appreciate that this book starts out with formal logic before anything else. It really forms the building blocks of how to *think* about
math, which turns out to be very important once you reach the subject of algorithms.

One of the final chapters is on the subject of the analysis of algorithms. I think many would consider it extracurricular, especially since a
dedicated algorithms course will go much deeper into the subject, but I decided to do it nonetheless, and it's been very beneficial. It spends a lot
of time on the mathematical background to the analysis of algorithms. I wrote a lot of proofs about certain functions being or not being at least or at most
in the order of some other function and it gave me a strong mental model of how to reason about that notation later. Highly recommend.

## Introduction to Computer Systems
[Link](https://www.cs.cmu.edu/afs/cs/academic/class/15213-f15/www/index.html)

In my last [year in review](../ossu-2024-review) I had yet to do the malloc-lab, which I'd eventually write about [in this blog post](../malloc).
Yet somehow I missed the most important lesson of all, which it turns out is *not* "learning how malloc works under the hood provides a strong
mental model for how to use malloc". That's a good lesson to learn for sure, but the more important lesson is that I know how to write custom memory
allocators now, and I use them all the time. It's called arena allocators, and they're essentially custom memory allocators over blocks of memory
that were allocated from the system memory allocator.

The starting code for the malloc lab is something called a "bump-allocator". It just has a pointer and a maximum. It allocates by increasing the pointer
and returning its previous address. If it reaches the maximum, it fails. The way it's presented is sort of tongue-in-cheek "this isn't very useful now, is it?".
*And yet*. It turns out, if you have some function that does a bunch of allocations, and you know at runtime that it's gonna use at most X amount of memory,
instead of dealing with all these different lifetimes of all these different pointers that you have to individually free when you're done with them
and you absolutely *can't* free them *before you're done with them, and *lord help you* if you free it *twice*, instead of all that, what if you just
had this *one* block of memory with a single lifetime, and you slice it up as you need it, and when you're finally done with it, you free it *once*.
It turns out, this sort of thing happens a lot in C programming, and it turns out that a bump-allocator is incredibly useful for *this* use case in particular.
As a result, I've made a lot of bump-allocators, especially in my algorithms course, which I decided to do in C because i'm Like That.

There's a lot more to talk about with regards to this course. It made me a better programmer in many ways, and it has made OSTEP much *much* easier
to work through.

## Stanford Online Algorithms Specialization
[Link](https://www.algorithmsilluminated.org/)

Algorithms is one of those things that are on my List of Things Every Programmer Should Know. This course definitely isn't the *easiest* introduction
to the subject, but on the other hand it provides some very strong fundamentals in how to reason about algorithms. I was doing this course at a pretty
quick pace, finishing part 1 and part 2 in about 4-5 weeks each, before I hit my slump. I'm pretty excited about starting part 3 after OSTEP, but I think
I need to do one thing at a time right now.

This is primarily a math course, and more specifically a proof-based course. It's proofs of correctness and proofs of runtime-analysis all the way down,
rounding off with a little programming problem at the end of each section, which roughly boils down to "implement this pseudocode and run it against the test cases".

I chose C for the programming problems for one reason primarily: C doesn't give you a lot of things for free, so if you want, say, a hash table, you
need to bring your own hash table. As a result, I've implemented all sorts of data structures, from index-based priority queues and binary trees to adjacency list graphs and, yes,
hash tables, all from scratch, so I could use them in various algorithms. It's all about learning the internals to strengthen that mental model.

The computer systems course, and in particular the deeper understanding of memory that follows, has been incredibly useful here. A lot of times I've
found myself scanning the input to determine exactly how much memory and then allocating exactly that amount of memory for my data structure. Very cool stuff!

I purchased the Algorithms Illuminated book by the course author some 2 years ago, and I've been using it as my primary resource for the course. It's
pretty cheap for a university textbook, and *very* worth it. It's a lot more detailed with the math compared to the video lectures, in my opinion.

## Operating Systems: Three Easy Pieces
[Link](https://borrproject.github.io/computer-science/systems/ostep/#extended-approach)

Unlike the algorithms course, this might just be the easiest introduction to the subject you'll find. Not because it doesn't get difficult (it does),
but moreso because Remzi and Andrea (most people seem to forget that there are two authors of this book) are really good at building you from the ground up
to be able to handle that difficulty. This makes it very well-suited for self-study, and they made all of that available for free!

This book is split into three main "parts" (or "easy pieces" as it were): virtualization, concurrency, and persistence. As of writing I have just
finished the concurrency piece. The neat thing is, it explains things both from the client-side (what does a program need an operating system to do and why?)
and from the OS side (how does an operating system implement that?). While the part on virtualization mostly built upon the things I had already learned from
computer systems, the concurrency part has been a boon. It goes very deep into the weeds of how concurrency and threads work, how the POSIX threads API works(!),
and what sort of concurrency primitives are needed (mostly locks and condition variables), not to mention how to use them to build various concurrent
structures like a bounded consumer/producer queue (very useful). There are very few programs worth doing that don't involve concurrency in some manner,
and having a good understanding of how that works is pretty essential in my opinion.

With the OSTEP book and lectures there are also projects available to tie all these concepts together. I decided to do the same set of projects that were assigned
to the Spring 2018 class at University of Wisconsin-Madison. Much like the book, there are projects focused on the client-side (make a program that uses these OS features),
and projects that are essentially kernel hacking projects that have you implement some feature into the xv6 operating system.

One thing about the kernel hacking projects that I hadn't thought about before I did them, but which I think might be a seriously underrated aspect of them
is that they give the student some real experience working with a fairly substantial existing code base in order to implement a feature without breaking
anything else. This is something that most CS graduates will be doing when they start working in the field, and more importantly, this is not something
CS students get a lot of experience with during school. This alone is a good reason for anyone to do at least some of these projects.

## So what did we learn
It would appear that learning how to not get to the point where I have to force myself to relax will always be a challenge. This time it resulted in
a four month break. The alternative might just have been burnout. The problem, it seems, is that once I get fixated on something, I can't let it go.
It's just how my brain works. I need to find another relaxing video game to play when I get like that, I think.

At some point during the year I had made it my goal to finish OSTEP, Algorithms, and Computer Networking by the end of the year. Alas, the slump
ensured that wasn't going to happened, but I think that's ok. After all, I made a lot of progress regardless, and there's always next year. As long as
I keep putting one food in front of the other, that's all that really matters.

## Looking ahead
With concurrency being (most likely) the longest and most difficult part of OSTEP, that course is nearing its end. After that I'll finish up Algorithms,
but then what?

There's one subject that I am absolutely certain I will study, which is Computer Networking. The plan is to use course materials based on the free
online textbook [Computer Networks: A Systems Approach](https://book.systemsapproach.org/). It's just such a natural progression from all
the systems-level programming I've done so far, and as you might be able to tell, I like learning how things work under the hood.

Another thing I've considered is the [Database Systems](https://15445.courses.cs.cmu.edu/fall2024/) course from CMU (they really like skulls on this one),
another systems based course. This one has you implement things in a database using C++! I bet there's gonna be a lot of concurrency in this one.

And finally, I've taken an interest in computing history and I'd love to learn more. It's a bit of a detour from computer *science*, but I'd say
it's still relevant, and history is important!
