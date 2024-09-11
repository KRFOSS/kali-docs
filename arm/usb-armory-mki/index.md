---
title: USB Armory MKI
description:
icon:
weight:
author: ["steev",]
---

The [USB Armory MKI](https://inversepath.com/usbarmory_mark-one.html) from Inverse Path is an open source hardware design, implementing a flash drive sized computer. Kali Linux fits on a microSD card for it.

By default, the Kali Linux USB Armory MKI image **does not** contains the [**kali-linux-default** metapackage](/docs/general-use/metapackages/) which is often found in Kali platforms. If you wish to install extra tools please refer to our [metapackages page](/docs/general-use/metapackages/).

## Kali on USB Armory MKI - Build-Script Instructions

Kali does not provide pre-built images for download, but you can still generate one by cloning the [Kali-ARM Build-Scripts](https://gitlab.com/kalilinux/build-scripts/kali-arm) repository on GitLab, and follow the _README.md_ file's instructions. The script to use is `usb-armory-mki.sh`.

Once the build script finishes running, you will have an "img" file in the directory where you ran the script from. At that point, the instructions are the same as if you had downloaded a pre-built image.

The easiest way to generate these images is **from within a pre-existing Kali Linux environment**.

## Kali on USB Armory MKI - User Instructions

To install Kali on your USB Armory MKI, follow these instructions:

1. Get a fast microSD card with at least 16GB capacity. Class 10 cards are highly recommended.or USB drive.
2. Use the **[dd](https://manpages.debian.org/testing/coreutils/dd.1.en.html)** utility to image this file to your microSD card or USB drive. In our example, we use a microSD which is located at `/dev/sdX`. **_Change this as needed._**

{{% notice info %}}
This process will wipe out your microSD card. If you choose the wrong storage device, you may wipe out your computers hard disk.
{{% /notice %}}

```console
$ xzcat kali-linux-2024.3-usb-armory-mki-armhf.img.xz | sudo dd of=/dev/sdX bs=4M status=progress
```

This process can take a while, depending on your PC, your microSD card's speed, and the size of the Kali Linux image.

Once the _dd_ operation is complete, boot up the USB Armory MKI with the microSD card plugged in.

You should be able to [log in to Kali](/docs/introduction/default-credentials/).

## Kali on USB Armory MKI - Image Customization

If you want to customize the Kali USB Armory MKI image, including changes to the [packages](/docs/general-use/metapackages/) being installed, changing the [desktop environment](/docs/general-use/switching-desktop-environments/), increasing or decreasing the image file size or generally being adventurous, check out the [Kali-ARM Build-Scripts](https://gitlab.com/kalilinux/build-scripts/kali-arm) repository on GitLab, and follow the _README.md_ file's instructions. The script to use is `usb-armory-mki.sh`.
