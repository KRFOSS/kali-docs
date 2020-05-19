---
title: ASUS Chromebook Flip
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

The [ASUS Chromebook Flip](https://www.asus.com/us/Notebooks/ASUS_Chromebook_Flip_C100PA/) is a quad core 1.8GHz, with 2GB or 4GB of RAM Chromebook with a 10.1" 10 point multitouch touchscreen. Kali Linux fits on an external micro SD card or USB key.

## Kali on ASUS Chromebook Flip - User Instructions

If all you want to do is install Kali on your ASUS Chromebook Flip, follow these instructions:

1. Get a nice fast 8 GB micro SD card or USB key.
2. [Put your Chromebook in developer mode](http://www.chromium.org/chromium-os/developer-information-for-chrome-os-devices/acer-c720-chromebook), and enable USB boot.  You can ignore legacy boot on that page since these devices do not have SeaBIOS.
3. Download the Kali ASUS Chromebook Flip image from our [downloads](https://www.offensive-security.com/kali-linux-arm-images/) area.
4. Use the **dd** utility to image this file to your microSD card or USB key. In our example, we use a microSD which is located at _/dev/sdb_. **_Change this as needed._**

{{% notice info %}}
**Alert!** This process will wipe out your SD card/USB key. If you choose the wrong storage device, you may wipe out your computers hard disk.
{{% /notice %}}

```
xzcat kali-$version-veyron.img.xz | dd of=/dev/sdb bs=4M
```

This process can take awhile depending on your device speed and image size.

Once the _dd_ operation is complete, boot up the ASUS Chromebook Flip with the microSD/USB key plugged in. Log in to Kali (_**kali**_ / _**kali**_), that's it, you're done!

## Kali on ASUS Chromebook Flip - Developer Instructions

If you are a developer and want to tinker with the Kali ASUS Chromebook Flip image, including changing the kernel configuration and generally being adventurous, check out the [kali-arm-build-scripts](https://gitlab.com/kalilinux/build-scripts/kali-arm) repository on GitHub, and follow the _README.md_ file's instructions.  The script to use is **chromebook-arm-veyron.sh**
