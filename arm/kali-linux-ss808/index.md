---
title: SS808/MK808
description:
icon:
type: archived
weight:
author: ["steev",]
---

The SainSmart SS808 is a **rockchip**-based ARM device that comes in various forms and flavors. It has a dual-core 1.6 GHz A9 processor with 1 GB of RAM and runs Kali very well.

## Stock Kali on SS808 - Easy Version

If all you want to do is to install Kali on your SS808, follow instructions below:

1. Get a nice fast 8 GB (or more) microSD card. Class 10 cards are highly recommended.
2. Download the Kali Linux SS808 image from our [downloads](https://www.offensive-security.com/kali-linux-vmware-arm-image-download/) area.
3. Use the **dd** utility to image this file to your microSD card. In our example, we assume the storage device is located at /dev/sdb and are using an SS808 image. **Change this as needed**.
4. Download the [MK808-Finless-1-6-Custom-ROM](http://www.freaktab.com/showthread.php?3207-NEW-MK808-Finless-1-6-Custom-ROM) to a Windows machine and extract the zip file.
5. Read the README file of the MK808 Finless ROM tool, then install the required Windows drivers.
6. Run the Finless ROM Flash Tool and ensure that it says "Found RKAndroid Loader Rock USB" at the bottom. Deselect kernel.img and recovery.img from the list, and flash the device.
7. Next overwrite both kernel.img and recovery.img in the FInless ROM directory with the kali "kernel.img".
8. In the Finless ROM tool, make sure only "kernel.img" and "recovery.img" are selected, and flash your device again.
9. Insert your microSD card in the SS808 and boot it up.

{{% notice info %}}
**Alert!** This process will wipe out your SD card! If you choose the wrong storage device, you may wipe out your computers hard disk.
{{% /notice %}}

```console
$ dd if=kali-linux-$version-SS808.img of=/dev/sdb bs=4M
```

This process can take a while depending on your USB storage device speed and image size. Once the dd operation is done, boot up your SS808, with the microSD card plugged in. [Log in to Kali](/docs/introduction/default-credentials/) and **startx**. That's it, you're done!
