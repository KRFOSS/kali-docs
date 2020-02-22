---
title: Samsung ChromeBook
description:
icon:
date: 2019-11-25
type: post
weight: 100
author: ["steev",]
tags: ["",]
keywords: ["",]
og_description:
---

The Samsung ARM Chromebook is an ultraportable laptop. It was quite a challenge, but we have a Kali image that runs great on the Chromebook. Boasting an Exynos 5250 1.7GHz dual core processor and 2 GB of RAM, the Chromebook is a fast ARM laptop. Kali Linux fits on an external SD card on this machine which leaves the internal disk untouched.

## Kali on Chromebook - User Instructions

If all you want to do is install Kali on your Samsung ARM Chromebook, follow these instructions:

1. Get a nice fast 8 GB SD card or USB stick.
2. [Put your Chromebook in developer mode](http://www.chromium.org/chromium-os/developer-information-for-chrome-os-devices/samsung-arm-chromebook#TOC-Developer-Mode), and enable USB boot.
3. Download the Kali Samsung ARM Chromebook image from our [downloads](https://www.offensive-security.com/kali-linux-arm-images/) area.
4. Use the **dd** utility to image this file to your SD /USB device. In our example, we use a USB stick which is located at /dev/sdb. **Change this as needed.**

{{% notice info %}}
**Alert!** This process will wipe out your SD card. If you choose the wrong storage device, you may wipe out your computers hard disk.
{{% /notice %}}

```
xzcat kali-$ver-chromebook.img.xz | dd of=/dev/sdb bs=512k
```

This process can take awhile depending on your USB storage device speed and image size.

Once the dd operation is complete, boot up the Chromebook with the SD / USB plugged in (NOT IN THE BLUE USB PORT!). At the developer boot prompt, hit CTRL+U, which should boot you into Kali Linux. Log in to Kali (root / toor) and **startx**. That's it, you're done!

## Kali on Samsung Chromebook - Developer Instructions

If you are a developer and want to tinker with the Kali Samsung Chromebook image, including changing the kernel configuration and generally being adventurous, check out the [kali-arm-build-scripts](https://gitlab.com/kalilinux/build-scripts/kali-arm) repository on github, and follow the README.md file's instructions. The script to use is chromebook-arm-samsung.sh
