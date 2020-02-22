---
title: EfikaMX
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

The EfikaMX is a low end, low cost ARM computer. Despite its less-than-stellar specifications, its affordability makes it an excellent option for a tiny Linux system.

## Stock Kali on EfikaMX - Easy Version

If all you want to do is to install Kali on your EfikaMX, follow these instructions:

1. Get a nice fast 8 GB (or more) SD card. Class 10 cards are highly recommended.
2. Download the Kali Linux EfikaMX image from our [downloads](https://www.offensive-security.com/kali-linux-vmware-arm-image-download/) area.
3. Use the **dd** utility to image this file to your SD card. In our example, we assume the storage device is located at /dev/sdb. **Change this as needed.**

{{% notice info %}}
**Alert!** This process will wipe out your SD card. If you choose the wrong storage device, you may wipe out your computers hard disk.
{{% /notice %}}

```
root@kali:~ dd if=kali-1.0.3-efikamx.img of=/dev/sdb bs=512k
```

This process can take a while depending on your USB storage device speed and image size. Once the dd operation is complete, boot up your EfikaMX with the SD card plugged in. You will be able to log in to Kali (root / toor) and **startx**. That's it, you're done!

## Kali on EfikaMX - Long Version

If you are a developer and want to tinker with the Kali EfikaMX image, including changing the kernel configuration and generally being adventurous, check out the [kali-arm-build-scripts](https://gitlab.com/kalilinux/build-scripts/kali-arm) repository on github, and follow the README.md file's instructions. The script to use is efikamx.sh
