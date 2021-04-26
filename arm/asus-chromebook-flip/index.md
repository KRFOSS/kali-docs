---
title: ASUS Chromebook Flip
description:
icon:
type: post
weight:
author: ["steev",]
---

The [ASUS Chromebook Flip](https://www.asus.com/us/Notebooks/ASUS_Chromebook_Flip_C100PA/) is a quad core 1.8GHz, with 2GB or 4GB of RAM Chromebook with a 10.1" 10 point multitouch touchscreen. Kali Linux fits on an external microSD card or USB key.

By default, the Kali Linux ASUS Chromebook Flip image contains the **kali-linux-default** metapackage similar to other ARM images. If you wish to install extra tools please refer to our [tools site](https://tools.kali.org/kali-metapackages).

The easiest way to generate these images is **from within a pre existing Kali Linux environment**.

## Kali on ASUS Chromebook Flip - User Instructions

Kali no longer provides pre-built images for download, but you can still generate one by cloning the [kali-arm-build-scripts](https://gitlab.com/kalilinux/build-scripts/kali-arm) repository on Gitlab, and follow the _README.md_ file's instructions. The script to use is **chromebook-arm-veyron.sh**.

Once the build script finishes running, you will have an img file in the directory where you ran the script from. At that point, the instructions are the same as if you had downloaded a pre-built image.

If all you want to do is install Kali on your ASUS Chromebook Flip, follow these instructions:

1. Get a fast microSD card or USB key with at least 16GB capacity. Class 10 cards are highly recommended.
2. Use the **dd** utility to image this file to your microSD card (same process as [making a Kali USB](/docs/usb/live-usb-install-with-windows/).

In our example, we assume the storage device is located at **_/dev/sdb_**. Do _not_ simply copy these value, **change this to the correct drive path**.

{{% notice info %}}
This process will wipe out your SD card. If you choose the wrong storage device, you may wipe out your computers hard disk.
{{% /notice %}}

```console
$ xzcat kali-linux-$version-veyron.img.xz | sudo dd of=/dev/sdb bs=4M status=progress
```

This process can take a while, depending on your PC, your microSD card or USB key speed, and the size of the Kali Linux image.

Once the _dd_ operation is complete, boot up the ASUS Chromebook Flip with the microSD card or USB key plugged in, and hit CTRL+U before the 30 second timeout.

You should be able to [log in to Kali](/docs/introduction/default-credentials/).

## Kali on ASUS Chromebook Flip - Image Customization

If you want to customize the Kali ASUS Chromebook Flip image, including changes to the [packages](https://www.kali.org/docs/general-use/metapackages/) being installed, changing the desktop environment, increasing or decreasing the image file size or generally being adventurous, check out the [kali-arm-build-scripts](https://gitlab.com/kalilinux/build-scripts/kali-arm) repository on GitLab, and follow the _README.md_ file's instructions. The script to use is **chromebook-arm-veyron.sh**