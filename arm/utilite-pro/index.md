---
title: Utilite Pro
description:
icon:
type: post
weight:
author: ["steev",]
---

The [Utilite Pro](http://www.compulab.co.il/utilite-computer/web/utilite-overview) is a quad core 1.2GHz Cortex A9, with 2GB of RAM. Kali Linux fits on an external microSD card.

By default, the Kali Linux Utilite Pro image contains the **kali-linux-default** metapackage similar to other ARM images. If you wish to install extra tools please refer to our [tools site](https://tools.kali.org/kali-metapackages).

The easiest way to generate these images is **from within a pre existing Kali Linux environment**.

## Kali on Utilite Pro - User Instructions

Kali no longer provides pre-built images for download, but you can still generate one by cloning the [kali-arm-build-scripts](https://gitlab.com/kalilinux/build-scripts/kali-arm) repository on Gitlab, and follow the _README.md_ file's instructions. The script to use is **TODO.sh**.

Once the build script finishes running, you will have an img file in the directory where you ran the script from. At that point, the instructions are the same as if you had downloaded a pre-built image.

If all you want to do is install Kali on your Utilite Pro, follow these instructions:

1. Get a fast microSD or eMMC card with at least 16GB capacity. Class 10 cards are highly recommended.
2. Use the **dd** utility to image this file to your microSD card (same process as [making a Kali USB](/docs/usb/live-usb-install-with-windows/).

In our example, we assume the storage device is located at **_/dev/sdb_**. Do _not_ simply copy these value, **change this to the correct drive path**.

{{% notice info %}}
This process will wipe out your SD card. If you choose the wrong storage device, you may wipe out your computers hard disk.
{{% /notice %}}

```console
$ xzcat kali-linux-$version-utilite.img.xz | sudo dd of=/dev/sdb bs=4M status=progress
```

This process can take a while, depending on your PC, your microSD/eMMC card's speed, and the size of the Kali Linux image.

Once the _dd_ operation is complete, boot up the Utilite Pro with the microSD/eMMC plugged in.

You should be able to [log in to Kali](/docs/introduction/default-credentials/).

## Kali on Utilite - Image Customization

If you want to customize the Kali Utilite image, including changes to the [packages](https://www.kali.org/docs/general-use/metapackages/) being installed, changing the desktop environment, increasing or decreasing the image file size or generally being adventurous, check out the [kali-arm-build-scripts](https://gitlab.com/kalilinux/build-scripts/kali-arm) repository on GitLab, and follow the _README.md_ file's instructions. The script to use is **utilite.sh**
