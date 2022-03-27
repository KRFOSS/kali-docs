---
title: Samsung Chromebook (daisy_snow) & Samsung Chromebook 2 (peach_pi / peach_pit) & HP Chromebook (daisy_spring)
description:
icon:
weight:
author: ["steev",]
build-script: https://gitlab.com/kalilinux/build-scripts/kali-arm/-/blob/master/chromebook-exynos.sh
headless: kali-desktop-xfce
metapackage: kali-linux-default
status: build-scripts
cpu: "Exynos 5 Octa 5420"
gpu: Mali-T628MP6
ram: DDR3L
ram-size: 4GB
ethernet: no
wifi: "2.4GHz b/g/n"
bluetooth: yes
usb3: 1
usb2: 1
storage: ["sdcard","emmc"]
---

{{% notice info %}}
The ChromiumOS code names for the:

- Samsung Chromebook is **daisy_snow**.
- Samsung Chromebook 2 is **peach_pi** (1080p screen) or **peach_pit** (1366x768).
- HP Chromebook is **daisy_spring**

We use **exynos** for the script name as there are a number of Chromebooks with the Samsung Exynos processor in them that use the same kernel.
{{% /notice %}}

The Samsung ARM Chromebooks are ultraportable laptops. Boasting an Exynos 5250 1.7GHz dual core processor and 2GB of RAM (4GB of RAM for the [Chromebook 2](https://web.archive.org/web/20161111005125/http://www.samsung.com/us/computing/chromebooks/12-14/samsung-chromebook-2-13-3-xe503c32-k01us/)), the Chromebook is a fast ARM laptop. Kali Linux fits on an external full-size SD card (or microSD card for the Chromebook 2) or USB drive for these machine which leaves the internal disk untouched.

The [HP ARM Chromebook](https://www8.hp.com/ca/en/ads/chromebooks/specs.html) is also an ultraportable laptop. It also hasan Exynos 5250 1.7GHz dual core processor and 2GB of RAM, the Chromebook is an equally fast ARM laptop. Kali Linux fits on an USB drive on this machine which means the internal disk is untouched.

By default, the Kali Linux Samsung/HP Chromebook image contains the [**kali-linux-default** metapackage](/docs/general-use/metapackages/) similar to most other platforms. If you wish to install extra tools please refer to our [metapackages page](/docs/general-use/metapackages/).

{{% notice info %}}
The build script for the Exynos based Chromebooks has not been converted to the new style, so builds may fail.  If you are planning to build for this board, please consider updating the script to the new way, and submitting it as a merge request.
{{% /notice %}}

## Kali on Samsung/HP Chromebook - Build-Script Instructions

Kali does not provide pre-built images for download, but you can still generate one by cloning the [Kali-ARM Build-Scripts](https://gitlab.com/kalilinux/build-scripts/kali-arm) repository on GitLab, and follow the _README.md_ file's instructions. The script to use is `chromebook-exynos.sh`.

Once the build script finishes running, you will have an "img" file in the directory where you ran the script from. At that point, the instructions are the same as if you had downloaded a pre-built image.

The easiest way to generate these images is **from within a pre-existing Kali Linux environment**.

## Kali on Samsung/HP Chromebook - User Instructions

To install Kali on your Samsung/HP Chromebook, follow these instructions:

1. Get a fast microSD card or USB drive with at least 16GB capacity. Class 10 cards are highly recommended.
2. [Put your Chromebook in developer mode](http://www.chromium.org/chromium-os/developer-information-for-chrome-os-devices/acer-c720-chromebook), and enable USB boot.
3. Use the **[dd](https://packages.debian.org/testing/dd)** utility to image this file to your microSD card or USB drive (same process as [making a Kali USB](/docs/usb/live-usb-install-with-windows/).

In our example, we assume the storage device is located at `/dev/sdb`. Do _not_ simply copy these value, **change this to the correct drive path**.

{{% notice info %}}
This process will wipe out your microSD card or USB drive. If you choose the wrong storage device, you may wipe out your computers hard disk.
{{% /notice %}}

```console
$ xzcat kali-linux-2022.1-chromebook-exynos-xfce-armhf.img.xz | sudo dd of=/dev/sdb bs=4M status=progres
```

This process can take a while, depending on your PC, your microSD card or USB drive speed, and the size of the Kali Linux image.

Once the _dd_ operation is complete, boot up the Samsung/HP Chromebook with the microSD card or USB drive plugged in, and hit **CTRL+U** before the 30 second timeout.

You should be able to [log in to Kali](/docs/introduction/default-credentials/).
