---
title: Samsung Chromebook 2
description:
icon:
date: 2019-11-25
type: post
weight: 100
author: ["steev",]
tags: ["",]
keywords: ["",]
og_description:
---

The [Samsung ARM Chromebook 2](https://web.archive.org/web/20161111005125/http://www.samsung.com/us/computing/chromebooks/12-14/samsung-chromebook-2-13-3-xe503c32-k01us/) is an ultraportable laptop. It was quite a challenge, but we have a Kali image that runs great on the Chromebook. Boasting an Exynos 5800 1.7GHz quad core processor and 4 GB of RAM, the Chromebook 2 is a fast ARM laptop. Kali Linux fits on an external micro SD card on this machine which leaves the internal disk untouched.

## Kali on Chromebook 2 - User Instructions

If all you want to do is install Kali on your Samsung ARM Chromebook 2, follow these instructions:

1. Get a nice fast 8 GB micro SD card.
2. [Put your Chromebook in developer mode](http://www.chromium.org/chromium-os/developer-information-for-chrome-os-devices/samsung-arm-chromebook#TOC-Developer-Mode), and enable USB boot.
3. Download the Kali Samsung ARM Chromebook 2 image from our [downloads](https://www.offensive-security.com/kali-linux-arm-images/) area.
4. Use the **dd** utility to image this file to your SD device. In our example, we use a micro SD card which is located at **_/dev/sdb_**. **_Change this as needed._**

{{% notice info %}}
This process will wipe out your SD card. If you choose the wrong storage device, you may wipe out your computers hard disk.
{{% /notice %}}

```
xzcat kali-$ver-exynos.img.xz | dd of=/dev/sdb bs=4M
```

This process can take awhile depending on your device speed and image size.

Once the _dd_ operation is complete, boot up the Chromebook with the SD card plugged in. At the developer boot prompt, hit CTRL+U, which should boot you into Kali Linux. Log in to Kali (**_root_** / **_toor_**), that's it, you're done!

## Kali on Samsung Chromebook 2 - Developer Instructions

If you are a developer and want to tinker with the Kali Samsung Chromebook 2 image, including changing the kernel configuration and generally being adventurous, check out the [kali-arm-build-scripts](https://gitlab.com/kalilinux/build-scripts/kali-arm) repository on GitHub, and follow the _README.md_ file's instructions. The script to use is **chromebook-arm-exynos.sh**
