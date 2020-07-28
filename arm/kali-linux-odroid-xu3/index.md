---
title: ODROID-XU3
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

The [ODROID-XU3](http://www.hardkernel.com/main/products/prdt_info.php?g_code=g140448267127) is an octacore development board. Boasting 4 A15 cores and 4 A7 cores and 4 GB of RAM, the ODROID-XU3 is a fast ARM device. Kali Linux fits on an external micro SD card or on an eMMC module.

## Kali on ODROID-XU3 - User Instructions

If all you want to do is install Kali on your ODROID-XU3, follow these instructions:

1. Get a nice fast 8 GB micro SD card or eMMC.
2. Download the Kali ODROID-XU3 image from our [downloads](https://www.offensive-security.com/kali-linux-arm-images/) area.
3. Use the **dd** utility to image this file to your microSD/eMMC device. In our example, we use a microSD which is located at **_/dev/sdb_**. **_Change this as needed._**

{{% notice info %}}
This process will wipe out your SD card. If you choose the wrong storage device, you may wipe out your computers hard disk.
{{% /notice %}}

```
xzcat kali-$version-odroidxu3.img.xz | dd of=/dev/sdb bs=4M
```

This process can take awhile depending on your device speed and image size.

Once the _dd_ operation is complete, boot up the ODROID-XU3 with the SD / eMMC plugged in. Log in to Kali (**_kali_** / **_kali_**), that's it, you're done!

## Kali on ODROID-XU3 - Developer Instructions

If you are a developer and want to tinker with the Kali ODROID-XU3 image, including changing the kernel configuration and generally being adventurous, check out the [kali-arm-build-scripts](https://gitlab.com/kalilinux/build-scripts/kali-arm) repository on GitHub, and follow the _README.md_ file's instructions.  The script to use is **odroid-xu3.sh**
