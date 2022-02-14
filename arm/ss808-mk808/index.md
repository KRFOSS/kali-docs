---
title: SS808/MK808
description:
icon:
archived: "true"
weight:
author: ["steev",]
build-script: N/A
headless: kali-desktop-xfce
metapackage: N/A
status: eol
cpu:
gpu:
ram:
ethernet:
wifi:
bluetooth:
usb3:
usb2:
storage:
---

{{% notice info %}}
Kali Linux is no longer providing pre-built images or a build-script to generate your own image.
This hardware is no longer supported.
The page is left for historical value.
{{% /notice %}}

- - -

<!-- @g0tmi1k: How does MK808 come into it -->
The SainSmart SS808 is a **rockchip**-based ARM device that comes in various forms and flavors. It has a dual-core 1.6 GHz A9 processor with 1GB of RAM and runs Kali very well.

By default, the Kali Linux SS808 image **does not** contains the [**kali-linux-default** metapackage](/docs/general-use/metapackages/) which is often found in Kali platforms. If you wish to install extra tools please refer to our [metapackages page](/docs/general-use/metapackages/).

## Kali on SS808 - User Instructions

If you're unfamiliar with the details of [downloading and validating a Kali Linux image](/docs/introduction/download-official-kali-linux-images/), or for [using that image to create a bootable device](/docs/usb/live-usb-install-with-windows/), it's strongly recommended that you refer to the more detailed procedures described in the specific articles on those subjects.

If all you want to do is to install Kali on your SS808, follow instructions below:

1. Get a fast microSD card with at least 8GB capacity. Class 10 cards are highly recommended.
2. Download _and validate_ the `Kali Raspberry SS808` image from the [downloads](https://www.offensive-security.com/kali-linux-arm-images/) area. The process for validating an image is described in more detail on [Downloading Kali Linux](/docs/introduction/download-official-kali-linux-images/).
3. Use the **[dd](https://packages.debian.org/testing/dd)** utility to image this file to your microSD card (same process as [making a Kali USB](/docs/usb/live-usb-install-with-windows/).
4. Download the [MK808-Finless-1-6-Custom-ROM](https://forum.freaktab.com/?3207-NEW-MK808-Finless-1-6-Custom-ROM) to a Windows machine and extract the zip file.
5. Read the README file of the MK808 Finless ROM tool, then install the required Windows drivers.
6. Run the Finless ROM Flash Tool and ensure that it says **Found RKAndroid Loader Rock USB** at the bottom. Deselect `kernel.img` and `recovery.img` from the list, and flash the device.
7. Next overwrite both `kernel.img` and `recovery.img` in the FInless ROM directory with the Kali `kernel.img`.
8. In the Finless ROM tool, make sure only `kernel.img` and `recovery.img` are selected, and flash your device again.
9. Insert your microSD card in the SS808 and boot it up.

In our example, we assume the storage device is located at `/dev/sdb`. Do _not_ simply copy these value, **change this to the correct drive path**.

{{% notice info %}}
This process will wipe out your microSD card. If you choose the wrong storage device, you may wipe out your computers hard disk.
{{% /notice %}}

```console
$ dd if=kali-linux-2022.1-SS808.img of=/dev/sdb bs=4M
```

This process can take a while, depending on your PC, your microSD card's speed, and the size of the Kali Linux image.

Once the _dd_ operation is complete, boot up the SS808 with the microSD card plugged in.

You should be able to [log in to Kali](/docs/introduction/default-credentials/) and **startx**.
