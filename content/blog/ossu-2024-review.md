---
title: "OSSU 2024 In Review"
description: "I've had a productive year"
date: 2024-12-28T20:00:00Z
tags:
  - journal
  - ossu
---
This year I started the [OSSU Computer Science curriculum](https://cs.ossu.dev/) (with modifications). Here's how that has gone as of the end of 2024:
* **Nand2Tetris**: ✅ Completed, 2024-04-20
* **Systematic Program Design**: ✅ Completed, 2024-09-21
* **Discrete Mathematics by Epp, S.** ⏩ In-progress, chapter 8/12
* **Introduction to Computer Systems** ⏩ In-progress, lecture 15/27, lab 4/7
## Nand2Tetris
This course taught me that I can learn anything I want. It also taught me a bunch of cool things about computer architecture.

Nand2Tetris is a course that takes you all the way from basic digital logic circuits to computer that can (int theory) run tetris. Through a series of projects you get to specify all the hardware components of a whole computer (they give you a clock, a screen, and a keyboard for free), make an assembler, a two-stage compiler for a simple programming language, and an OS written in that language.

The course gives each of its subjects a surface-level treatment, focusing instead on how each subject relates to the other, For an introductory computer architecture course, I think that is perfect. You really get an intuition for *why* things are the way they are, while at the same time giving you an opportunity to get a feel for a variety of subjects in Computer Science that you can then choose to make a deeper dive into.

It's also great for building intuition about how computers actually. Before this course, the low-level mechanics of how memory and CPU instructions work were pure magic, but afterwards I had all the tools I needed to reason about it. This was especially helpful for the first half of the Introduction to Computer Systems course (especially the machine programming parts), since most of it was just the specifics of how x86-64 implements concepts I already learned in N2T.
## Systematic Program Design
This is a solid small-scale software design course. I mostly took this course to see what sort of programming practices they're teaching young programmers these days, and I'm happy to report that the state of things is pretty good at this level at least. I'm especially happy about the TDD-style approach to things, even though this course's variation of it is more of a top-down approach than what Kent Beck had in mind. I also think if you treat the design recipes as ways to reason about a problem, and the templates as drilling exercises to get you used to that kind of reasoning, you'll get a lot out of this course. You're not supposed be copy-pasting templates in your professional work, but doing it the first few times will help you establish those thought patterns.
## Discrete Mathematics
I think I've learned how to enjoy math. I don't know if it's The Joy of Learning in general, or if there's something specific about math. I think part of it might be that I really enjoy exploring systems of logic, and what is mathematics if not that. So far in this book I've explored propositional logic, number theory, sequences and induction, set theory, and relations and functions. I will admit one thing, however: number theory is not really my jam. I've noticed that my progress slows down significantly the more number theory I'm doing. Everything else is super cool though.
## Introduction to Computer Systems
I'm going to make a bold claim. Carnegie Mellon University consistently ranks among the best universities in the world for Computer Science, and this course is one of the major reasons why. It covers a lot of the same material as Nand2Tetris but in much greater detail, and in the context of real-world hardware and software. The best way I can describe this course is that it bridges the gap between hardware and operating system. Like Nand2Tetris, it is not a course that covers one singular subject, but rather a collection of subjects that all relate to and build on each other. So far I've learned about binary data representation (I finally understand how floats work), x86-64 machine code, pipelining and optimization, the memory hierarchy, and a bit of OS stuff. Now, this is a difficult course. You cannot simply read the book and watch the lectures, and then go on your merry day, you will not learn a thing from that. You have to do the practice problems from the book, you have to do the labs, and sometimes, it takes a few tries to fully *get* a concept (I had to do several passes on code optimization in order to fully get how to reason about data-dependencies and pipelined CPU-instructions), but once you *get it*, it is so rewarding. One thing to note is, you *need* the textbook, which is called Computer Systems: A Programmer's Perspective, you need the US-edition specifically, and there are no good versions online, in stores or on the seven seas. And the print version is $160. So keep that in mind.

## Lessons Learned
The most important thing I learned this year was how to structure myself
and my own learning, and to be consistent with it. It's something I
always struggled with.

Another thing is the need to pace myself. It's commonly said on the OSSU
discord that "speedrunning always takes longer". This is true in two
ways:
1. Rushing things is a detriment to learning. When you feel like you're
	 in a hurry, you tend to skip over things, not do problems, etc. and
	 as a result you don't learn what's needed in order to proceed. The
	 result is a need to go back and repeat things you were already
	 supposed to have learned. Take your time.
2. Burnout. Something like OSSU is a multi-year project. It turns out,
	 learning a bachelor's/master's degree worth of material is going to
	 take about a bachelor's/master's degree amount of time. Pushing yourself and overworking yourself for that long is gonna bite you, it's gonna lead to burn you out, and it's gonna slow you down in the long run.
	 I realized at some point, that I needed to *learn* how to force
	 myself to relax. That means finding an activity wholly unrelated to
	 studies or work. Play videogames, hang out with friends, drink a cup
	 of tea while watching a show. Just make sure you relax.

## What's Next?
I try not to plan things too far. Usually the final decision on "what
course/book/thing comes next" is made the moment i finish my current
thing. With that being said, there's at least a few topics I want to
explore in the new year.
### Algorithms
This is the natural next step after discrete mathematics. Part of why I
started this whole thing was, that I wanted to become a better
programmer, and the things I wanted to work on were the bare
fundamentals. This is just that.

I'll be taking the theoretical approach for the simple reason that I'm a
nerd for theory. That means studying algorithms not as a subject of
programming, but as a subject of math. Looking forward to it!
### Operating Systems
Clearly I've caught the systems programming bug. I just think it's neat.
I've got no idea if I'll ever get to work in systems programming
professionally, but at the very least I find it fun right now.

The popular self-study course on this topic is OSTEP, and that's most
likely the course I'm going to follow. Free online textbooks are just
too good to pass up on.
