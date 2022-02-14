---
title: USB Armory MKII
description:
icon:
weight:
author: ["steev",]
build-script: https://gitlab.com/kalilinux/build-scripts/kali-arm/-/blob/master/usb-armory-mkii.sh
headless: kali-desktop-xfce
metapackage: N/A
status: pre-generated
cpu:
gpu:
ram:
ethernet:
wifi:
bluetooth:
usb3:
usb2:
storage:
---

The [USB Armory MKII](https://inversepath.com/usbarmory) from Inverse Path is an open source hardware design, implementing a flash drive sized computer. Kali Linux fits on a microSD card for it.

By default, the Kali Linux USB Armory MKII image **does not** contains the [**kali-linux-default** metapackage](/docs/general-use/metapackages/) which is often found in Kali platforms. If you wish to install extra tools please refer to our [metapackages page](/docs/general-use/metapackages/).

## Kali on USB Armory MKII - User Instructions

If you're unfamiliar with the details of [downloading and validating a Kali Linux image](/docs/introduction/download-official-kali-linux-images/), or for [using that image to create a bootable device](/docs/usb/live-usb-install-with-windows/), it's strongly recommended that you refer to the more detailed procedures described in the specific articles on those subjects.

To install a pre-built image of the standard build of Kali Linux on your USB Armory MKII, follow these instructions:

1. Get a fast microSD card with at least 16GB capacity. Class 10 cards are highly recommended.
2. Download _and validate_ the `Kali USB Armory MKII` image from the [downloads](https://www.offensive-security.com/kali-linux-arm-images/) area. The process for validating an image is described in more detail on [Downloading Kali Linux](/docs/introduction/download-official-kali-linux-images/).
3. Use the **[dd](https://packages.debian.org/testing/dd)** utility to image this file to your microSD card. In our example, we use a microSD which is located at `/dev/sdb`. **_Change this as needed._**

{{% notice info %}}
This process will wipe out your microSD card. If you choose the wrong storage device, you may wipe out your computers hard disk.
{{% /notice %}}

```console
$ xzcat kali-linux-2022.1-usb-armory-mkii-xfce-armhf.img.xz | sudo dd of=/dev/sdb bs=4M status=progress
```

This process can take a while, depending on your PC, your microSD card's speed, and the size of the Kali Linux image.

Once the _dd_ operation is complete, boot up a computer with the USB armory plugged in.

You should be able to [log in to Kali](/docs/introduction/default-credentials/).

## Kali on USB Armory MKII - Image Customization

If you want to customize the Kali USB Armory MKII image, including changes to the [packages](/docs/general-use/metapackages/) being installed, changing the [desktop environment](/docs/general-use/switching-desktop-environments/), increasing or decreasing the image file size or generally being adventurous, check out the [Kali-ARM Build-Scripts](https://gitlab.com/kalilinux/build-scripts/kali-arm) repository on GitLab, and follow the _README.md_ file's instructions. The script to use is `usb-armory-mkii.sh`.
