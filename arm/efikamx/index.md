---
title: EfikaMX
description:
icon:
type: archived
weight:
author: ["steev",]
build-script: https://gitlab.com/kalilinux/build-scripts/kali-arm/-/blob/master/efikamx.sh
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

The EfikaMX is a low end, low cost ARM computer. Despite its less-than-stellar specifications, its affordability makes it an excellent option for a tiny Linux system.

## Kali on EfikaMX - User Instructions

If you're unfamiliar with the details of [downloading and validating a Kali Linux image](/docs/introduction/download-official-kali-linux-images/), or for [using that image to create a bootable device](/docs/usb/live-usb-install-with-windows/), it's strongly recommended that you refer to the more detailed procedures described in the specific articles on those subjects.

To install a pre-built image of the standard build of Kali Linux on your EfikaMX, follow these instructions:

1. Get a fast microSD card with at least 16GB capacity. Class 10 cards are highly recommended.
2. Download _and validate_ the `Kali EfikaMX` image from the [downloads](https://www.offensive-security.com/kali-linux-vmware-arm-image-download/) area. The process for validating an image is described in more detail on [Downloading Kali Linux](/docs/introduction/download-official-kali-linux-images/).
3. Use the **[dd](https://packages.debian.org/testing/dd)** utility to image this file to your microSD card (same process as [making a Kali USB](/docs/usb/live-usb-install-with-windows/).

In our example, we assume the storage device is located at `/dev/sdb`. Do _not_ simply copy these value, **change this to the correct drive path**.

{{% notice info %}}
**Alert!** This process will wipe out your microSD card. If you choose the wrong storage device, you may wipe out your computers hard disk.
{{% /notice %}}

```console
$ dd if=kali-linux-$version-efikamx.img of=/dev/sdb bs=4M
```

This process can take a while, depending on your PC, your microSD card's speed, and the size of the Kali Linux image.

Once the _dd_ operation is complete, insert the microSD card into the EfikaMX  and power it on.

You should be able to [log in to Kali](/docs/introduction/default-credentials/) and **startx**.

That's it, you're done!

## Kali on EfikaMX - Image Customization

If you want to customize the Kali BeagleBone Black image, including changes to the [packages](/docs/general-use/metapackages/) being installed, changing the [desktop environment](/docs/general-use/switching-desktop-environments/), increasing or decreasing the image file size or generally being adventurous, check out the [Kali-ARM Build-Scripts](https://gitlab.com/kalilinux/build-scripts/kali-arm) repository on GitLab, and follow the _README.md_ file's instructions. The script to use is `efikamx.sh`.
