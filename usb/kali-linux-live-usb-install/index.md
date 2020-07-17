---
title: Making a Kali Bootable USB Drive
description:
icon:
date: 2020-03-07
type: post
weight: 10
author: ["g0tmi1k",]
tags: ["",]
keywords: ["",]
og_description:
---

Our favourite way, and the fastest method, for getting up and running with Kali Linux is to run it "live" from a USB drive. This method has several advantages:

- It's non-destructive — it makes no changes to the host system's hard drive or installed OS, and to go back to normal operations, you simply remove the "Kali Live" USB drive and restart the system.
- It's portable — you can carry Kali Linux in your pocket and have it running in minutes on an available system
- It's customizable — you can [roll your own custom Kali Linux ISO image](/docs/development/live-build-a-custom-kali-iso/) and put it onto a USB drive using the same procedures
- It's potentially persistent — with a bit of extra effort, you can configure your Kali Linux "live" USB drive to have [persistent storage](/docs/usb/kali-linux-live-usb-persistence/), so the data you collect is saved across reboots

In order to do this, we first need to create a bootable USB drive which has been set up from an ISO image of Kali Linux.

## What You'll Need

1. A _verified_ copy of the appropriate ISO image of the latest Kali build image for the system you'll be running it on: see the details on [downloading official Kali Linux images](/docs/introduction/download-official-kali-linux-images/).

2. If you're running under Windows, there is not one tool that is considered the overall best for imaging. We recommend [Etcher](https://www.balena.io/etcher/), however Rufus is another popular option. If one does not work for you, consider the other.

3. A USB thumb drive, 4GB or larger. (Systems with a direct SD card slot can use an SD card with similar capacity. The procedure is identical.)

## Kali Linux Live USB Install Procedure

The specifics of this procedure will vary depending on whether you're doing it on a [Windows](https://www.kali.org/docs/usb/kali-linux-live-usb-install/), [Linux](https://www.kali.org/docs/usb/kali-linux-live-usb-install-linux/), or [macOS/OS X](https://www.kali.org/docs/usb/kali-linux-live-usb-install-macos/) system.

#### Creating a Bootable Kali USB Drive on Windows (Etcher)

1. Plug your USB drive into an available USB port on your Windows PC, note which drive designator (e.g. "F:\") it uses once it mounts, and launch Etcher.

2. Choose the Kali Linux ISO file to be imaged with "select image" and verify that the USB drive to be overwritten is the correct one. Click the "Flash!" button once ready.

![kali-usb-install-windows](kali-usb-install-windows.png)
3. Once Etcher alerts you that the image has been flashed, you can safely remove the USB drive and proceed to boot into Kali with it.
