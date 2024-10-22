---
title: Installing NetHunter
description:
icon:
weight:
author: ["re4son",]
---

## Overview

Installing NetHunter requires the following steps:

1. [Download a pre-built image or build your own image](#1-nethunter-pre-built-images-and-support)
2. [Put your device in developer mode](#2-putting-your-device-in-developer-mode)
3. [Unlock your device](#3-unlocking-rooting-and-installing-a-custom-recovery-on-your-android-device)
4. [Install TWRP](#3-unlocking-rooting-and-installing-a-custom-recovery-on-your-android-device)
5. [Flash Magisk](#3-unlocking-rooting-and-installing-a-custom-recovery-on-your-android-device)
6. [Android 9 and above: Format "data" and flash Universal DM-Verity & ForceEncrypt Disabler](#4-flashing-universal-dm-verity--forceencrypt-disabler)
7. [Install NetHunter](#5-installing-the-nethunter-image)
8. [Android 10 and above: Update NetHunter App from the NetHunter Store](#5-installing-the-nethunter-image)
9. [Run the NetHunter App to finish the installation](#5-installing-the-nethunter-image)

## 1. NetHunter pre-built images and support

The NetHunter team builds and publishes pre-created images for a selected list of devices, on the [official NetHunter download page](/get-kali/#kali-mobile).

If you devices is not available as a [pre-build image](https://nethunter.kali.org/image-stats.html) but supported by NetHunter, you can easily build your own image by following the steps in our ["Building NetHunter" documentation](/docs/nethunter/building-nethunter/).

You can confirm that your device and Android version is supported via:

- [NetHunter pre-created images](https://nethunter.kali.org/images.html)
- [NetHunter supported kernels](https://nethunter.kali.org/kernels.html)

## 2. Putting your device in "Developer Mode"

Before the installation begins, you must enable _Developer Mode_ on your device.
This is done by navigating to _Settings_ -> _About_ and tapping on the _Build number_ field 7 times until you receive the notification that Developer Mode has been enabled.

Go back to the main settings page and you will have a new section titled _Developer options_.

Tap on the new _Developer options_ section and enable both the _Advanced Reboot_ and _Android Debugging_ options.

## 3. Unlocking, rooting, and installing a custom recovery on your android device

NetHunter supports over [99 different devices](https://nethunter.kali.org/images.html) running [Android versions from 4.4/Kitkat though to 11/Eleven](https://nethunter.kali.org/kernel-stats.html).

Whilst we have standardised the NetHunter installation procedure, the steps to unlock, root, and install a custom recovery varies from device to device and even differs between Android versions.

The preferred custom recovery for NetHunter is [TWRP](https://twrp.me/Devices/).

The preferred software to root the device for NetHunter is [Magisk](https://xdaforums.com/t/magisk-the-magic-mask-for-android.3473445/).

Please refer to the appropriate guide to unlock, root, and install a custom recovery on your device from your preferred Internet resource, such as the [XDA Developers Forums](https://xdaforums.com/).

## 4. Flashing Universal DM-Verity & ForceEncrypt Disabler

**IMPORTANT NOTE** for Android 9, 10, & 11 users: Please ensure that you flash the [Universal DM-Verity, ForceEncrypt Disabler](https://xdaforums.com/t/deprecated-universal-dm-verity-forceencrypt-disk-quota-disabler-11-2-2020.3817389/) and format the data partition prior to installing NetHunter.
Magisk does not support user context changes on encrypted data partitions, which leads to errors when connecting to the Kali rootfs via ssh (i.e. "Required key not available") if the data partition is encrypted.

## 5. Installing the NetHunter Image

Now that your Android phone is ready, transfer the NetHunter image to it, reboot in recovery mode, and flash the zip on your phone. Once done, reboot and launch the NetHunter app to complete the setup!

**IMPORTANT NOTE** for Android 10 & 11 users: Please update the NetHunter app from the NetHunter store after flashing NetHunter. Android 10 introduced "scoped storage" restrictions which prevents NetHunter from using the storage location we traditionally used to save configuration files. We are in the process of moving the location and implementing an import/export function but updating the app after flashing NetHunter provides a workaround that allows us to continue accessing the current storage location until the new features are implemented.
