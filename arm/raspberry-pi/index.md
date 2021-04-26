---
title: Raspberry Pi
description:
icon:
type: post
weight:
author: ["steev",]
---

The [Raspberry Pi](https://raspberrypi.org/) is a low-cost, credit-card-sized ARM computer. Despite being a good bit less powerful than a laptop or desktop PC, its affordability makes it an excellent option for a tiny Linux system and it can do far more than act as a media hub.

The Raspberry Pi provides a SD card slot for mass storage and will attempt to boot off that device when the board is powered on.

By default, the Kali Linux Raspberry Pi image contains the **kali-linux-default** metapackage similar to other ARM images. If you wish to install extra tools please refer to our [tools site](https://tools.kali.org/kali-metapackages).

{{% notice info %}}
The Raspberry Pi images use [Re4son](https://twitter.com/re4sonkernel)'s kernel, which includes the drivers for external Wi-Fi cards, TFT displays, and the [nexmon](https://github.com/seemoo-lab/nexmon) firmware for the built-in wireless card on the Raspberry Pi 3 and 4. You will not need to download it and install it, and doing so will likely be a downgrade over the current installed kernel.
{{% /notice %}}

## Kali Linux on Raspberry Pi — User Instructions

If you're unfamiliar with the details of [downloading and validating a Kali Linux image](/docs/introduction/download-official-kali-linux-images/), or for [using that image to create a bootable device](/docs/usb/live-usb-install-with-windows/), it's strongly recommended that you refer to the more detailed procedures described in the specific articles on those subjects.

To install a pre-built image of the standard build of Kali Linux on your Raspberry Pi, the general process goes as follows:

1. Get a fast SD card with at least 16GB capacity. Class 10 cards are highly recommended.
2. Download _and validate_ the Kali Linux Raspberry Pi image from the [downloads](https://www.offensive-security.com/kali-linux-arm-images/) area. The process for validating an image is described in more detail in the article on [Downloading Kali Linux](/docs/introduction/download-official-kali-linux-images/).
3. Use the **dd** utility to image this file to your microSD card (same process as [making a Kali USB](/docs/usb/live-usb-install-with-windows/).

In our example, we assume the storage device is located at **_/dev/sdb_**. Do _not_ simply copy these value, **change this to the correct drive path**.

{{% notice info %}}
This command will overwrite any existing data on your SD card. If you specify the _wrong device path_, you could wipe out your computer's hard disk!
{{% /notice %}}

```console
$ xzcat kali-linux-$version-rpi.img.xz | sudo dd of=/dev/sdb bs=4M status=progress
```

This process can take a while, depending on your PC, your microSD card's speed, and the size of the Kali Linux image.

Once the _dd_ operation is complete, boot up the Raspberry Pi with the microSD plugged in.

You should be able to [log in to Kali](/docs/introduction/default-credentials/).

## Kali Linux on the Raspberry Pi - Tips

There is no wireless on the Raspberry Pi, so you will need to use an external device for wireless!

## Kali Linux on the Raspberry Pi - Other uses

We love seeing users come up with their own images and sharing them. As an example, there's a badass user-created project running Kali on a Pi 3, a touch interface and mounted on a friggin DRONE [here](https://whitedome.com.au/re4son/sticky-fingers-kali-pi/).

## Kali Linux on Raspberry Pi — Custom Build

If you want to customize the Kali Raspberry Pi image, including changes to the [packages](https://www.kali.org/docs/general-use/metapackages/) being installed, changing the desktop environment, increasing or decreasing the image file size or generally being adventurous, check out the [kali-arm-build-scripts](https://gitlab.com/kalilinux/build-scripts/kali-arm) repository on GitLab, and follow the _README.md_ file's instructions. The script to use is **rpi.sh**
