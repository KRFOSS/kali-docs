---
title: Acer Tegra Chromebook 13" (Nyan)
description:
icon:
weight:
author: ["steev",]
build-script: https://gitlab.com/kalilinux/build-scripts/kali-arm/-/blob/master/chromebook-arm-nyan.sh
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

The Acer Tegra Chromebook is an ultraportable laptop. It was quite a challenge, but we have a Kali image that runs great on Acer Tegra Chromebook. Boasting a Tegra K1 2.1GHz quad core processor and 4GB of RAM, the Chromebook is a fast ARM laptop. Kali Linux fits on an external full-size SD card on this machine which leaves the internal disk untouched.

By default, the Kali Linux Acer Tegra Chromebook 13" image contains the [**kali-linux-default** metapackage](/docs/general-use/metapackages/) similar to most other platforms. If you wish to install extra tools please refer to our [metapackages page](/docs/general-use/metapackages/).

## Kali on Acer Tegra Chromebook - Build-Script Instructions

Kali does not provide pre-built images for download, but you can still generate one by cloning the [Kali-ARM Build-Scripts](https://gitlab.com/kalilinux/build-scripts/kali-arm) repository on GitLab, and follow the _README.md_ file's instructions. The script to use is `chromebook-arm-nyan.sh`.

Once the build script finishes running, you will have an "img" file in the directory where you ran the script from. At that point, the instructions are the same as if you had downloaded a pre-built image.

The easiest way to generate these images is **from within a pre-existing Kali Linux environment**.

## Kali on Acer Tegra Chromebook - User Instructions

To install Kali on your Acer Tegra Chromebook 13", follow these instructions:

1. Get a fast full-size SD card or USB drive with at least 16GB capacity. Class 10 cards are highly recommended.
2. [Put your Chromebook in developer mode](http://www.chromium.org/chromium-os/developer-information-for-chrome-os-devices/acer-c720-chromebook), and enable USB boot.
3. Use the **[dd](https://packages.debian.org/testing/dd)** utility to image this file to your full-size SD card (same process as [making a Kali USB](/docs/usb/live-usb-install-with-windows/).

In our example, we assume the storage device is located at `/dev/sdb`. Do _not_ simply copy these value, **change this to the correct drive path**.

{{% notice info %}}
This process will wipe out your full-size SD card or USB drive. If you choose the wrong storage device, you may wipe out your computers hard disk.
{{% /notice %}}

```console
$ xzcat kali-linux-2021.3-nyan.img.xz | sudo dd of=/dev/sdb bs=4M status=progress
```

This process can take a while, depending on your PC, your full-size SD card or USB drive speed, and the size of the Kali Linux image.

Once the _dd_ operation is complete, boot up the Acer Tegra Chromebook with the full-size SD card or USB drive plugged in, and hit **CTRL+U** before the 30 second timeout.

You should be able to [log in to Kali](/docs/introduction/default-credentials/).
