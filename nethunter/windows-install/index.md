---
title: Installing NetHunter from Windows
description:
icon:
date: 2019-10-26
type: post
weight: 100
author: ["g0tmi1k",]
tags: ["",]
keywords: ["",]
og_description:
---

## Installation Caveats

**IMPORTANT NOTE**: The initial installation of NetHunter can be a frustrating experience if the installation requirements are not met. Most of the installation issues arise from misconfigured Android drivers on your Windows system. It is therefore extremely important to make sure the Android drivers are installed properly before proceeding with the installation. Make sure you carefully follow the **"Installing BRT/ NRT and Google / Android Windows drivers"** process described below in the "Installing from Windows" section.

**IMPORTANT NOTE**: To ensure a hassle free installation, use USB 2.0 ports during the installation. When installations fail inexplicably, it's most likely a USB version standard issue. We use a powered USB 2.0 hub to connect our OPO to a computer during installation.

## Putting Your Device in "Developer Mode"

Before the installation begins, you must enable _Developer mode_ on your device. This is done by navigating to _Settings_ -> _About_ and tapping on the _Build number_ field 7 times until you receive the notification that developer mode has been enabled. Go back to the main settings page and you will have a new section titled _Developer options_. Tap on the new _Developer options_ section and enable both the _Advanced Reboot_ and _Android Debugging_ options.

## Unlocking and Rooting Your Android Device
For first time installations, it is usually best to completely flash your device "to stock" and bring it to a known-good state. This will ensure as painless an installation as possible, removing many of the variables that would cause an incomplete or failed installation. While there are many ways to unlock and root your Android devices, we chose to use the Windows based "Boot Rootkit" by WugFresh. Depending on which type of device you’re setting up, choose the correct Boot Rootkit installer:

* Nexus devices -  Nexus Boot Rootkit -  http://www.wugfresh.com/nrt/
* OnePlus devices - Bacon Boot Rootkit -  http://www.wugfresh.com/brt/

## Installing BRT/ NRT and Android Windows Drivers

Download and install the NRT/BRT application (as needed) and execute it for the first time. Once loaded, click the _Full Driver Installation Guide_ button. A Window with installation instructions will pop up – **it is vital you read these instructions very carefully and follow them slowly**. Once you have successfully completed a _Full Driver Test_ in Step 4, proceed with the NetHunter installation process described below.

## Flashing Back to Stock

Flashing your device back to stock allows for an installation over a clean slate, reducing the possibility of errors during the installation process. You can "flash to stock" using either NRT or BRT - however make sure to adhere to the compatibility table outlined in the [Supported Devices and ROMs](https://www.kali.org/docs/nethunter/home/#1-0-supported-devices-and-roms) section of this doc.

Depending on the version of NRT / BRT you are using, you may need to manually apply "Over The Air" updates to your device once flashed to stock. This is done by navigating to _Settings_ -> _About_ and tapping on the _System updates_ field. You may need to apply several updates before the device will be at the latest version, rebooting after each update. Once there are no more updates, continue with the installation process described below.

## Rooting Your Device

Once flashed, click on the _Root_ button in the NRT / BRT application. From our experience, it is better if _Custom Recovery_ is **unchecked** in this initial process. Once rooted, re-check _Custom Recovery_ and hit the _Root_ button again. This will install the TWRP custom recovery image on your Android device.

## Installing the NetHunter Image

Now that your Android phone is ready, transfer the NetHunter image to it, reboot in recovery mode, and flash the zip on your phone. Once done, reboot and bask in the glory of NetHunter!
