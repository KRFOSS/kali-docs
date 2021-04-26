---
title: Trimslice
description:
icon:
type: post
weight:
author: ["steev",]
---

The [Trimslice](http://www.compulab.co.il/utilite-computer/web/trim-slice) is a dual core 1GHz, with 1GB of RAM. Kali Linux fits on an external micro SD card.

By default, the Kali Linux Trimslice image contains the **kali-linux-default** metapackage similar to other ARM images. If you wish to install extra tools please refer to our [tools site](https://tools.kali.org/kali-metapackages).

## Kali on Trimslice - User Instructions

If all you want to do is install Kali on your Trimslice, follow these instructions:

1. Get a nice fast 8 GB micro SD card or eMMC.
2. Download the Kali Trimslice image from our [downloads](https://www.offensive-security.com/kali-linux-arm-images/) area.
3. Use the **dd** utility to image this file to your microSD card. In our example, we use a microSD which is located at **_/dev/sdb_**. **_Change this as needed._**

{{% notice info %}}
This process will wipe out your SD card. If you choose the wrong storage device, you may wipe out your computers hard disk.
{{% /notice %}}

```console
$ xzcat kali-linux-$version-trimslice.img.xz | dd of=/dev/sdb bs=4M
```

This process can take awhile depending on your device speed and image size.

Once the _dd_ operation is complete, boot up the Trimslice with the microSD plugged in. [Log in to Kali](/docs/introduction/default-credentials/), that's it, you're done!

## Kali on Trimslice - Image Customization

If you want to customize the Kali Trimslice image, including changes to the [packages](https://www.kali.org/docs/general-use/metapackages/) being installed, changing the desktop environment, increasing or decreasing the image file size or generally being adventurous, check out the [kali-arm-build-scripts](https://gitlab.com/kalilinux/build-scripts/kali-arm) repository on GitLab, and follow the _README.md_ file's instructions. The script to use is **trimslice.sh**
