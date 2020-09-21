---
title: BeagleBone Black
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

The [BeagleBone Black](http://beagleboard.org/BLACK) is a low-cost, community-supported ARM-based development platform aimed at developers and hobbyists. The BeagleBone Black runs a 1GHz Cortex-A8 CPU and includes hardware-based floating point and 3D acceleration; while much lower-powered than a desktop or laptop system, its affordability makes it an excellent option for a tiny Linux system.

The BeagleBone Black provides a micro-SD card slot for mass storage and if that device is bootable, will use it in preference to the board's "burned-in" Angstrom or Debian operating system.

By default, the Kali Linux BeagleBone Black image contains a minimum toolset, similar to all the other ARM images. If you wish to upgrade the installation to a standard desktop installation, you can include the extra tools by installing the **kali-linux-default** metapackage. For more information on metapackages, please refer to our [tools site](http://tools.kali.org/kali-metapackages).

## Kali Linux on BeagleBone Black - Pre-built Version

If you're unfamiliar with the details of [downloading and validating a Kali Linux image](/docs/introduction/download-official-kali-linux-images/), or for [using that image to create a bootable device](/docs/usb/kali-linux-live-usb-install/), it's strongly recommended that you refer to the more detailed procedures described in the specific articles on those subjects.

To install a prebuilt image of the standard build of Kali Linux on your BeagleBone Black, follow these instructions:

1. Get a fast micro-SD card with at least 8 GB capacity. Class 10 cards are highly recommended.
2. Download _and validate_ the Kali Linux BeagleBone Black image from the Offensive Security [downloads](https://www.offensive-security.com/kali-linux-arm-images/) area. The process for validating an image is described in more detail in the article on  ["Downloading Kali Linux"](/docs/introduction/download-official-kali-linux-images/).
3. Use the **dd** utility to image this file to your microSD card (same process as [making a Kali USB](/docs/usb/kali-linux-live-usb-install/).
In our example, we assume the storage device is located at **_/dev/sdb_**. Do _not_ simply copy these value, **change this to [the correct drive path](/docs/faq/how-do-i-tell-what-drive-path-my-usb-drive-is-on).**

{{% notice info %}}
This command will wipe out any existing data on your micro-SD card. If you specify the _wrong device path_, you could wipe out your computer's hard disk!
{{% /notice %}}

```
dd if=kali-linux-$version-bbb.img of=/dev/sdb bs=4M
```

This process can take a while, depending on your PC, your micro-SD card's speed, and the size of the Kali Linux image. Once the _dd_ operation is complete, insert the micro-SD card into the BeagleBone Black and power it on.
You should be able to log into Kali (as user **_kali_**, using the password **_kali_**) and execute the **startx** command at the shell prompt to start up the XFCE desktop environment.

{{% notice info %}}
**IMPORTANT!** Please change your SSH host keys as soon as possible as **_all_** ARM images are pre-configured the same keys. You should also change the kali password to something more secure, _**especially** if this machine will be publicly accessible!_
{{% /notice %}}

Changing the SSH host keys can be accomplished by doing the following:

```
kali@kali:~# sudo rm /etc/ssh/ssh_host_*
kali@kali:~# sudo dpkg-reconfigure openssh-server
kali@kali:~# sudo service ssh restart
```

## Kali Linux on BeagleBone Black - Custom Build

If you are a developer and want to tinker with the Kali BeagleBone Black image, including changing the kernel configuration, customizing the packages included, or making other modifications, you can work with the **bbb.sh** script in the [kali-arm-build-scripts](https://gitlab.com/kalilinux/build-scripts/kali-arm) repository on github, and follow the _README.md_ file's instructions.

You will need to [set up an ARM cross-compilation environment](/docs/development/arm-cross-compilation-environment/) before you can build a BeagleBone Black image of Kali Linux. A general overview of the build process for ARM devices can be found in the article on ["Preparing a Kali Linux ARM chroot"](/docs/development/kali-linux-arm-chroot/).
