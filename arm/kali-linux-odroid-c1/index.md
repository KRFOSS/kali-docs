---
title: ODROID-C1
description:
icon:
date: 2019-10-26
type: post
weight: 100
author: ["steev",]
tags: ["",]
keywords: ["",]
og_description:
---

The [ODROID-C1](http://www.hardkernel.com/main/products/prdt_info.php?g_code=G141578608433) is a quad core 1.5GHz Cortex A5, with 1GB of RAM development board. Kali Linux fits on an external micro SD card or on an eMMC module.

## Kali on ODROID-C1 - User Instructions

If all you want to do is install Kali on your ODROID-C1, follow these instructions:

1. Get a nice fast 8 GB micro SD card or eMMC.
2. Download the Kali ODROID-C1 image from our [downloads](https://www.offensive-security.com/kali-linux-arm-images/) area.
3. Use the **dd** utility to image this file to your microSD/eMMC device. In our example, we use a microSD which is located at **_/dev/sdb_**. **_Change this as needed._**

{{% notice info %}}
**Alert!** This process will wipe out your SD card. If you choose the wrong storage device, you may wipe out your computers hard disk.
{{% /notice %}}

```
xzcat kali-$version-odroidc.img.xz | dd of=/dev/sdb bs=512k
```

This process can take awhile depending on your device speed and image size.

Once the _dd_ operation is complete, boot up the ODROID-C1 with the SD / eMMC plugged in. Log in to Kali (**_root_** / **_toor_**), that's it, you're done!

## Kali on ODROID-C1 - Developer Instructions

If you are a developer and want to tinker with the Kali ODROID-C1 image, including changing the kernel configuration and generally being adventurous, check out the [kali-arm-build-scripts](https://gitlab.com/kalilinux/build-scripts/kali-arm) repository on GitHub, and follow the _README.md_ file's instructions.  The script to use is **odroid-c.sh**
