---
title: Raspberry Pi Zero W
description:
icon:
weight:
author: ["steev",]
---

{{% notice info %}}
The Raspberry Pi Zero W has `Raspberry Pi Zero W V1.1` printed on the **bottom** of the PCB.
{{% /notice %}}

The [Raspberry Pi Zero W](https://www.raspberrypi.org/products/raspberry-pi-zero-w-/) has a single core 1GHz, with 512MB of RAM. Kali Linux fits on an external microSD card. Unlike the [Raspberry Pi Zero](/docs/arm/raspberry-pi-zero/), the Raspberry Pi Zero W **has** *w*ireless networking on the board.

By default, the Kali Linux Raspberry Pi Zero W image contains the [**kali-linux-default** metapackage](/docs/general-use/metapackages/) similar to most other platforms. If you wish to install extra tools please refer to our [metapackages page](/docs/general-use/metapackages/).

{{% notice info %}}
The Raspberry Pi images use [Re4son](https://twitter.com/re4sonkernel)'s kernel, which includes the drivers for external Wi-Fi cards, TFT displays, and the [nexmon](https://github.com/seemoo-lab/nexmon) firmware for the built-in wireless card on the [Raspberry Pi 3](/docs/arm/raspberry-pi-3/) and [4](/docs/arm/raspberry-pi-4/). You will not need to download it and install it, and doing so will likely be a downgrade over the current installed kernel.
{{% /notice %}}

## Kali on Raspberry Pi Zero W - User Instructions

If you're unfamiliar with the details of [downloading and validating a Kali Linux image](/docs/introduction/download-official-kali-linux-images/), or for [using that image to create a bootable device](/docs/usb/live-usb-install-with-windows/), it's strongly recommended that you refer to the more detailed procedures described in the specific articles on those subjects.

To install a pre-built image of the standard build of Kali Linux on your Raspberry Pi Zero W, follow these instructions:

1. Get a fast microSD card with at least 16GB capacity. Class 10 cards are highly recommended.
2. Download _and validate_ the `Kali Raspberry Pi Zero/Zero W` image from the [downloads](/get-kali/) area. The process for validating an image is described in more detail on [Downloading Kali Linux](/docs/introduction/download-official-kali-linux-images/).
3. Use the **[dd](https://packages.debian.org/testing/dd)** utility to image this file to your microSD card (same process as [making a Kali USB](/docs/usb/live-usb-install-with-windows/).

In our example, we assume the storage device is located at `/dev/sdb`. Do _not_ simply copy these value, **change this to the correct drive path**.

{{% notice info %}}
This process will wipe out your microSD card. If you choose the wrong storage device, you may wipe out your computers hard disk.
{{% /notice %}}

```console
$ xzcat kali-linux-2022.3-raspberry-pi-zero-w-armel.img.xz | sudo dd of=/dev/sdb bs=4M status=progress
```

This process can take a while, depending on your PC, your microSD card's speed, and the size of the Kali Linux image.

Once the _dd_ operation is complete, boot up the Raspberry Pi Zero W with the microSD card plugged in.

You should be able to [log in to Kali](/docs/introduction/default-credentials/).

# Kali on Raspberry Pi Zero W Headless - Tips and Tricks

You can add a `wpa_supplicant.conf` file to the first partition of the microSD card to connect to a wireless network.

You can create this file on another Linux system by running `wpa_passphrase YOURNETWORK > wpa_supplicant.conf`.  It will prompt you for the wireless network's password.  You can add the password to the command as you run it, but keep in mind that if you do, your wifi network password will be in your user's shell history.

## Kali on Raspberry Pi Zero W - Image Customization

If you want to customize the Kali Raspberry Pi Zero W image, including changes to the [packages](/docs/general-use/metapackages/) being installed, changing the [desktop environment](/docs/general-use/switching-desktop-environments/), increasing or decreasing the image file size or generally being adventurous, check out the [Kali-ARM Build-Scripts](https://gitlab.com/kalilinux/build-scripts/kali-arm) repository on GitLab, and follow the _README.md_ file's instructions. The script to use is `raspberry-pi-zero-w.sh`.
