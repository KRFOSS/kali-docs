---
title: Patching the Kernel
description:
icon:
type: post
weight:
author: ["re4son",]
---

We will continue from the [Porting NetHunter page](/docs/nethunter/porting-nethunter/) and work on the Neus 6P kernel as an example. The idea stays the same though.

&nbsp;

## Patching

By default, we apply wifi injection patches and patches that add wifi drivers.
Kernel versions older than 4.x will require HID keyboard / mouse patches.
We no longer need CD-ROM patches as all modern operating systems support being installed from USB storage devices, thus we can mount NetHunter as USB drive.

In the Kernel-Builder, choose ***"Apply NetHunter kernel patches"***:

![](nh-kernel-010-patching1.png)

&nbsp;

 navigate to the directory that closest resembles your kernel version:

![](nh-kernel-020-patching2.png)

&nbsp;

 and apply each applicable patch:

![](nh-kernel-030-patching3.png)

&nbsp;

We recommend that you work in another terminal window in parallel and commit the changes to the kernel source after having applied each patch.
