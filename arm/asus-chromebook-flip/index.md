---
title: ASUS Chromebook Flip (Veyron)
description:
icon:
weight:
author: ["steev",]
build-script: https://gitlab.com/kalilinux/build-scripts/kali-arm/-/blob/master/chromebook-arm-veyron.sh
headless: kali-desktop-xfce
metapackage: kali-linux-default
status: build-scripts
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

The [ASUS Chromebook Flip](https://www.asus.com/us/Notebooks/ASUS_Chromebook_Flip_C100PA/) is a quad core 1.8GHz, with 2GB or 4GB of RAM Chromebook with a 10.1" 10 point mult-touch touchscreen. Kali Linux fits on an external microSD card or USB drive.

By default, the Kali Linux ASUS Chromebook Flip image contains the [**kali-linux-default** metapackage](https://www.kali.org/docs/general-use/metapackages/) similar to most other platforms. If you wish to install extra tools please refer to our [metapackages page](/docs/general-use/metapackages/).

## Kali on ASUS Chromebook Flip - Build-Script Instructions

Kali does not provide pre-built images for download, but you can still generate one by cloning the [Kali-ARM Build-Scripts](https://gitlab.com/kalilinux/build-scripts/kali-arm) repository on GitLab, and follow the _README.md_ file's instructions. The script to use is `chromebook-arm-veyron.sh`.

Once the build script finishes running, you will have an "img" file in the directory where you ran the script from. At that point, the instructions are the same as if you had downloaded a pre-built image.

The easiest way to generate these images is **from within a pre-existing Kali Linux environment**.

## Kali on ASUS Chromebook Flip - User Instructions

To install Kali on your ASUS Chromebook Flip, follow these instructions:

1. Get a fast microSD card or USB drive with at least 16GB capacity. Class 10 cards are highly recommended.
2. Use the **[dd](https://packages.debian.org/testing/dd)** utility to image this file to your microSD card (same process as [making a Kali USB](/docs/usb/live-usb-install-with-windows/).

In our example, we assume the storage device is located at `/dev/sdb`. Do _not_ simply copy these value, **change this to the correct drive path**.

{{% notice info %}}
This process will wipe out your microSD card or USB drive. If you choose the wrong storage device, you may wipe out your computers hard disk.
{{% /notice %}}

```console
$ xzcat kali-linux-2021.2-veyron.img.xz | sudo dd of=/dev/sdb bs=4M status=progress
```

This process can take a while, depending on your PC, your microSD card or USB drive speed, and the size of the Kali Linux image.

Once the _dd_ operation is complete, boot up the ASUS Chromebook Flip with the microSD card or USB drive plugged in, and hit **CTRL+U** before the 30 second timeout.

You should be able to [log in to Kali](/docs/introduction/default-credentials/).

## Kali on ASUS Chromebook Flip - Image Customization

If you want to customize the Kali ASUS Chromebook Flip image, including changes to the [packages](/docs/general-use/metapackages/) being installed, changing the [desktop environment](/docs/general-use/switching-desktop-environments/), increasing or decreasing the image file size or generally being adventurous, check out the [Kali-ARM Build-Scripts](https://gitlab.com/kalilinux/build-scripts/kali-arm) repository on GitLab, and follow the _README.md_ file's instructions. The script to use is `chromebook-arm-veyron.sh`.
