---
title: Raspberry Pi 2 [REV: v1.2]
description:
icon:
type: post
weight:
author: ["steev",]
build-script: https://gitlab.com/kalilinux/build-scripts/kali-arm/-/blob/master/rpi3-nexmon.sh
build-script: https://gitlab.com/kalilinux/build-scripts/kali-arm/-/blob/master/rpi3-64.sh
build-script: https://gitlab.com/kalilinux/build-scripts/kali-arm/-/blob/master/rpi3-64-lite.sh
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

The [Raspberry Pi 2 [REV: v1.2]](https://www.raspberrypi.org/products/raspberry-pi-2-model-b/) is a quad core 900MHz, with 1GB of RAM. Kali Linux fits on an external microSD card.
<!-- @gg0tmi1k: How do you know if its v1.2 or the other image? -->
<!-- @gg0tmi1k: Worth putting a link to the other image as well? -->

By default, the Kali Linux Raspberry Pi 2 [REV: v1.2] image contains the [**kali-linux-default** metapackage](https://tools.kali.org/kali-metapackages) similar to most other platforms. If you wish to install extra tools please refer to our [metapackages page](/docs/general-use/metapackages/).

{{% notice info %}}
The Raspberry Pi 2 [REV: v1.2] has a 64-bit processor and can run 64-bit images. This is the **only** revision of the Raspberry Pi 2 that can do so.

Because it can run 64-bit images, you can choose either `Kali Linux RaspberryPi 2, 3, 4 and 400 (img.xz)` or `Kali Linux RaspberryPi 2 (v1.2), 3, 4 and 400 (64-Bit) (img.xz)` as the image to run, the latter being 64-bit.
<!-- @gg0tmi1k: it might be worth saying which one to pick if you don't know -->
{{% /notice %}}

{{% notice info %}}
The Raspberry Pi images use [Re4son](https://twitter.com/re4sonkernel)'s kernel, which includes the drivers for external Wi-Fi cards, TFT displays, and the [nexmon](https://github.com/seemoo-lab/nexmon) firmware for the built-in wireless card on the [Raspberry Pi 3](/docs/arm/raspberry-pi-3/) and [4](/docs/arm/raspberry-pi-4/). You will not need to download it and install it, and doing so will likely be a downgrade over the current installed kernel.
{{% /notice %}}

## Kali on Raspberry Pi2 [REV: v1.2] - User Instructions

If you're unfamiliar with the details of [downloading and validating a Kali Linux image](/docs/introduction/download-official-kali-linux-images/), or for [using that image to create a bootable device](/docs/usb/live-usb-install-with-windows/), it's strongly recommended that you refer to the more detailed procedures described in the specific articles on those subjects.

To install a pre-built image of the standard build of Kali Linux on your Raspberry Pi 2, follow these instructions:

1. Get a fast microSD card with at least 16GB capacity. Class 10 cards are highly recommended.
2. Download _and validate_ our preferred `Kali Raspberry Pi 2` image from the [downloads](https://www.offensive-security.com/kali-linux-arm-images/) area. The process for validating an image is described in more detail on [Downloading Kali Linux](/docs/introduction/download-official-kali-linux-images/).
3. Use the **[dd](https://packages.debian.org/testing/dd)** utility to image this file to your microSD card (same process as [making a Kali USB](/docs/usb/live-usb-install-with-windows/).

In our example, we assume the storage device is located at `/dev/sdb`. Do _not_ simply copy these value, **change this to the correct drive path**.

{{% notice info %}}
This process will wipe out your microSD card. If you choose the wrong storage device, you may wipe out your computers hard disk.
{{% /notice %}}

```console
$ xzcat kali-linux-$version-rpi4-nexmon.img.xz | sudo dd of=/dev/sdb bs=4M status=progress
```

**or**

```console
$ xzcat kali-linux-$version-rpi4-nexmon-64.img.xz | sudo dd of=/dev/sdb bs=4M status=progress
```

This process can take a while, depending on your PC, your microSD card's speed, and the size of the Kali Linux image.

Once the _dd_ operation is complete, boot up the Raspberry Pi 2 with the microSD card plugged in.

You should be able to [log in to Kali](/docs/introduction/default-credentials/).

## Kali on Raspberry Pi 2 [REV: v1.2] - Tips

If you are on the 5.10 kernel, you can use mt76 chipset USB Wi-Fi devices, but they require creating a configuration file in `/etc/modprobe.d` with the following contents:

```console
# Load mt76usb without using scatter-gather which doesn't work on the RPi2 or RPi3 USB chipset
options mt76-usb disabe_usb_sg=1
```

## Kali on Raspberry Pi 2 [REV: v1.2] - Image Customization

If you want to customize the Kali Raspberry Pi 2 [REV: v1.2] image, including changes to the [packages](/docs/general-use/metapackages/) being installed, changing the [desktop environment](/docs/general-use/switching-desktop-environments/), increasing or decreasing the image file size or generally being adventurous, check out the [Kali-ARM Build-Scripts](https://gitlab.com/kalilinux/build-scripts/kali-arm) repository on GitLab, and follow the _README.md_ file's instructions. The script to use is `rpi3-nexmon.sh` (32-bit) or `rpi3-64.sh` (64-bit).
