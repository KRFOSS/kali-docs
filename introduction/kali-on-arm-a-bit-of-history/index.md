---
title: Kali's ARM history
description:
icon:
date: 2019-11-25
type: post
weight: 60
author: ["steev",]
keywords: [""]
og_description:
---

## Kali on ARM - A bit of history

When BackTrack ARM first came out, it was one image, for a Motorola Xoom. The work was done on the Xoom itself by muts. He started from an Ubuntu image for it, built all of the packages for Backtrack on it, then installed them. I then took the work and expanded it to support 3 or 4 different ARM devices I had, following a similar proceedure. I showed muts the work I'd done and he was as excited about it as I was.

When Kali came about, we retooled everything, including build servers for armel, armhf, and arm64. No more building packages manually on the ARM devices themselves. So everything was in place, but the images for ARM devices were still being built manually. Putting out an updated image meant downloading the last release, writing it to an sdcard, booting the device, running updates, building the kernel, installing the new kernel, cleaning up the logs and apt cache, then powering the system off, plugging the sdcard back into my other system, and creating a dd image of the sdcard, putting it on to a server. This was very error prone due to the nature of sd cards from different manufacturers having different actual sizes.

We wanted to make it so anyone could, starting from a Kali amd64 installation, build an image that would work on any of our supported ARM devices, end up with exactly what we put out, and most importantly, customize it for their needs. So I created the [kali-arm-build-scripts](https://gitlab.com/kalilinux/build-scripts/kali-arm) - they aren't fancy, but they're easy to read, follow and modify.
