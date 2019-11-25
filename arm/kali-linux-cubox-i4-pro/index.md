---
title: CuBox-i4Pro
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

The [SolidRun CuBox-i4Pro](https://www.solid-run.com/product/cubox-i4pro/) is the "world's smallest computer".  The specifications are Quad core i.MX6 1GHZ processor, 2GB RAM, Gbit ethernet, eSata port, and MicroSD slot.

## Kali on Cubox-i4 Pro - User Instructions

If all you want to do is install Kali on your CuBox-i4Pro, follow these instructions:

1. Get a nice fast 8 GB micro SD card.
2. Download the Kali Cubox-i image from our [downloads](https://www.offensive-security.com/kali-linux-arm-images/) area.
3. Use the **dd** utility to image this file to your SD device. In our example, we use a MicroSD which is located at **_/dev/sdb_**. **_Change this as needed._**

{{% notice info %}}
This process will wipe out your SD card. If you choose the wrong storage device, you may wipe out your computers hard disk.
{{% /notice %}}

```
xzcat kali-$version-cubox-i.img.xz | dd of=/dev/sdb bs=512k
```

This process can take awhile depending on your device speed and image size.

Once the _dd_ operation is complete, boot up the CuBox-i4Pro with the MicroSD plugged in. Log in to Kali (**_root_** / **_toor_**), that's it, you're done!

## Kali on SolidRun Cubox-i4pro - Developer Instructions

If you are a developer and want to tinker with the Kali CuBox-i4Pro image, including changing the kernel configuration and generally being adventurous, check out the [kali-arm-build-scripts](https://gitlab.com/kalilinux/build-scripts/kali-arm) repository on GitHub, and follow the _README.md_ file's instructions. The script to use is **cubox-i.sh**
