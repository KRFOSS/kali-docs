---
title: Samsung Chromebook (daisy_snow)
description:
icon:
type: post
weight:
author: ["steev",]
build-script: https://gitlab.com/kalilinux/build-scripts/kali-arm/-/blob/master/chromebook-arm-exynos.sh
headless: kali-desktop-xfce
metapackage: kali-linux-default
status: build-scripts
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

{{% notice info %}}
The ChromiumOS code name for the Samsung Chromebook is **daisy_snow**. We use **exynos** for the script name as there are a number of Chromebooks with the Samsung Exynos processor in them that use the same kernel.
{{% /notice %}}

The Samsung ARM Chromebook is an ultraportable laptop. It was quite a challenge, but we have a Kali image that runs great on the Chromebook. Boasting an Exynos 5250 1.7GHz dual core processor and 2GB of RAM, the Chromebook is a fast ARM laptop. Kali Linux fits on an external full-size SD card on this machine which leaves the internal disk untouched.

By default, the Kali Linux Samsung Chromebook image contains the [**kali-linux-default** metapackage](https://tools.kali.org/kali-metapackages) similar to most other platforms. If you wish to install extra tools please refer to our [metapackages page](/docs/general-use/metapackages/).

## Kali on Samsung Chromebook - Build-Script Instructions

Kali does not provide pre-built images for download, but you can still generate one by cloning the [Kali-ARM Build-Scripts](https://gitlab.com/kalilinux/build-scripts/kali-arm) repository on GitLab, and follow the _README.md_ file's instructions. The script to use is `chromebook-arm-exynos.sh`.

Once the build script finishes running, you will have an "img" file in the directory where you ran the script from. At that point, the instructions are the same as if you had downloaded a pre-built image.

The easiest way to generate these images is **from within a pre-existing Kali Linux environment**.

## Kali on Samsung Chromebook - User Instructions

To install Kali on your Kali on your Samsung Chromebook, follow these instructions:

1. Get a fast microSD card or USB drive with at least 16GB capacity. Class 10 cards are highly recommended.
2. [Put your Chromebook in developer mode](http://www.chromium.org/chromium-os/developer-information-for-chrome-os-devices/acer-c720-chromebook), and enable USB boot.
3. Use the **[dd](https://packages.debian.org/testing/dd)** utility to image this file to your microSD card or USB drive (same process as [making a Kali USB](/docs/usb/live-usb-install-with-windows/).

In our example, we assume the storage device is located at `/dev/sdb`. Do _not_ simply copy these value, **change this to the correct drive path**.

{{% notice info %}}
This process will wipe out your microSD card or USB drive. If you choose the wrong storage device, you may wipe out your computers hard disk.
{{% /notice %}}

```console
$ xzcat kali-linux-$version-exynos.img.xz | sudo dd of=/dev/sdb bs=4M status=progress
```

This process can take a while, depending on your PC, your microSD card or USB drive speed, and the size of the Kali Linux image.

Once the _dd_ operation is complete, boot up the Samsung Chromebook with the microSD card or USB drive plugged in, and hit **CTRL+U** before the 30 second timeout.

You should be able to [log in to Kali](/docs/introduction/default-credentials/).
