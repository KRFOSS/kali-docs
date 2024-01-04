---
title: Raspberry Pi 3
description:
icon:
weight:
author: ["steev",]
---

The [Raspberry Pi 3](https://www.raspberrypi.org/products/raspberry-pi-3-model-b/) has a quad core 1.2GHz processor, with 1GB of RAM. Kali Linux fits on an external microSD card.

By default, the Kali Linux Raspberry Pi 3 image contains the [**kali-linux-default** metapackage](/docs/general-use/metapackages/) similar to most other platforms. If you wish to install extra tools please refer to our [metapackages page](/docs/general-use/metapackages/).

{{% notice info %}}
The Raspberry Pi 3 has a 64-bit processor and can run 64-bit images.
Because it can run 64-bit images, you can choose either `Kali Linux Raspberry Pi 2, 3, 4 and 400 (32-bit) (img.xz)` or `Kali Linux Raspberry Pi 2 (v1.2), 3, 4 and 400 (64-bit) (img.xz)` as the image to run, the latter being 64-bit.<br />
<br />
We recommend using the 32-bit image on Raspberry Pi devices as that gets far more testing, and a lot of documentation out there expects you to be running RaspberryPi OS which is 32-bit.
{{% /notice %}}

{{% notice info %}}
The Raspberry Pi images use [Re4son](https://twitter.com/re4sonkernel)'s kernel, which includes the drivers for external Wi-Fi cards, TFT displays, and the [nexmon](https://github.com/seemoo-lab/nexmon) firmware for the built-in wireless card on the [Raspberry Pi 3](/docs/arm/raspberry-pi-3/) and [4](/docs/arm/raspberry-pi-4/). You will not need to download it and install it, and doing so will likely be a downgrade over the current installed kernel.
{{% /notice %}}

## Kali on Raspberry Pi 3 - User Instructions

If you're unfamiliar with the details of [downloading and validating a Kali Linux image](/docs/introduction/download-official-kali-linux-images/), or for [using that image to create a bootable device](/docs/usb/live-usb-install-with-windows/), it's strongly recommended that you refer to the more detailed procedures described in the specific articles on those subjects.

To install a pre-built image of the standard build of Kali Linux on your Raspberry Pi 3, follow these instructions:

1. Get a fast microSD card with at least 16GB capacity. Class 10 cards are highly recommended.
2. Download _and validate_ our preferred `Kali Raspberry Pi 3` image from the [downloads](/get-kali/) area. The process for validating an image is described in more detail on [Downloading Kali Linux](/docs/introduction/download-official-kali-linux-images/).
3. Use the **[dd](https://manpages.debian.org/buster/coreutils/dd.1.en.html)** utility to image this file to your microSD card (same process as [making a Kali USB](/docs/usb/live-usb-install-with-windows/).

In our example, we assume the storage device is located at `/dev/sdX`. Do _not_ simply copy these value, **change this to the correct drive path**.

{{% notice info %}}
This process will wipe out your microSD card. If you choose the wrong storage device, you may wipe out your computers hard disk.
{{% /notice %}}

```console
$ xzcat kali-linux-2023.4-raspberry-pi-armhf.img.xz | sudo dd of=/dev/sdX bs=4M status=progress
```

**or**

```console
$ xzcat kali-linux-2023.4-raspberry-pi-arm64.img.xz | sudo dd of=/dev/sdX bs=4M status=progress
```

This process can take a while, depending on your PC, your microSD's speed, and the size of the Kali Linux image.

Once the _dd_ operation is complete, boot up the Raspberry Pi 3 with the microSD plugged in.

You should be able to [log in to Kali](/docs/introduction/default-credentials/).

## Kali on Raspberry Pi 3 - Tips and Tricks

The bluetooth service on the Raspberry Pi 3 needs a **uart helper service** before it works. To enable and start the bluetooth service run the following commands:

```console
kali@kali:~$ sudo systemctl enable --now hciuart.service
kali@kali:~$ sudo systemctl enable --now bluetooth.service
```

If you are on the 5.10 or higher kernel, you can use mt76 chipset USB Wi-Fi devices, but they require creating a configuration file in `/etc/modprobe.d` with the following contents:

```plaintext
# Load mt76usb without using scatter-gather which doesn't work on the RPi2 or RPi3 USB chipset
options mt76-usb disable_usb_sg=1
```

## Kali on Raspberry Pi 3 Headless - Tips and Tricks

You can add a `wpa_supplicant.conf` file to the first partition of the microSD card to connect to a wireless network.

You can create this file on another Linux system by running `wpa_passphrase YOURNETWORK > wpa_supplicant.conf`. It will prompt you for the wireless network's password. You can add the password to the command as you run it, but keep in mind that if you do, your wifi network password will be in your user's shell history.

## Kali on Raspberry Pi 3 - Image Customization

If you want to customize the Kali Raspberry Pi 3 image, including changes to the [packages](/docs/general-use/metapackages/) being installed, changing the [desktop environment](/docs/general-use/switching-desktop-environments/), increasing or decreasing the image file size or generally being adventurous, check out the [Kali-ARM Build-Scripts](https://gitlab.com/kalilinux/build-scripts/kali-arm) repository on GitLab, and follow the _README.md_ file's instructions. The script to use is `raspberry-pi.sh` (32-bit) or `raspberry-pi-64-bit.sh` (64-bit).
