---
title: Samsung Chromebook 2
description:
icon:
type: post
weight:
author: ["steev",]
---

The [Samsung ARM Chromebook 2](https://web.archive.org/web/20161111005125/http://www.samsung.com/us/computing/chromebooks/12-14/samsung-chromebook-2-13-3-xe503c32-k01us/) is an ultraportable laptop. It was quite a challenge, but we have a Kali image that runs great on the Chromebook. Boasting an Exynos 5800 1.7GHz quad core processor and 4 GB of RAM, the Chromebook 2 is a fast ARM laptop. Kali Linux fits on an external micro SD card on this machine which leaves the internal disk untouched.

By default, the Kali Linux Samsung Chromebook 2 image contains the **kali-linux-default** metapackage similar to other ARM images. If you wish to install extra tools please refer to our [tools site](https://tools.kali.org/kali-metapackages).

The easiest way to generate these images is **from within a pre existing Kali Linux environment**.

## Kali on Samsung Chromebook 2 - User Instructions

Kali no longer provides pre-built images for download, but you can still generate one by cloning the [kali-arm-build-scripts](https://gitlab.com/kalilinux/build-scripts/kali-arm) repository on Gitlab, and follow the _README.md_ file's instructions. The script to use is **chromebook-arm-exynos.sh**.

Once the build script finishes running, you will have an img file in the directory where you ran the script from. At that point, the instructions are the same as if you had downloaded a pre-built image.

If all you want to do is install Kali on your Samsung Chromebook 2, follow these instructions:

1. Get a fast microSD card or USB key with at least 16GB capacity. Class 10 cards are highly recommended.
2. [Put your Chromebook in developer mode](http://www.chromium.org/chromium-os/developer-information-for-chrome-os-devices/acer-c720-chromebook), and enable USB boot.
3. Use the **dd** utility to image this file to your microSD card or USB key (same process as [making a Kali USB](/docs/usb/live-usb-install-with-windows/).

In our example, we assume the storage device is located at **_/dev/sdb_**. Do _not_ simply copy these value, **change this to the correct drive path**.

{{% notice info %}}
This process will wipe out your SD card or USB key. If you choose the wrong storage device, you may wipe out your computers hard disk.
{{% /notice %}}

```console
$ xzcat kali-linux-$version-exynos.img.xz | sudo dd of=/dev/sdb bs=4M status=progress
```

This process can take a while, depending on your PC, your microSD card or USB key speed, and the size of the Kali Linux image.

Once the _dd_ operation is complete, boot up the Samsung Chromebook 2 with the microSD or USB key plugged in, and hit CTRL+U before the 30 second timeout.

You should be able to [log in to Kali](/docs/introduction/default-credentials/).
