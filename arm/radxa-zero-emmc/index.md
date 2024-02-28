---
title: Radxa Zero (eMMC)
description:
icon:
weight:
author: ["steev",]
---

The [Radxa Zero](https://wiki.radxa.com/Zero) has a quad core 1.8GHz, with 512MB, 1GB, 2GB, or 4GB of LPDDR4 RAM. There are multiple eMMC versions available, we recommend at least 32GB.

By default, the Kali Linux Radxa Zero image contains the [**kali-linux-default** metapackage](/docs/general-use/metapackages/) similar to most other platforms. If you wish to install extra tools please refer to our [metapackages page](/docs/general-use/metapackages/).

{{% notice info %}}
The Radxa Zero eMMC image only works on the 2GB and 4GB variants, as the 512MB and 1GB do not have eMMC.
{{% /notice %}}

<!-- 2022.2 didn't have an image, 2022.3 will 
## Kali on Radxa Zero - User Instructions

If you're unfamiliar with the details of [downloading and validating a Kali Linux image](/docs/introduction/download-official-kali-linux-images/), or for [using that image to create a bootable device](/docs/usb/live-usb-install-with-windows/), it's strongly recommended that you refer to the more detailed procedures described in the specific articles on those subjects.

To install a pre-built image of the standard build of Kali Linux on your Raspberry Pi Zero 2 W, follow these instructions:

1. Get a fast microSD card with at least 16GB capacity. Class 10 cards are highly recommended.
2. Download _and validate_ the `Kali Radxa Zero` image from the [downloads](/get-kali/) area. The process for validating an image is described in more detail on [Downloading Kali Linux](/docs/introduction/download-official-kali-linux-images/).
3. Use the **[dd](https://manpages.debian.org/testing/coreutils/dd.1.en.html)** utility to image this file to your microSD card (same process as [making a Kali USB](/docs/usb/live-usb-install-with-windows/).

In our example, we assume the storage device is located at `/dev/sdX`. Do _not_ simply copy these value, **change this to the correct drive path**.

{{% notice info %}}
This process will wipe out your microSD card. If you choose the wrong storage device, you may wipe out your computers hard disk.
{{% /notice %}}

```console
$ xzcat kali-linux-2024.1-radxa-zero-emmc-arm64.img.xz | sudo dd of=/dev/sdX bs=4M status=progress
```

This process can take a while, depending on your PC, your microSD card's speed, and the size of the Kali Linux image.

Once the _dd_ operation is complete, boot up the Radxa Zero with the microSD card plugged in.

You should be able to [log in to Kali](/docs/introduction/default-credentials/).

## Kali on Radxa Zero (sdcard) - Image Customization

If you want to customize the Kali Radxa Zero sdcard image, including changes to the [packages](/docs/general-use/metapackages/) being installed, changing the [desktop environment](/docs/general-use/switching-desktop-environments/), increasing or decreasing the image file size or generally being adventurous, check out the [Kali-ARM Build-Scripts](https://gitlab.com/kalilinux/build-scripts/kali-arm) repository on GitLab, and follow the _README.md_ file's instructions. The script to use is `radxa-zero-sdcard.sh`.
-->
## Kali on Radxa Zero (eMMC) - Build-Script Instructions

Kali does not provide pre-built images for download, but you can still generate one by cloning the [Kali-ARM Build-Scripts](https://gitlab.com/kalilinux/build-scripts/kali-arm) repository on GitLab, and follow the _README.md_ file's instructions. The script to use is `radxa-zero-emmc.sh`.

Once the build script finishes running, you will have an `img.xz` file in the `images` directory where you ran the script from. At that point, the instructions are the same as if you had downloaded a pre-built image.

The easiest way to generate these images is **from within a pre-existing Kali Linux environment**.

## Kali on Radxa Zero (eMMC) - User Instructions

The Radxa Zero requires entering maskrom mode in order to write the Kali Linux image to the eMMC.

To install Kali on your Radxa Zero (eMMC), follow these instructions:

{{% notice info %}}
This process will wipe out whatever is on your eMMC.
{{% /notice %}}

Windows:

<!-- TODO: Do installation in Windows and document it -->

Linux (maskrom mode):

We will need to download a few files from Radxa's website as well as install the Amlogic boot tool in order to write to the eMMC with the Radxa Zero in maskrom mode.

 - [radxa-zero-erase-emmc.bin](https://dl.radxa.com/zero/images/loader/radxa-zero-erase-emmc.bin) - automatically erases the eMMC, then presents the eMMC as a USB storage device.
 - [rz-udisk-loader.bin](https://dl.radxa.com/zero/images/loader/rz-udisk-loader.bin) - expose the eMMC device as a USB Mass Storage device.

1. sudo apt update
2. sudo apt install python3-pip
3. sudo pip3 install pyamlboot
4. connect the Radxa Zero to the computer in maskrom mode
  - Locate the USB boot button on the bottom of the Radxa Zero
  - Press the button and while holding it down, plug the Radxa Zero in to the computer
  - You can release the button when the power LED turns on
5. Verify that the computer sees the Radxa Zero in maskrom mode via `lsusb`
  - lsusb
```console
kali@kali:~$ lsusb | grep Amlogic
Bus 001 Device 048: ID 1b8e:c003 Amlogic, Inc. GX-CHIP
```
6. sudo boot-g12.py radxa-zero-erase-emmc.bin
7. Use the **[dd](https://manpages.debian.org/testing/coreutils/dd.1.en.html)** utility to image this file to the newly presented USB device (same process as [making a Kali USB](/docs/usb/live-usb-install-with-windows/).

In our example, we assume the storage device is located at `/dev/sdX`. Do _not_ simply copy these value, **change this to the correct device path**.

{{% notice info %}}
This process will wipe out your whatever is on your eMMC. If you choose the wrong storage device, you may wipe out your computers hard disk.
{{% /notice %}}

```console
$ xzcat kali-linux-2024.1-radxa-zero-emmc-arm64.img.xz | sudo dd of=/dev/sdX bs=4M status=progres
```

This process can take a while, depending on your PC, your Radxa Zero's storage, and the size of the Kali Linux image.

Once the _dd_ operation is complete, unplug the Radxa Zero from the computer, and boot up the Radxa Zero.

You should be able to [log in to Kali](/docs/introduction/default-credentials/).

Linux (sdcard boot, write to eMMC):

{{% notice info %}}
You cannot use the Radxa Zero **sdcard** image on the eMMC, and vice versa. The bootloader is written to different locations depending on eMMC and sdcard and they are **not** interchangeable.
{{% /notice %}}

With this mode, we first boot the Radxa Zero from a microSD card, and simply use the **[dd](https://manpages.debian.org/testing/coreutils/dd.1.en.html)** utility to write an image to the eMMC.

1. Follow the [Radxa Zero (sdcard)](/docs/arm/radxa-zero-sdcard/) instructions
2. Connect the Radxa Zero to a wireless network
3. Copy the image file you want to use to the microSD card. Keep in mind that you will need enough free space on the microSD card
4. Use the **[dd](https://manpages.debian.org/testing/coreutils/dd.1.en.html)** utility to image this file to the eMMC device (same process as [making a Kali USB](/docs/usb/live-usb-install-with-windows/).

In our example, we assume the storage device is located at `/dev/mmcblk0`. Do _not_ simply copy these value, **change this to the correct device path**.

{{% notice info %}}
This process will wipe out your whatever is on your eMMC. If you choose the wrong storage device, you may wipe out your computers hard disk.
{{% /notice %}}

```console
$ xzcat kali-linux-2024.1-radxa-zero-emmc-arm64.img.xz | sudo dd of=/dev/mmcblk0 bs=4M status=progres
```

This process can take a while, depending on your microSD card, your Radxa Zero's storage, and the size of the Kali Linux image.

Once the _dd_ operation is complete, power off the Radxa Zero, unplug the microSD card, and then boot up the Radxa Zero.

You should be able to [log in to Kali](/docs/introduction/default-credentials/).
