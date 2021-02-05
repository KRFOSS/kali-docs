---
title: CuBox
description:
icon:
type: post
weight:
author: ["steev",]
---

The [CuBox](https://www.solid-run.com/product/cubox-carrier-base/) is a low end, low cost ARM computer. Despite its less-than-stellar specifications, its affordability makes it an excellent option for a tiny Linux system and it can do far more than act as a media PC.

The easiest way to generate these images is **from within a pre existing Kali Linux environment**.

## Stock Kali on CuBox - Easy Version

If all you want to do is to install Kali on your CuBox, follow these instructions:

1. Get a nice fast 8 GB (or more) SD card. Class 10 cards are highly recommended.
2. Download the Kali Linux CuBox image from our [downloads](https://www.offensive-security.com/kali-linux-arm-images/) area.
3. Use the **dd** utility to image this file to your SD card. In our example, we assume the storage device is located at **_/dev/sdb_**. **_Change this as needed._**

{{% notice info %}}
This process will wipe out your SD card. If you choose the wrong storage device, you may wipe out your computers hard disk.
{{% /notice %}}

```
dd if=kali-linux-$version-cubox.img of=/dev/sdb bs=4M
```

This process can take a while depending on your USB storage device speed and image size. Once the _dd_ operation is complete, boot up your CuBox with the SD card plugged in. You will be able to [Log in to Kali](/docs/introduction/default-credentials/) and **startx**. That's it, you're done!

{{% notice info %}}
If the image does not boot, please connect via serial and make sure that your u-boot version is listed as 5.4.4 NQ SR1. If it is just 5.4.4 NQ, you will need to upgrade it via the CuBox installer. Instructions can be found at the [CuBox Wiki](http://wiki.solid-run.com/doku.php?id=products:imx6:cubox-i)
{{% /notice %}}

## Kali on CuBox - Long Version

If you are a developer and want to tinker with the Kali CuBox image, including changing the kernel configuration and generally being adventurous, check out the [kali-arm-build-scripts](https://gitlab.com/kalilinux/build-scripts/kali-arm) repository on GitHub, and follow the _README.md_ file's instructions. The script to use is **cubox.sh**
