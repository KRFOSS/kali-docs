---
title: i.MX 6ULL EVK
description:
icon:
archived: "true"
weight:
author: ["steev",]
---

<!-- @steev: TODO: This is a community contributed image, so we don't know that much about it, nor do we test it. -->

The [i.MX 6ULL EVK](https://www.nxp.com/design/development-boards/i-mx-evaluation-and-development-boards/evaluation-kit-for-the-i-mx-6ull-and-6ulz-applications-processor:MCIMX6ULL-EVK) is a market-focused development tool based on the i.MX 6ULL applications processor. Kali Linux fits on a microSD card for it.

By default, the Kali Linux i.MX 6ULL EVK image contains the [**kali-linux-default** metapackage](/docs/general-use/metapackages/) similar to most other platforms. If you wish to install extra tools please refer to our [metapackages page](/docs/general-use/metapackages/).

## Kali on the i.MX 6ULL EVK - Build-Script Instructions

Kali does not provide pre-built images for download, but you can still generate one by cloning the [Kali-ARM Build-Scripts](https://gitlab.com/kalilinux/build-scripts/kali-arm) repository on GitLab, and follow the _README.md_ file's instructions. The script to use is `imx-6ull-evk.sh`.

Once the build script finishes running, you will have an "img" file in the directory where you ran the script from. At that point, the instructions are the same as if you had downloaded a pre-built image.

The easiest way to generate these images is **from within a pre-existing Kali Linux environment**.

1. Get a fast microSD card with at least 16GB capacity. Class 10 cards are highly recommended.
2. Use the **[dd](https://packages.debian.org/testing/dd)** utility to image this file to your microSD card (same process as [making a Kali USB](/docs/usb/live-usb-install-with-windows/).

In our example, we assume the storage device is located at `/dev/sdb`. Do _not_ simply copy these value, **change this to the correct drive path**.

{{% notice info %}}
This process will wipe out your microSD card. If you choose the wrong storage device, you may wipe out your computers hard disk.
{{% /notice %}}

```console
$ xzcat kali-linux-2022.4-imx-6ull-evk-armhf.img.xz | sudo dd of=/dev/sdb bs=4M status=progres
```

This process can take a while, depending on your PC, your microSD card, and the size of the Kali Linux image.

<!-- @steev: TODO: mention the jumper settings according to the documentation once @1y gets back to me about the questions I have. -->

Once the _dd_ operation is complete, boot up the with the microSD card plugged in.

You should be able to [log in to Kali](/docs/introduction/default-credentials/).
