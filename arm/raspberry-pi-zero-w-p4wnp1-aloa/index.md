---
title: Raspberry Pi Zero W P4wnP1 A.L.O.A (A Little Offensive Application)
description:
icon:
weight:
author: ["steev",]
build-script: https://gitlab.com/kalilinux/build-scripts/kali-arm/-/blob/master/raspberry-pi-zero-w-p4wnp1-aloa.sh
headless: none
metapackage: none
status: pre-generated
cpu: BCM2835
cores: 1
gpu: "Broadcom VideoCore IV"
ram: DDR3
ram-size: 512MB
ethernet: no
wifi: "2.4GHz/5GHz"
bluetooth: yes
usb3: no
usb2: 1
storage: sdcard
kernel: custom
---

# P4wnP1 A.L.O.A

## Introduction

The RaspberryPi Zero W P4wnP1 A.L.O.A. (A Little Offensive Application) image is a highly customized version of Kali Linux.  It is the successor to the [P4wnP1](https://p4wnp1.readthedocs.io/en/latest/) project.  The original P4wnP1 was written in bash scripts, and P4wnP1 A.L.O.A. is its continuation, rewritten in Go, although you can still use bash scripts with it as well.  It allows you to connect the RaspberryPi to a computer, and send commands, or use its networking, all without having to interact with it, athough you can do that too!

The P4wnP1 A.L.O.A software includes a number of features that the original P4wnP1 had such as Plug & Play USB device emulation, and WiFi via a modified copy of the Nexmon firmware which allows for KARMA attacks, WiFi covert channel, and while monitor mode is included, it is **NOT** supported, but also adds HIDScript which is similar to [DuckyScript](https://github.com/hak5darren/USB-Rubber-Ducky/wiki/Duckyscript) for payloads but based on JavaScript, and Bluetooth support.


- - -

## Quick install and usage:

1. Download _and validate_ the `Kali Linux Raspberry Pi Zero W P4wnP1 ALOA` image from the [downloads](https://www.offensive-security.com/kali-linux-arm-images/) area. The process for validating an image is described in more detail on [Downloading Kali Linux](/docs/introduction/download-official-kali-linux-images/).
2. Use the **[dd](https://packages.debian.org/testing/dd)** utility to image this file to your microSD card. In our example, we use a microSD which is located at `/dev/sdb`. **_Change this as needed._**

{{% notice info %}}
This process will wipe out your microSD card. If you choose the wrong storage device, you may wipe out your computers hard disk.
{{% /notice %}}

```console
$ xzcat kali-linux-2022.2-raspberry-pi-zero-w-p4wnp1-aloa-armel.img.xz | sudo dd of=/dev/sdb bs=4M status=progress
```

This process can take a while, depending on your PC, your microSD card's speed, and the size of the Kali Linux image.

3. Once the _dd_ operation is complete, plug the microSD card into your RaspberryPi Zero W.

4. From another computer, connect to the default wireless network `💥🖥💥 Ⓟ➃ⓌⓃ🅟❶` of P4wnP1 A.L.O.A. with the password `MaMe82-P4wnP1`

5. Once you are connected to the P4wnP1 A.L.O.A. wireless network, you can access the system via the web interface, you can ssh in, or if you have built the P4wnP1 command line client for your host system, you can use it as well.
   - Go to http://172.24.0.1:8000 in your web browser
   - ssh to 172.24.0.1, and you should be able to [log in to Kali](/docs/introduction/default-credentials/) and then run the `P4wnP1_cli` command
   - Compile the P4wnP1 A.L.O.A. CLI software for your host system, and pass `host` along with your commands 

{{% notice info %}}
One of the important customizations of this Kali Linux image, is that both the `root` and `kali` users can ssh in.
The `root` user has the default password of `toor`.
{{% /notice %}}

6. Go wild


- - -

## How it works:

P4wnP1 A.L.O.A. by MaMe82 is a framework which turns a Rapsberry Pi Zero W into a flexible, low-cost platform for pentesting, red teaming and physical engagements ... or into "A Little Offensive Appliance".

P4wnP1 A.L.O.A. is not meant to:

   - be a "weaponized" tool
   - provide RTR payloads, which could be carried out by everybody, without understanding what's going on or which risks are involved

P4wnP1 A.L.O.A. is meant to:

   - be a flexible, low-cost, pocket sized platform
   - serve as enabler for tasks like the one described here
   - support prototyping, testing and carrying out all kinds of USB related tasks, commonly used during pentest or redteam engagements, without providing a finalized static solution

 P4wnP1 A.L.O.A. provides a configuration, which utilizes the given components to do the following things:

   - drive-by against Windows hosts in order to deliver in-memory client code to download stage2 via HID covert channel, based on keystroke injection (HIDScript)
   - starting the keystroke injection, as soon as P4wnP1 is connected to a USB host (TriggerAction issuing HIDScript)
   - bring up the stager, which delivers the WiFi covert channel client agent via HID covert channel, as soon as the keystroke injection starts (TriggerAction running a bash script, which again starts the external server)
   - bring up the WiFi covert channel server, when needed (same TriggerAction and BashScript)
   - deploy a USB setup which provides a USB keyboard (to allow keystroke injection) and an additional raw HID device (serves as covert channel for stage2 delivery) - the USB settings are stored in a settings template
   - deploy a WiFi setup, which allows remote access to P4wnP1, in order to allow interaction with the CLI frontend of the WiFi covert channel server - the WiFi settings are stored in a settings template
   - provide a single point of entry, to deploy all the needed configurations at once (done by a Master Template, which consists of proper WiFi settings, proper USB settings and the TriggerActions needed to start the HIDScript)


The best place for up to date information is on the project's [README](https://github.com/RoganDawes/P4wnP1_aloa/blob/master/README.md) on Github.  

Passwords:  
ssh: 
   - `root` / `toor`

   - `kali` / `kali`
  
Wi-Fi: 
   - `MaMe82-P4wnP1`


- - -

Problems, questions, feedback? Join us in the forums:  
https://forums.kali.org/