---
title: Raspberry Pi
description:
icon:
date: 2019-10-26
type: post
weight: 100
author: ["steev",]
tags: ["",]
keywords: ["",]
og_description:
---


```
The [[ https://www.kali.org/docs/arm/install-kali-linux-arm-raspberry-pi | Raspberry Pi Kali doc ]] should be updated to account for errors found in [[ https://forums.kali.org/showthread.php?13-Install-Kali-ARM-on-a-Raspberry-Pi/page2 | this thread ]], this [[ https://itfellover.com/kali-linux-1-1-0-git-install-kernel-cross-compilation-with-wireless-injection-working-v2/ | user posted doc ]], and even [[ https://www.raspberrypi.org/forums/viewtopic.php?t=68598&p=500327 | this Raspberry Pi doc ]]. There’s even a badass user-created project running Kali on a Pi3, a touch interface and mounted on a friggin DRONE [[ https://whitedome.com.au/re4son/sticky-fingers-kali-pi/ | here ]].
```

The [Raspberry Pi](http://raspberrypi.org) is a low-cost, credit-card-sized ARM computer. Despite being a good bit less powerful than a laptop or desktop PC, its affordability makes it an excellent option for a tiny Linux system and it can do far more than act as a media hub.

The Raspberry Pi provides a SD card slot for mass storage and will attempt to boot off that device when the board is powered on.

By default, the Kali Linux Raspberry Pi image has been streamlined with the minimum tools, similar to all the other ARM images. If you wish to upgrade the installation to a standard desktop installation, you can include the extra tools by installing the **kali-linux-full** metapackage. For more information on metapackages, please refer to our [tools page](http://tools.kali.org/kali-metapackages).

## Kali Linux on Raspberry Pi — Pre-built Version

If you're unfamiliar with the details of [downloading and validating a Kali Linux image](/docs/introduction/download-official-kali-linux-images/), or for [using that image to create a bootable device](/docs/usb/kali-linux-live-usb-install/), it's strongly recommended that you refer to the more detailed procedures described in the specific articles on those subjects.

To install a prebuilt image of the standard build of Kali Linux on your Raspberry Pi, the general process goes as follows:

1. Get a fast SD card with at least 8 GB capacity. Class 10 cards are highly recommended.
2. Download _and validate_ the Kali Linux Raspberry Pi image from the Offensive Security [downloads](https://www.offensive-security.com/kali-linux-arm-images/) area. The process for validating an image is described in more detail in the article on  ["Downloading Kali Linux"](/docs/introduction/download-official-kali-linux-images/).
3. Use the **dd** utility to image this file to your SD card. The full process for creating a bootable USB or SD device is described in the article on ["Making a Kali Live USB Drive"](/docs/usb/kali-linux-live-usb-install/). In the following example, we assume that the image is named "kali-2.1.2-rpi.img", that it's is in your current working directory, and that the SD card is located at **/dev/sdb**. Do _not_ simply copy these value, **change this to [the correct drive path](https://www.kali.org/docs/faq/how-do-i-tell-what-drive-path-my-usb-drive-is-on) corresponding to your SD card.**

{{% notice info %}}
This command will overwrite any existing data on your SD card. If you specify the _wrong device path_, you could wipe out your computer's hard disk!
{{% /notice %}}

```
root@kali:~ dd if=kali-2.1.2-rpi.img of=/dev/sdb bs=512k
```

This process can take a while depending on your SD card's device speed and image size. Once the **dd** operation is complete, insert the SD card into the Raspberry Pi and power it on.

You should be able to log into Kali (as user **_root_**, using the password **_toor_**) and execute the **startx** command at the shell prompt to start up the XFCE desktop environment.

{{% notice info %}}
**IMPORTANT!** Please change your SSH host keys as soon as possible as **_all_** ARM images are pre-configured the same keys. You should also change the root password to something more secure, _**especially** if this machine will be publicly accessible!_
{{% /notice %}}

Changing the SSH host keys can be accomplished by doing the following:

```
root@kali:~ rm /etc/ssh/ssh_host_*
root@kali:~ dpkg-reconfigure openssh-server
root@kali:~ service ssh restart
```

## Kali Linux on Raspberry Pi — Custom Build

If you are a developer and want to tinker with the Kali Raspberry Pi image, including changing the kernel configuration, customizing the packages included, or making other modifications, you can work with the **rpi.sh** script in the [kali-arm-build-scripts](https://gitlab.com/kalilinux/build-scripts/kali-arm) repository on github, and follow the _README.md_ file's instructions.

You will need to [set up an ARM cross-compilation environment](/docs/development/arm-cross-compilation-environment/) before you can build a Raspberry Pi image of Kali Linux. A general overview of the build process for ARM devices can be found in the article on ["Preparing a Kali Linux ARM chroot"](/docs/development/kali-linux-arm-chroot/).
