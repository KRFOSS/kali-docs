---
title: ODROID-C2
description:
icon:
type: post
weight:
author: ["steev",]
---

The [ODROID-C2](https://wiki.odroid.com/odroid-c2/odroid-c2) has an Amlogic S905, Quad Core Cortex™-A53 (ARMv8 64-bit) processor with Triple Core Mali-450 GPU and 2GB DDR3 (32-bit / 912Mhz) of RAM. Kali Linux can run from either an external microSD card, or an eMMC module.

By default, the Kali Linux ODROID-C2 image contains the **kali-linux-default** metapackage similar to other ARM images. If you wish to install extra tools please refer to our [tools site](https://tools.kali.org/kali-metapackages).

## Kali on ODROID-C2 microSD card - User Instructions

If you're unfamiliar with the details of [downloading and validating a Kali Linux image](/docs/introduction/download-official-kali-linux-images/), or for [using that image to create a bootable device](/docs/usb/live-usb-install-with-windows/), it's strongly recommended that you refer to the more detailed procedures described in the specific articles on those subjects.

To install a pre-built image of the standard build of Kali Linux on your ODROID-C2, follow these instructions:

1. Get a fast microSD or eMMC card with at least 16GB capacity. Class 10 cards are highly recommended.
2. Download _and validate_ the `Kali ODROID-C2` image from the [downloads](https://www.offensive-security.com/kali-linux-arm-images/) area. The process for validating an image is described in more detail in the article on [Downloading Kali Linux](/docs/introduction/download-official-kali-linux-images/).
3. Use the **dd** utility to image this file to your microSD card (same process as [making a Kali USB](/docs/usb/live-usb-install-with-windows/).

In our example, we assume the storage device is located at **_/dev/sdb_**. Do _not_ simply copy these value, **change this to the correct drive path**.

{{% notice info %}}
This process will wipe out your SD card. If you choose the wrong storage device, you may wipe out your computers hard disk.
{{% /notice %}}

```console
$ xzcat kali-linux-$version-odroidc2.img.xz | sudo dd of=/dev/sdb bs=4M status=progress
```

This process can take a while, depending on your PC, your microSD card's speed, and the size of the Kali Linux image.

Once the _dd_ operation is complete, boot up the ODROID-C2 with the microSD plugged in.

You should be able to [log in to Kali](/docs/introduction/default-credentials/).

## Kali on the ODROID-C2 eMMC - User Instructions

If you want to install Kali on your ODROID-C2's eMMC, there are 2 different ways to do so.

If you have the [USB Adapter for EMMC Module](https://www.hardkernel.com/shop/usb3-0-emmc-module-writer/) then you can simply follow the same steps as you would for the microSD card.

{{% notice info %}}
The eMMC modules and USB Adapter for EMMC Module on the Pine64 devices and ODROID devices can be used interchangeably.
{{% /notice %}}

If you do not have the USB Adapter for EMMC Module, you can use a bootable microSD card to write the Kali image to eMMC. The instructions are similar to the microSD card, and as with above, we need to make sure that we have the correct device. The easiest way to tell which device you want to use, is look in /dev at the `mmcblkX` devices. The device that has a `boot0` and `boot1` is the eMMC. For example, if `/dev/mmcblk1boot0` exists it would mean that we want to use `/dev/mmcblk1` as our device. One important difference is that we **do** need to include the number of the device, unlike above when using `sdb`.

{{% notice info %}}
This process will wipe out your eMMC. If you choose the wrong storage device, you may wipe out your microSD card.
{{% /notice %}}

```console
$ xzcat kali-linux-$version-odroidc2.img.xz | sudo dd of=/dev/mmcblk1 bs=4M status=progress
```

## Kali on the ODROID-C2 - Tips

The bootloader on the ODROID-C2 is u-boot, and in order to make changes to the kernel command line, the file to edit is `/etc/default/u-boot` and the option is `U_BOOT_PARAMETERS`. If you make any modifications to this file, you will want to then run `u-boot-update`.

USB on the ODROID-C2 will autosuspend if there is nothing plugged in to a USB port at boot time, so one possible change you might want to add is `usbcore.autosuspend=-1` if you want to plug in a USB device **after** the ODROID-C2 has booted.

If both a microSD card and an eMMC are plugged in, the ODROID-C2 will attempt to boot from the microSD card first.

## Kali on ODROID-C2 - Image Customization

If you want to customize the Kali ODROID-C2 image, including changes to the [packages](https://www.kali.org/docs/general-use/metapackages/) being installed, changing the desktop environment, increasing or decreasing the image file size or generally being adventurous, check out the [kali-arm-build-scripts](https://gitlab.com/kalilinux/build-scripts/kali-arm) repository on GitLab, and follow the _README.md_ file's instructions. The script to use is **odroid-c2.sh**
