---
title: ODROID U2
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

The [ODROID U2](http://www.hardkernel.com/main/products/prdt_info.php?g_code=G135341370451) is a tricky piece of hardware as console output is not a given. Ideally, when purchasing an ODROID, you should also get a USB UART cable, used for serial debugging of the boot process. Saying this, these machines are (at this time) some of the most impressive in terms of size, horsepower and memory availability.

{{% notice info %}}
The ODROID-U2 and ODROID-U3 hardware are based on the same basic platform, so the U2 image will work with the U3 without modification.
{{% /notice %}}

## Kali on ODROID U2 - User Instructions

If all you want to do is to install Kali on your awesome ODROID, follow these instructions:

1. Get a nice fast 8 GB (and above) microSD. Class 10 cards are highly recommended.
2. Download the Kali Linux ODROID U2 image from our [downloads](https://www.offensive-security.com/kali-linux-arm-images/) area.
3. Use the **dd** utility to image this file to your microSD card. In our example, we assume the storage device is located at **_/dev/sdb_**. **_Change this as needed._**

{{% notice info %}}
This process will wipe out your SD card. If you choose the wrong storage device, you may wipe out your computers hard disk.
{{% /notice %}}

```
dd if=kali-$vers-odroid.img of=/dev/sdb bs=4M
```

This process can take a while depending on your USB storage device speed and image size. Once the _dd_ operation is done,boot up the ODROID with the microSD plugged in. You should be welcomed with a Gnome login screen - (**_kali_** / **_kali_**). That's it, you're done!

### Troubleshooting

To troubleshoot the ODROID boot process, you will need to connect a UART serial cable to the ODROID. Once the cable is connected, you can issue the following command to connect to the console:

```
screen /dev/ttySAC1 115200
```

## Kali on ODROID U2 - Developer Instructions

If you are a developer and want to tinker with the Kali ODROID image, including changing the kernel configuration and generally being adventurous, check out the [kali-arm-build-scripts](https://gitlab.com/kalilinux/build-scripts/kali-arm) repository on GitHub, and follow the _README.md_ file's instructions. The script to use is **odroid-u2.sh**
