---
title: USB Armory
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

The [USB armory](https://inversepath.com/usbarmory) from Inverse Path is an open source hardware design, implementing a flash drive sized computer. Kali Linux fits on a micro SD card for it.

## Kali on USB armory - User Instructions

If all you want to do is install Kali on your USB armory, follow these instructions:

1. Get a nice fast 8 GB micro SD card.
2. Download the Kali USB armory image from our [downloads](https://www.offensive-security.com/kali-linux-arm-images/) area.
3. Use the **dd** utility to image this file to your microSD device. In our example, we use a microSD which is located at **_/dev/sdb_**. **_Change this as needed._**

{{% notice info %}}
This process will wipe out your SD card. If you choose the wrong storage device, you may wipe out your computers hard disk.
{{% /notice %}}

```
xzcat kali-$version-usbarmory.img.xz | dd of=/dev/sdb bs=512k
```

This process can take awhile depending on your device speed and image size.

Once the _dd_ operation is complete, boot up a computer with the USB armory plugged in. Log in to Kali (**_root_** / **_toor_**), that's it, you're done!

## Kali on USB armory - Developer Instructions

If you are a developer and want to tinker with the Kali USB armory image, including changing the kernel configuration and generally being adventurous, check out the [kali-arm-build-scripts](https://gitlab.com/kalilinux/build-scripts/kali-arm) repository on GitHub, and follow the _README.md_ file's instructions.  The script to use is **usbarmory.sh**
