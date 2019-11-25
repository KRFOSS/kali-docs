---
title: Installing NetHunter On the Gemini PDA
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

## Installing NetHunter On the Gemini PDA

![](NetHunter-Gemini_tiny.jpg)

# From unpacking to running NetHunter in 4 steps:

1. Flash stock rooted Android image
2. Run Magisk Manager to finish the rooting process
3. Install TWRP recovery
4. Install NetHunter

## 1. Flash stock rooted Android
The Gemini PDA ships with a non-rooted Android image which needs to be replaced.
Either install the "Pentester Pro" image with rooted Android and Kali Linux partitions as detailed here: [Kali Linux Gemini PDA](/docs/arm/kali-linux-gem-pda)

~~or create your own image if you prefer a different partition layout:
[http://support.planetcom.co.uk/index.php/Linux_Flashing_Guide](http://support.planetcom.co.uk/index.php/Linux_Flashing_Guide)~~
**Note:** The official partition tool does not offer a rooted android image at the time of writing (26/02/2019). The image advertised as rooted is the same as the non-rooted version.

Reboot the newly imaged Gemini into Android and:
* Configure the keyboard
* Connect to wifi
* Open the "Play Store" app and sign in with your google account
* Update all apps


## 2. Run Magisk Manager to finish the rooting process
* Run the "Magisk Manager" app and follow the prompts to update the app

Sometimes the magisk version shipped with an android image is not compatible with the latest version of the magisk manager which will require a little workaround.
If you are prompted with an error message that "Magisk Manager" is incompatible with the installed version of "Magisk", downgrade the manager app, upgrade magisk via TWRP, and upgrade the manager app again like this:
* uninstall the existing Magisk Manager
* download Magisk Manager v6.1.0 from [https://github.com/topjohnwu/Magisk/releases](https://github.com/topjohnwu/Magisk/releases)
* goto "security" settings and turn on "allow installation of apps from unkown sources"
* install magisk manager apk from the download folder
* open Magisk Manager, say "no" to update
* disable "Check Updates" in "Settings"
* exit Magisk Manager
* ~~Download the latest version of "Magisk" (not "Magisk Manager") from [https://github.com/topjohnwu/Magisk/releases](https://github.com/topjohnwu/Magisk/releases)~~
* ~~Continue with the installation of TWRP and install the new magisk version via TWRP when convenient. Once installed open the "Magisk Manager" app and follow the prompts to update the app~~
Note: Newer versions of Magisk seem to be breaking auto rotation. Let's stick with the previous version for now.

## 3. Install TWRP recovery
* install the "Official TWRP App" from Playstore
* open the "Official TWRP App"
* select your account
* tick "I agree", "Run with root permissions"
* press "OK"
* press "TWRP Flash"
* press "ok when prompted for allowing root access
* If superuser permissions are not granted automatically, open Magisk Manager and enable TWRP manually
* Select device "Planet Gemini PDA -- geminipda"
* select latest version
* download image
* go back
* select image
* Flash to recovery

## 4. Install NetHunter
* Download the NetHunter beta image from here:
[http://http.re4son-kernel.com/downloads/gemini-nethunter/](http://http.re4son-kernel.com/downloads/gemini-nethunter/)
* connect the Gemini PDA to your computer
* transfer the NetHunter image to the Gemini PDA
* reboot the Gemini PDA into recovery via the "Official TWRP App" (TWRP Flash->Menu->Reboot-Reboot Recovery)
* press "Install"
* select the NetHunter image
* swipe to confirm flash
* reboot
* start the "NetHunter" app
* click "allow" seven times and allow root access
* let setup finish
* reboot

### Enjoy Kali NetHunter on the Gemini PDA

## Current status
* HID attacks are not yet supported. The drivers are still being worked on.
* SearchSploit is not fully working yet.

Please help with the development by submitting issues and pull requests. We much appreciate it.
