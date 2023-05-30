---
title: Pinebook
description:
icon:
weight:
author: ["steev",]
---

The [Pinebook](https://www.pine64.org/pinebook/) has an Allwinner A64 Quad Core SOC with Mali 400 MP2 GPU, and 2GB LPDDR3 RAM. Kali Linux can run from either microSD card, or the internal eMMC.

By default, the Kali Linux Pinebook image contains the [**kali-linux-default** metapackage](/docs/general-use/metapackages/) similar to most other platforms. If you wish to install extra tools please refer to our [metapackages page](/docs/general-use/metapackages/).

{{% notice info %}}
With kernels newer than 5.19, the built-in Wi-Fi adapter no longer works. You will need to use an USB Wi-Fi adapter.
{{% /notice %}}

## Kali on the Pinebook microSD card - User Instructions

If you're unfamiliar with the details of [downloading and validating a Kali Linux image](/docs/introduction/download-official-kali-linux-images/), or for [using that image to create a bootable device](/docs/usb/live-usb-install-with-windows/), it's strongly recommended that you refer to the more detailed procedures described in the specific articles on those subjects.

To install a pre-built image of the standard build of Kali Linux on your Pinebook, follow these instructions:

1. Get a fast microSD card or eMMC module with at least 16GB capacity. Class 10 cards are highly recommended.
2. Download _and validate_ the `Kali Pinebook` image from the [downloads](/get-kali/) area. The process for validating an image is described in more detail on [Downloading Kali Linux](/docs/introduction/download-official-kali-linux-images/).
3. Use the **[dd](https://manpages.debian.org/testing/coreutils/dd.1.en.html)** utility to image this file to your microSD card (same process as [making a Kali USB](/docs/usb/live-usb-install-with-windows/).

In our example, we assume the storage device is located at `/dev/sdb`. Do _not_ simply copy these value, **change this to the correct drive path**.

{{% notice info %}}
This process will wipe out your microSD card. If you choose the wrong storage device, you may wipe out your computers hard disk or eMMC module.
{{% /notice %}}

```console
$ xzcat kali-linux-2023.2-pinebook-arm64.img.xz | sudo dd of=/dev/sdb bs=4M status=progress
```

This process can take a while, depending on your PC, your microSD card's speed, and the size of the Kali Linux image.

Once the _dd_ operation is complete, boot up the Pinebook with the microSD card plugged in.

You should be able to [log in to Kali](/docs/introduction/default-credentials/).

## Kali on the Pinebook eMMC - User Instructions

If you want to install Kali on your Pinebook's eMMC module, there are 2 different ways to do so.

If you have the [USB adapter for eMMC module](https://pine64.com/product/usb-adapter-for-emmc-module/?v=0446c16e2e66) then you can simply follow the same steps as you would for the microSD card.

If you do not have the USB adapter for eMMC module, you can use a bootable microSD card to write the Kali image to eMMC. The instructions are similar to the microSD card, and as with above, we need to make sure that we have the correct device. The easiest way to tell which device you want to use, is look in /dev at the `mmcblkX` devices. The device that has a `boot0` and `boot1` is the eMMC. For example, if `/dev/mmcblk1boot0` exists it would mean that we want to use `/dev/mmcblk1` as our device. One important difference is that we **do** need to include the number of the device, unlike above when using `sdb`.

{{% notice info %}}
This process will wipe out your eMMC module. If you choose the wrong storage device, you may wipe out your computers hard disk or microSD card.
{{% /notice %}}

```console
$ xzcat kali-linux-2023.2-pinebook-arm64.img.xz | sudo dd of=/dev/mmcblk1 bs=4M status=progress
```

This process can take a while, depending on your PC, your eMMC's speed, and the size of the Kali Linux image.

Once the _dd_ operation is complete, boot up the Pinebook with the eMMC plugged in.

You should be able to [log in to Kali](/docs/introduction/default-credentials/).

## Kali on the Pinebook - Image Customization

If you want to customize the Kali Pinebook image, including changes to the [packages](/docs/general-use/metapackages/) being installed, changing the [desktop environment](/docs/general-use/switching-desktop-environments/), increasing or decreasing the image file size or generally being adventurous, check out the [Kali-ARM Build-Scripts](https://gitlab.com/kalilinux/build-scripts/kali-arm) repository on GitLab, and follow the _README.md_ file's instructions. The script to use is `pinebook.sh`.
