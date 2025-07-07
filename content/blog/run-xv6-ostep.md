---
title: "Running and debugging xv6 for OSTEP in a Docker container"
description: "Side-stepping the issue of running old xv6 on a newer platform, by running it on an older platform instead."
date: 2025-07-07T09:00:00Z
tags:
  - programming
  - computer systems
  - ossu
---
[Operating Systems: Three Easy Pieces](https://pages.cs.wisc.edu/~remzi/OSTEP/) by Remzi Arpaci-Dusseau is a free online textbook, that teaches fundamental operating system concepts. It's a great read (at least it has been so far), written in a very approachable style. As a companion to the textbook, Remzi has published several [programming projects](https://github.com/remzi-arpacidusseau/ostep-projects), many of which involve hacking on [xv6](https://pdos.csail.mit.edu/6.828/2012/xv6.html), a minimal teaching operating system based on UNIX, developed by MIT.

The issue you might encounter, if you try to do these projects, is that the version of xv6 used in OSTEP is the x86-version of xv6, which was discontinued in 2018 after MIT decided to move the xv6 project over to RISC-V. While you might be able to *build* the project on a modern machine by disabling the `-Werror` compiler flag, running it in QEMU may be a different story, at least out of the box. On my machine running Arch Linux, QEMU hangs with the following message:

```txt
SeaBIOS (version Arch Linux 1.16.3-1-1)


iPXE (http://ipxe.org) 00:03.0 C900 PCI2.10 PnP PMM+1EFD3360+1EF33360 C900
                                                                               


Booting from Hard Disk..
```

No good.

Fortunately, it's fairly easy to set up and environment that is of the era (circa 2018), using either virtual machines or containerization.

Because I like to stay in the terminal as much as possible, and because the xv6 Makefile includes targets for running QEMU without a GUI, I decided to go with a Docker container based on Ubuntu 18.04, which is the LTS version of the time. In order to run the projects, you need various build tools included in the build-essentials package, as well as QEMU (obviously), gawk,  GDB for debugging, and finally expect, which is used by the project testing script:
```Dockerfile
FROM ubuntu:18.04

ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get -yq update && apt-get -yq install --no-install-recommends \ 
    build-essential qemu gawk expect gdb
```
The DEBIAN_FRONTEND variable is necessary to avoid the package manager asking for user input during install. Save that file with the name "Dockerfile".

Now go ahead and build that image, and give it a nice descriptive name such as, say, "xv6-dev" by running the following command in the same directory as your Dockerfile:
```sh
docker build -t xv6-dev .
```
With that, we now have an image that we can use to build, run, and even debug xv6 in QEMU.

## So how do I use this thing?
Obviously a container image is no good if you don't know how to use it. Here's how.

First, let's assume you're working on a project such as [initial-xv6](https://github.com/remzi-arpacidusseau/ostep-projects/tree/master/initial-xv6), and you've checked out the xv6 repo into a directory named "src":
```sh
#inside the initial-xv6 directory
git clone https://github.com/mit-pdos/xv6-public.git src
```

In that case, navigate to the src directory and run the following:
```sh
docker run --rm -it -v $(pwd):/xv6 --workdir /xv6 -p "25000:25000" xv6-dev /bin/bash
```
A quick explanation of the command line arguments:
- `--rm`: removes the container after it stops
- `-it`: let's you interact with a terminal inside the container
- `-v $(pwd):/xv6`: mounts the present working directory to `/xv6` inside the container
- `--workdir /xv6`: sets the present working directory of the container to `/xv6`
- `-p "25000:25000"`: maps port 25000 of the host to port 25000 of the container.
Now you should be ready to build, run, and debug.

## Building, running, and debugging xv6
The xv6 Makefile defines several targets for not just building but also running the project inside QEMU, including targets with "nox" (for "No X (Server)") in their name, which are used for non-graphical environments. Our Docker container is a non-graphical environment, so that's perfect!

The eagle-eyed will have noticed that we expose port 25000 on the Docker container and wonder why. That is because the gdb-targets in the Makefile start a gdbserver listening on port 25000, which you can use to remotely debug the kernel using gdb on your host OS.

Running xv6 inside QEMU is as simple as running the following command:
```sh
make qemu-nox
```
You know it's working if you see the following prompt:
```txt
xv6...
cpu1: starting 1
cpu0: starting 0
sb: size 1000 nblocks 941 ninodes 200 nlog 30 logstart 2 inodestart 32 bmap start 58
init: starting sh
$ 
```

Debugging is a bit more involved, but not that difficult.
Inside the container run the following:
```sh
make qemu-nox-gdb
```
Your output should look a lot like this:
```txt
sed "s/localhost:1234/localhost:25000/" < .gdbinit.tmpl > .gdbinit
*** Now run 'gdb'.
qemu-system-i386 -nographic -drive file=fs.img,index=1,media=disk,format=raw -drive file=xv6.img,index=0,media=disk,format=raw -smp 2 -m 512  -S -gdb tcp::25000
```
Now on your host OS, navigate to the same `src` folder that xv6 is in, and run:
```sh
gdb kernel
```
Inside gdb, run `target remote localhost:25000`. This will connect your gdb client to the gdbserver that's running inside the container. Go ahead and set a breakpoint in main continuing execution. It should stop right at the beginning of main:
```txt
(gdb) target remote localhost:25000
Remote debugging using localhost:25000
0x0000fff0 in ?? ()
(gdb) break main
Breakpoint 1 at 0x80102ea0: file main.c, line 19.
(gdb) c
Continuing.

Thread 1 hit Breakpoint 1, main () at main.c:19
19	{
(gdb) 
```
And there you have it. You are now ready to debug your kernel hacking shenanigans.
