---
title: MCIMX6ULL-EVK
description:
icon:
type: post
weight:
author: ["steev",]
build-script: https://gitlab.com/kalilinux/build-scripts/kali-arm/-/blob/master/i.mx-6ull-evk.sh
headless:
metapackage: kali-linux-default
status: community
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

<!-- @steev: TODO: This is a community contributed image, so we don't know that much about it, nor do we test it. -->

The [MCIMX6ULL-EVK](https://www.nxp.com/design/development-boards/i-mx-evaluation-and-development-boards/evaluation-kit-for-the-i-mx-6ull-and-6ulz-applications-processor:MCIMX6ULL-EVK) is a market-focused development tool based on the i.MX 6ULL applications processor. Kali Linux fits on a microSD card for it.

By default, the Kali Linux MCIMX6ULL-EVK image contains the [**kali-linux-default** metapackage](https://tools.kali.org/kali-metapackages) similar to most other platforms. If you wish to install extra tools please refer to our [metapackages page](/docs/general-use/metapackages/).

## Kali on the MCIMX6ULL-EVK - Build-Script Instructions

Kali does not provide pre-built images for download, but you can still generate one by cloning the [Kali-ARM Build-Scripts](https://gitlab.com/kalilinux/build-scripts/kali-arm) repository on GitLab, and follow the _README.md_ file's instructions. The script to use is `i.mx-6ull-evk`.

Once the build script finishes running, you will have an "img" file in the directory where you ran the script from. At that point, the instructions are the same as if you had downloaded a pre-built image.

The easiest way to generate these images is **from within a pre-existing Kali Linux environment**.

1. Get a fast microSD card with at least 16GB capacity. Class 10 cards are highly recommended.
2. Use the **[dd](https://packages.debian.org/testing/dd)** utility to image this file to your full-size SD card (same process as [making a Kali USB](/docs/usb/live-usb-install-with-windows/).

In our example, we assume the storage device is located at `/dev/sdb`. Do _not_ simply copy these value, **change this to the correct drive path**.

{{% notice info %}}
This process will wipe out your microSD card. If you choose the wrong storage device, you may wipe out your computers hard disk.
{{% /notice %}}

```console
$ xzcat kali-linux-$version-i.mx-6ull-evk.img.xz | sudo dd of=/dev/sdb bs=4M status=progress
```

This process can take a while, depending on your PC, your microSD card, and the size of the Kali Linux image.

<!-- @steev: TODO: mention the jumper settings according to the documentation once @1y gets back to me about the questions I have. -->

Once the _dd_ operation is complete, boot up the  with the microSD cardplugged in.

You should be able to [log in to Kali](/docs/introduction/default-credentials/).

