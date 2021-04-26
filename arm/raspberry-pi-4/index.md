---
title: Raspberry Pi 4
description:
icon:
type: post
weight:
author: ["steev",]
---

The [Raspberry Pi 4](https://www.raspberrypi.org/products/raspberry-pi-4-model-b/) has a quad core 1.5GHz processor, with 2GB, 4GB or 8GB of RAM, depending on model. Kali Linux runs on a microSD card.

By default, the Kali Linux Raspberry Pi 4 image contains the **kali-linux-default** metapackage similar to other ARM images. If you wish to install extra tools please refer to our [tools site](https://tools.kali.org/kali-metapackages).

{{% notice info %}}
The Raspberry Pi 4 has a 64-bit processor and can run 64-bit images.

Because it can run 64-bit images, you can choose either `Kali Linux RaspberryPi 2, 3, 4 and 400 (img.xz)` or `Kali Linux RaspberryPi 2 (v1.2), 3, 4 and 400 (64-Bit) (img.xz)` as the image to run, the latter being 64-bit.
<!-- @gg0tmi1k: it might be worth saying which one to pick if you don't know -->
{{% /notice %}}

{{% notice info %}}
The Raspberry Pi images use [Re4son](https://twitter.com/re4sonkernel)'s kernel, which includes the drivers for external Wi-Fi cards, TFT displays, and the [nexmon](https://github.com/seemoo-lab/nexmon) firmware for the built-in wireless card on the Raspberry Pi 3 and 4. You will not need to download it and install it, and doing so will likely be a downgrade over the current installed kernel.
{{% /notice %}}

## Kali on Raspberry Pi 4 - User Instructions

If you're unfamiliar with the details of [downloading and validating a Kali Linux image](/docs/introduction/download-official-kali-linux-images/), or for [using that image to create a bootable device](/docs/usb/live-usb-install-with-windows/), it's strongly recommended that you refer to the more detailed procedures described in the specific articles on those subjects.

To install a pre-built image of the standard build of Kali Linux on your Raspberry Pi 4, follow these instructions:

1. Get a fast microSD card with at least 16GB capacity. Class 10 cards are highly recommended.
2. Download _and validate_ our preferred `Kali Raspberry Pi 4` image from the [downloads](https://www.offensive-security.com/kali-linux-arm-images/) area. The process for validating an image is described in more detail in the article on [Downloading Kali Linux](/docs/introduction/download-official-kali-linux-images/).
3. Use the **dd** utility to image this file to your microSD card (same process as [making a Kali USB](/docs/usb/live-usb-install-with-windows/).

In our example, we assume the storage device is located at **_/dev/sdb_**. Do _not_ simply copy these value, **change this to the correct drive path**.

{{% notice info %}}
This process will wipe out your SD card. If you choose the wrong storage device, you may wipe out your computers hard disk.
{{% /notice %}}

```console
$ xzcat kali-linux-$version-rpi4-nexmon.img.xz | sudo dd of=/dev/sdb bs=4M status=progress
```

**or**

```console
$ xzcat kali-linux-$version-rpi4-nexmon-64.img.xz | sudo dd of=/dev/sdb bs=4M status=progress
```

This process can take a while, depending on your PC, your microSD card's speed, and the size of the Kali Linux image.

Once the _dd_ operation is complete, boot up the Raspberry Pi 4 with the microSD plugged in.

You should be able to [log in to Kali](/docs/introduction/default-credentials/).

## Kali on Raspberry Pi 4 - Tips and Tricks

The bluetooth service on the Raspberry Pi 4 needs a uart helper service before it works. To enable and start the bluetooth service run the following commands

```console
$ sudo systemctl enable --now uart.service
$ sudo systemctl enable --now bluetooth.service
```

By default, audio is routed via HDMI, so you won't hear audio via the 3.5mm audio jack. You can run the following command in order to redirect the output.

```console
$ sudo amixer -c 0 set numid=3 1
```

## Kali on Raspberry Pi 4 - Image Customization

If you want to customize the Kali Raspberry Pi 4 image, including changes to the [packages](https://www.kali.org/docs/general-use/metapackages/) being installed, changing the desktop environment, increasing or decreasing the image file size or generally being adventurous, check out the [kali-arm-build-scripts](https://gitlab.com/kalilinux/build-scripts/kali-arm) repository on GitLab, and follow the _README.md_ file's instructions. The script to use is **rpi3-nexmon.sh** (32-bit) or **rpi3-64.sh** (64-bit).
