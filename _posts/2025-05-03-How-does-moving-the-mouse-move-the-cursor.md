---
layout: post
title: "How does moving the mouse move the cursor"
date: 2026-05-03
---

My ultimate aim is to make a series of explanations of how computers do various things
and in doing so naturally motivate teaching the various parts and concepts needed to
understand computers. Kind of like teaching many real sentences of a language and
explaining grammar and vocab.

[gif of a hand moving a mouse side by side with the cursor moving on a display on the right]
Right, simple enough question.

The machine I'll be considering is this 2019 amd laptop with a usb mouse plugged in.
I have arranged and photographed its innards on the right.
[picture of the machine - picture of the circuit boards aranged]

Ok, first a high level sequence of events, thinking in terms of abstract messages passing 
from one part of the machine to another, without yet worrying about how electricity is
used to send these signals

[diagram of innards on the left, abstract view from the processor on the right]

1. The mouse detects that it has moved and stores its relative x,y motion to respond with
when it is interrogated by the usb host controller.
2. The usb host controller is configured to interrogate the usb mouse at a certain polling
rate, in this case 100Hz, or once every 10ms.
3. The mouse returns a usb packet containing the total relative motion it recorded since 
it was last interrogated. (It also contains whether any buttons were pressed or releaded).
4. The usb host controller, in this case a part of the main chip in the laptop, has been
configured to put this information at a location in RAM that it was told about previously
by a processor. The processes that allows things other than the cpu to read and write
information into ram is known as DMA - direct memory access.
5. Once the usb host controller has put the data in ram, it pokes a processor to tell it 
that there is new mouse data in the ram and to go take a look right now. This type of 
signal is aptly called an interrupt.
6. The cpu has a part where it stores the address in ram of a table of more addresses to the
the pieces of code that need to be run when a given interrupt is triggered.
7. The cpu starts reading instructions from the relevant area in ram and doing them,
this code is called the interrupt handler and begins by saving the data that was in the
processor somewhere in ram so that it can get it back later when it goes back to what it
was in the middle of.
8. The interrupt routine does something, code in the kernel gets run, in the xhci driver,
usb/usbhid/usbhost? driver, the
9. Somehow the event file for the mouse gets updated and the kernel is storing which
programs are reading the file and waiting for the next piece of data to come in.
In this case the Xorg process is, it uses libinput which uses libevdev? which uses glibc
to do the read syscall.
10. Something something something, the xorg process communicates with the integrated amd
gpu also on the main piece of silicon (putting info in ram that the gpu also has access
to?) by using some userspace amd driver library to do ioctl syscalls to tell the kernel
to use the amd driver to tell the amd gpu to update the cursor position so that when the
image nexts gets written out by the (scan unit?) it is with the curor in the updated
location


