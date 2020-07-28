---
title: Acer Tegra Chromebook 13"
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

The Acer Tegra Chromebook is an ultraportable laptop. It was quite a challenge, but we have a Kali image that runs great on the Chromebook. Boasting a Tegra K1 2.1GHz quad core processor and 4 GB of RAM, the Chromebook is a fast ARM laptop. Kali Linux fits on an external SD card on this machine which leaves the internal disk untouched.

## Kali on Chromebook - User Instructions

If all you want to do is install Kali on your Acer Tegra Chromebook, follow these instructions:

1. Get a nice fast 8 GB SD card.
2. [Put your Chromebook in developer mode](http://www.chromium.org/chromium-os/developer-information-for-chrome-os-devices/acer-c720-chromebook), and enable USB boot.
3. Download the Kali Acer Tegra Chromebook image from our [downloads](https://www.offensive-security.com/kali-linux-arm-images/) area.
4. Use the **dd** utility to image this file to your SD card device. In our example, we use an SD Card which is located at **_/dev/sdb_**. **_Change this as needed._**

{{% notice info %}}
**Alert!** This process will wipe out your SD card. If you choose the wrong storage device, you may wipe out your computers hard disk.
{{% /notice %}}

```
xzcat kali-$version-acer.img | dd of=/dev/sdb bs=4M
```

This process can take awhile depending on your storage device speed and image size.

Once the _dd_ operation is complete, boot up the Chromebook with the SD card plugged in. At the developer boot prompt, hit CTRL+U, which should boot you into Kali Linux. Log in to Kali (**_kali_** / **_kali_**). That's it, you're done!

## Kali on Acer Tegra Chromebook - Developer Instructions

If you are a developer and want to tinker with the Kali Acer Chromebook image, including changing the kernel configuration and generally being adventurous, check out the [kali-arm-build-scripts](https://gitlab.com/kalilinux/build-scripts/kali-arm) repository on GitHub, and follow the _README.md_ file's instructions. The script to use is **chromebook-arm-acer.sh**
