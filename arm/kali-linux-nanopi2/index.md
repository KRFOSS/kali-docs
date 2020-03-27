---
title: NanoPi2
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

The [NanoPi2](http://nanopi.io/nanopi2.html) is a quad core 1.9GHz, with 1GB of RAM. Kali Linux fits on an external micro SD card.

## Kali on NanoPi2 - User Instructions
If all you want to do is install Kali on your NanoPi2, follow these instructions:

1. Get a nice fast 8 GB micro SD card or eMMC.
2. Download the Kali NanoPi2 image from our [downloads](https://www.offensive-security.com/kali-linux-arm-images/) area.
3. Use the **dd** utility to image this file to your microSD card. In our example, we use a microSD which is located at **_/dev/sdb_**. **_Change this as needed._**

{{% notice info %}}
This process will wipe out your SD card. If you choose the wrong storage device, you may wipe out your computers hard disk.
{{% /notice %}}

```
xzcat kali-$version-nanopi2.img.xz | dd of=/dev/sdb bs=4M
```

This process can take awhile depending on your device speed and image size.
Once the _dd_ operation is complete, boot up the NanoPi2 with the microSD  plugged in. Log in to Kali (**_root_** / **_toor_**), that's it, you're done!

## Kali on NanoPi2 - Developer Instructions

If you are a developer and want to tinker with the Kali NanoPi2 image, including changing the kernel configuration and generally being adventurous, check out the [kali-arm-build-scripts](https://gitlab.com/kalilinux/build-scripts/kali-arm) repository on GitHub, and follow the _README.md_ file's instructions.  The script to use is **nanopi2.sh**
