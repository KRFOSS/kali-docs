---
title: Trimslice
description:
icon:
weight:
author: ["steev",]
---

The [Trimslice](http://www.compulab.co.il/utilite-computer/web/trim-slice) is a dual core 1GHz, with 1GB of RAM. Kali Linux fits on an external microSD card.

By default, the Kali Linux Trimslice image contains the [**kali-linux-default** metapackage](/docs/general-use/metapackages/) similar to most other platforms. If you wish to install extra tools please refer to our [metapackages page](/docs/general-use/metapackages/).

{{% notice info %}}
The build script for the Trimslice has not been converted to the new style, so builds may fail. If you are planning to build for this board, please consider updating the script to the new way, and submitting it as a merge request.
{{% /notice %}}

## Kali on Trimslice - Build-Script Instructions

Kali does not provide pre-built images for download, but you can still generate one by cloning the [Kali-ARM Build-Scripts](https://gitlab.com/kalilinux/build-scripts/kali-arm) repository on GitLab, and follow the _README.md_ file's instructions. The script to use is `trimslice.sh`.

Once the build script finishes running, you will have an "img.xz" file in the `images` directory where you ran the script from. At that point, the instructions are the same as if you had downloaded a pre-built image.

The easiest way to generate these images is **from within a pre-existing Kali Linux environment**.

## Kali on Trimslice - User Instructions

To install Kali on your Trimslice, follow these instructions:

1. Get a fast microSD card with at least 16GB capacity. Class 10 cards are highly recommended.
2. Use the **[dd](https://manpages.debian.org/testing/coreutils/dd.1.en.html)** utility to image this file to your microSD card (same process as [making a Kali USB](/docs/usb/live-usb-install-with-windows/).

In our example, we assume the storage device is located at `/dev/sdX`. Do _not_ simply copy these value, **change this to the correct drive path**.

{{% notice info %}}
This process will wipe out your microSD card. If you choose the wrong storage device, you may wipe out your computers hard disk.
{{% /notice %}}

```console
$ xzcat images/kali-linux-2024.3-trimslice-armhf.img.xz | sudo dd of=/dev/sdX bs=4M status=progress
```

This process can take a while, depending on your PC, your microSD card speed, and the size of the Kali Linux image.

Once the _dd_ operation is complete, boot up the Banana Pro with the microSD card plugged in.

You should be able to [log in to Kali](/docs/introduction/default-credentials/).
