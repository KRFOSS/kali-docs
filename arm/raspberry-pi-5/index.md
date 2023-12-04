---
title: Raspberry Pi 5
description:
icon:
weight:
author: ["steev",]
---

The [Raspberry Pi 5](https://www.raspberrypi.org/products/raspberry-pi-5/) has a quad core 2.4GHz processor, with 4GB or 8GB of RAM, depending on model. Kali Linux runs on a microSD card.

By default, the Kali Linux Raspberry Pi 5 image contains the [**kali-linux-default** metapackage](/docs/general-use/metapackages/) similar to most other platforms. If you wish to install extra tools please refer to our [metapackages page](/docs/general-use/metapackages/).

{{% notice info %}}
The Raspberry Pi 5 support is still in its infancy as we are developing the image without access to the hardware.
<br />
{{% /notice %}}

{{% notice info %}}
The Raspberry Pi 5 image does not yet include [Nexmon](https://github.com/seemoo-lab/nexmon) support for the internal Wi-Fi card.  If you plan to do wireless testing, you will need to use a USB Wi-Fi adapter.
{{% /notice %}}

## Kali on Raspberry Pi 5 - User Instructions

If you're unfamiliar with the details of [downloading and validating a Kali Linux image](/docs/introduction/download-official-kali-linux-images/), or for [using that image to create a bootable device](/docs/usb/live-usb-install-with-windows/), it's strongly recommended that you refer to the more detailed procedures described in the specific articles on those subjects.

To install a pre-built image of the standard build of Kali Linux on your Raspberry Pi 5, follow these instructions:

1. Get a fast microSD card with at least 16GB capacity. Class 10 cards are highly recommended.
2. Download _and validate_ our preferred `Kali Raspberry Pi 5` image from the [downloads](/get-kali/) area. The process for validating an image is described in more detail on [Downloading Kali Linux](/docs/introduction/download-official-kali-linux-images/).
3. Use the **[dd](https://manpages.debian.org/testing/coreutils/dd.1.en.html)** utility to image this file to your microSD card (same process as [making a Kali USB](/docs/usb/live-usb-install-with-windows/).

In our example, we assume the storage device is located at `/dev/sdb`. Do _not_ simply copy these value, **change this to the correct drive path**.

{{% notice info %}}
This process will wipe out your microSD card. If you choose the wrong storage device, you may wipe out your computers hard disk.
{{% /notice %}}

```console
$ xzcat kali-linux-2023.4-raspberry-pi-arm64.img.xz | sudo dd of=/dev/sdb bs=4M status=progress
```

This process can take a while, depending on your PC, your microSD's speed, and the size of the Kali Linux image.

Once the _dd_ operation is complete, boot up the Raspberry Pi 5 with the microSD plugged in.

You should be able to [log in to Kali](/docs/introduction/default-credentials/).

## Kali on Raspberry Pi 5 - Tips and Tricks

<!-- Need to verify this is true with the 5 before adding it.
By default, audio is routed via HDMI, so you won't hear audio via the 3.5mm audio jack. You can run the following command in order to redirect the output:

```console
kali@kali:~$ sudo amixer -c 0 set numid=3 1
```
-->

To build external modules against the kernel, most instructions will state that you need to install header packages.  This is **not** the case on the Raspberryi Pi 5 image, they are already included and are **not** a part of a package.  
In order to make the headers usable however, you do need to run the following commands.  

```console
kali@kali:~$ cd /usr/src/kernel
kali@kali:/usr/src/kernel/$ sudo git clean -fdx && sudo make bcm2711_defconfig && sudo make modules_prepare
```

To build external modules that are from the Kali package repository, as an example [realtek-rtl88xxau-dkms](https://pkg.kali.org/pkgs/realtek-rtl88xxau-dkms) you need to tell it to install **without** also installing recommended packages.

```console
kali@kali:~$ sudo apt install --no-install-recommends realtek-rtl88xxau-dkms
```

- - -

# Kali on Raspberry Pi 5 Headless - Tips and Tricks

You can add a `wpa_supplicant.conf` file to the first partition of the microSD card to connect to a wireless network.

You can create this file on another Linux system by running `wpa_passphrase YOURNETWORK > wpa_supplicant.conf`. It will prompt you for the wireless network's password. You can add the password to the command as you run it, but keep in mind that if you do, your wifi network password will be in your user's shell history.

- - -

## Kali on Raspberry Pi 5 - Image Customization

If you want to customize the Kali Raspberry Pi 5 image, including changes to the [packages](/docs/general-use/metapackages/) being installed, changing the [desktop environment](/docs/general-use/switching-desktop-environments/), increasing or decreasing the image file size or generally being adventurous, check out the [Kali-ARM Build-Scripts](https://gitlab.com/kalilinux/build-scripts/kali-arm) repository on GitLab, and follow the _README.md_ file's instructions. The script to use is `raspberry-pi5.sh` (64-bit).
