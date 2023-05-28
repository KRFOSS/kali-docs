---
title: Raspberry Pi 1 (Original)
description:
icon:
weight:
author: ["steev",]
---

{{% notice info %}}
The early revisions of Raspberry Pi 1 (Original) boards have a full-size SD card slot, however later board revisions moved to a microSD card slot. We document using the full-size SD card, but the process is the same for microSD card.
{{% /notice %}}

The [Raspberry Pi 1](https://raspberrypi.org/) is a low-cost, credit-card-sized ARM computer. Despite being a being less powerful than a "standard" laptop or desktop PC, its affordability makes it an excellent option for a tiny Linux system. The Raspberry Pi 1 provides a full-size SD card slot for mass storage and will attempt to boot off that device when the board is powered on.

By default, the Kali Linux Raspberry Pi 1 image contains the [**kali-linux-default** metapackage](/docs/general-use/metapackages/) similar to most other platforms. If you wish to install extra tools please refer to our [metapackages page](/docs/general-use/metapackages/).

{{% notice info %}}
The Raspberry Pi 1 images use [Re4son](https://twitter.com/re4sonkernel)'s kernel, which includes the drivers for external Wi-Fi cards, TFT displays, and the [nexmon](https://github.com/seemoo-lab/nexmon) firmware for the built-in wireless card on the [Raspberry Pi 3](/docs/arm/raspberry-pi-3/) and [4](/docs/arm/raspberry-pi-4/). You will not need to download it and install it, and doing so will likely be a downgrade over the current installed kernel.
{{% /notice %}}

## Kali on Raspberry Pi 1 - User Instructions

If you're unfamiliar with the details of [downloading and validating a Kali Linux image](/docs/introduction/download-official-kali-linux-images/), or for [using that image to create a bootable device](/docs/usb/live-usb-install-with-windows/), it's strongly recommended that you refer to the more detailed procedures described in the specific articles on those subjects.

To install a pre-built image of the standard build of Kali Linux on your Raspberry Pi, the general process goes as follows:

1. Get a fast full-size SD card with at least 16GB capacity. Class 10 cards are highly recommended.
2. Download _and validate_ the Kali Linux Raspberry Pi 1 image from the [downloads](/get-kali/) area. The process for validating an image is described in more detail on [Downloading Kali Linux](/docs/introduction/download-official-kali-linux-images/).
3. Use the **[dd](https://manpages.debian.org/testing/coreutils/dd.1.en.html)** utility to image this file to your full-size SD card (same process as [making a Kali USB](/docs/usb/live-usb-install-with-windows/).

In our example, we assume the storage device is located at `/dev/sdb`. Do _not_ simply copy these value, **change this to the correct drive path**.

{{% notice info %}}
This process will wipe out your full-size SD card. If you choose the wrong storage device, you may wipe out your computers hard disk.
{{% /notice %}}

```console
$ xzcat kali-linux-2023.1-raspberry-pi1-armel.img.xz | sudo dd of=/dev/sdb bs=4M status=progres
```

This process can take a while, depending on your PC, your full-size SD card's speed, and the size of the Kali Linux image.

Once the _dd_ operation is complete, boot up the Raspberry Pi 1 with the full-size SD card plugged in.

You should be able to [log in to Kali](/docs/introduction/default-credentials/).

## Kali on the Raspberry Pi 1 - Tips

There is no wireless on the Raspberry Pi, so you will need to use an external device for wireless.

## Kali on Raspberry Pi 1 - Image Customization

If you want to customize the Kali Raspberry Pi 1 image, including changes to the [packages](/docs/general-use/metapackages/) being installed, changing the [desktop environment](/docs/general-use/switching-desktop-environments/), increasing or decreasing the image file size or generally being adventurous, check out the [Kali-ARM Build-Scripts](https://gitlab.com/kalilinux/build-scripts/kali-arm) repository on GitLab, and follow the _README.md_ file's instructions. The script to use is `raspberry-pi1.sh`.
