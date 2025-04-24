---
title: Installing NetHunter on the OnePlus 5T
description:
icon:
weight:
author: ["i-liek-turtals",]
---

We are going to be covering how to install Kali NetHunter on a OnePlus One. This guide will use Windows 11 (as the host), a USB cable (to connect), TWRP (for recovery), Magisk (for root access) and LineageOS v20 (Android 13, for the ROM).

Steps are as follows:

- Wipe the phone
- Enable developer mode & USB debug mode
- OEM Unlock
- Install LineageOS (version 20)
- Root the device
- Install NetHunter

We’ll be using a pre-created Nethunter image for this device but in order to install it, we’re going to remove stock OxygenOS and will be installing LineageOS.

[According to Nethunter wiki](https://nethunter.kali.org/images.html), we need a specific version of LineageOS (version 20). I wasn’t able to find this version on LineageOS’ official website so I’ll be linking a non official download link. Use it at your own risk.

This is an unsigned build but I didn’t have any issues while installing it. Apart from this, the installation process is identical to LineageOS v22’s installation so I’ll be following their [guide](https://wiki.lineageos.org/devices/dumpling/install/).

Also a side note, 5T’s last update was for Android v10. Installing [LineageOS v20](https://wiki.lineageos.org/devices/dumpling/) will upgrade it to Android v13 so we don’t have to disable force encryption & DM-verity.