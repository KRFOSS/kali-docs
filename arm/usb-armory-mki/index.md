---
title: USB Armory MKI
description:
icon:
type: post
weight:
author: ["steev",]
---

The [USB Armory MKI](https://inversepath.com/usbarmory_mark-one.html) from Inverse Path is an open source hardware design, implementing a flash drive sized computer. Kali Linux fits on a microSD card for it.

By default, the Kali Linux Raspberry Pi 400 image contains the **kali-linux-default** metapackage similar to other ARM images. If you wish to install extra tools please refer to our [tools site](https://tools.kali.org/kali-metapackages).

## Kali on USB Armory MKI - User Instructions

Kali no longer provides pre-built images for download, but you can still generate one by cloning the [kali-arm-build-scripts](https://gitlab.com/kalilinux/build-scripts/kali-arm) repository on Gitlab, and follow the _README.md_ file's instructions. The script to use is **usbarmory.sh**.

Once the build script finishes running, you will have an img file in the directory where you ran the script from. At that point, the instructions are the same as if you had downloaded a pre-built image.

If all you want to do is install Kali on your SB Armory MKI, follow these instructions:

1. Get a fast microSD card with at least 16GB capacity. Class 10 cards are highly recommended.or USB key.
2. Use the **dd** utility to image this file to your microSD card or USB key. In our example, we use a microSD which is located at **_/dev/sdb_**. **_Change this as needed._**

{{% notice info %}}
This process will wipe out your SD card. If you choose the wrong storage device, you may wipe out your computers hard disk.
{{% /notice %}}

```console
$ xzcat kali-linux-$version-usbarmory.img.xz | sudo dd of=/dev/sdb bs=4M status=progress
```

This process can take a while, depending on your PC, your microSD card's speed, and the size of the Kali Linux image.

Once the _dd_ operation is complete, boot up a computer with the USB armory plugged in.

You should be able to [log in to Kali](/docs/introduction/default-credentials/).

## Kali on USB Armory MKI - Image Customization

If you want to customize the Kali USB Armory MKI image, including changes to the [packages](https://www.kali.org/docs/general-use/metapackages/) being installed, changing the desktop environment, increasing or decreasing the image file size or generally being adventurous, check out the [kali-arm-build-scripts](https://gitlab.com/kalilinux/build-scripts/kali-arm) repository on GitLab, and follow the _README.md_ file's instructions. The script to use is **usbarmory.sh**