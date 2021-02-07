---
title: Utilite Pro
description:
icon:
type: archived
weight:
author: ["steev",]
---

The [Utilite Pro](http://www.compulab.co.il/utilite-computer/web/utilite-overview) is a quad core 1.2GHz Cortex A9, with 2GB of RAM. Kali Linux fits on an external micro SD card.

## Kali on Utilite Pro - User Instructions

If all you want to do is install Kali on your Utilite Pro, follow these instructions:

1. Get a nice fast 8 GB micro SD card or eMMC.
2. Download the Kali Utilite image from our [downloads](https://www.offensive-security.com/kali-linux-arm-images/) area.
3. Use the **dd** utility to image this file to your microSD card. In our example, we use a microSD which is located at **_/dev/sdb_**. **_Change this as needed._**

{{% notice info %}}
**Alert!** This process will wipe out your SD card. If you choose the wrong storage device, you may wipe out your computers hard disk.
{{% /notice %}}

```console
$ xzcat kali-linux-$version-utilite.img.xz | dd of=/dev/sdb bs=4M
```

This process can take awhile depending on your device speed and image size.

Once the _dd_ operation is complete, boot up the Utilite Pro with the microSD plugged in. [Log in to Kali](/docs/introduction/default-credentials/), that's it, you're done!

## Kali on Utilite - Developer Instructions

If you are a developer and want to tinker with the Kali Utilite Pro image, including changing the kernel configuration and generally being adventurous, check out the [kali-arm-build-scripts](https://gitlab.com/kalilinux/build-scripts/kali-arm) repository on GitHub, and follow the _README.md_ file's instructions. The script to use is **utilite.sh**
