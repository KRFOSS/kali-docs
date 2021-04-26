---
title: Trimslice
description:
icon:
type: post
weight:
author: ["steev",]
build-script: https://gitlab.com/kalilinux/build-scripts/kali-arm/-/blob/master/trimslice.sh
headless:
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

The [Trimslice](http://www.compulab.co.il/utilite-computer/web/trim-slice) is a dual core 1GHz, with 1GB of RAM. Kali Linux fits on an external microSD card.

By default, the Kali Linux Trimslice image contains the [**kali-linux-default** metapackage](https://tools.kali.org/kali-metapackages) similar to most other platforms. If you wish to install extra tools please refer to our [metapackages page](/docs/general-use/metapackages/).

## Kali on Trimslice - User Instructions

If you're unfamiliar with the details of [downloading and validating a Kali Linux image](/docs/introduction/download-official-kali-linux-images/), or for [using that image to create a bootable device](/docs/usb/live-usb-install-with-windows/), it's strongly recommended that you refer to the more detailed procedures described in the specific articles on those subjects.

If to install Kali on your Trimslice, follow these instructions:

1. Get a fast microSD card or eMMC with at least 16GB capacity. Class 10 cards are highly recommended.
2. Download _and validate_ the `Kali Raspberry Trimslice` image from the [downloads](https://www.offensive-security.com/kali-linux-arm-images/) area. The process for validating an image is described in more detail on [Downloading Kali Linux](/docs/introduction/download-official-kali-linux-images/).
3. Use the **[dd](https://packages.debian.org/testing/dd)** utility to image this file to your microSD card (same process as [making a Kali USB](/docs/usb/live-usb-install-with-windows/).

In our example, we assume the storage device is located at `/dev/sdb`. Do _not_ simply copy these value, **change this to the correct drive path**.

{{% notice info %}}
This process will wipe out your microSD card or eMMC. If you choose the wrong storage device, you may wipe out your computers hard disk.
{{% /notice %}}

```console
$ xzcat kali-linux-$version-trimslice.img.xz | dd of=/dev/sdb bs=4M
```

This process can take a while, depending on your PC, your microSD/eMMC's speed, and the size of the Kali Linux image.

Once the _dd_ operation is complete, boot up the Trimslice with the microSD/eMMC plugged in.

[Log in to Kali](/docs/introduction/default-credentials/).

## Kali on Trimslice - Image Customization

If you want to customize the Kali Trimslice image, including changes to the [packages](/docs/general-use/metapackages/) being installed, changing the [desktop environment](/docs/general-use/switching-desktop-environments/), increasing or decreasing the image file size or generally being adventurous, check out the [Kali-ARM Build-Scripts](https://gitlab.com/kalilinux/build-scripts/kali-arm) repository on GitLab, and follow the _README.md_ file's instructions. The script to use is `trimslice.sh`.
