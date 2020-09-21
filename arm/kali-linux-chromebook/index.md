---
title: HP Chromebook
description:
icon:
date: 2019-11-25
type: archived
weight: 100
author: ["steev",]
tags: ["",]
keywords: ["",]
og_description:
---

The [HP ARM Chromebook](http://www8.hp.com/ca/en/ads/chromebooks/specs.html) is an ultraportable laptop. It was quite a challenge, but we have a Kali image that runs great on the Chromebook. Boasting an Exynos 5250 1.7GHz dual core processor and 2 GB of RAM, the Chromebook is a fast ARM laptop. Kali Linux fits on an USB stick on this machine which leaves the internal disk untouched.

## Kali on Chromebook - User Instructions

If all you want to do is install Kali on your HP ARM Chromebook, follow these instructions:

1. Get a nice fast 8 GB USB stick.
2. Put your Chromebook in developer mode, and enable USB boot.
3. Download the Kali HP ARM Chromebook image from our [downloads](https://www.offensive-security.com/kali-linux-arm-images/) area.
4. Use the **dd** utility to image this file to your USB device. In our example, we use a USB stick which is located at **_/dev/sdb_**. **_Change this as needed._**

{{% notice info %}}
This process will wipe out your USB stick. If you choose the wrong storage device, you may wipe out your computers hard disk.
{{% /notice %}}

```
dd if=kali-linux-$version-chromebook.img of=/dev/sdb bs=4M
```

This process can take awhile depending on your USB storage device speed and image size.

Once the _dd_ operation is complete, boot up the Chromebook with the USB stick plugged in. At the developer boot prompt, hit CTRL+U, which should boot you into Kali Linux. Log in to Kali (**_root_** / **_toor_**) and **startx**. That's it, you're done!

## Kali on HP ARM Chromebook - Developer Instructions

If you are a developer and want to tinker with the Kali HP ARM Chromebook image, including changing the kernel configuration and generally being adventurous, check out the [kali-arm-build-scripts](https://gitlab.com/kalilinux/build-scripts/kali-arm) repository on GitHub, and follow the _README.md_ file's instructions. The script to use is **chromebook-arm-hp.sh**
