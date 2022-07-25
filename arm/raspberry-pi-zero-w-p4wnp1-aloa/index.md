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

{{% notice info %}}
This documetation is still a work in progress.  The P4wnP1 A.L.O.A. packs a lot of punch in a very tiny package.
{{% /notice %}}

# P4wnP1 A.L.O.A

## Introduction

The RaspberryPi Zero W P4wnP1 A.L.O.A. (A Little Offensive Application) image is a highly customized version of Kali Linux.  It is the successor to the [P4wnP1](https://p4wnp1.readthedocs.io/en/latest/) project.  The original P4wnP1 was written in bash scripts, and P4wnP1 A.L.O.A. has been rewritten in Go.

The P4wnP1 A.L.O.A software includes a number of features that the original P4wnP1 had such as Plug & Play USB device emulation, and WiFi via a modified copy of the Nexmon firmware which allows for KARMA attacks, WiFi covert channel, and while monitor mode is included, it is **NOT** supported, but also adds HIDScript which is similar to [DuckyScript](https://github.com/hak5darren/USB-Rubber-Ducky/wiki/Duckyscript) for payloads and based on JavaScript, and Bluetooth support.

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



You can configure the P4wnP1 A.L.O.A via CLI or webclient.  The CLI is written in Go, so it is able to be run anywhere that Go supports.  You can compile the CLI from the git sources at https://github.com/RoganDawes/P4wnP1_aloa

Passwords:  
ssh: 
      `root` / `toor`
      `kali` / `kali`
  
Default Wi-Fi password: `MaMe82-P4wnP1`

You can find more info at the project's [README](https://github.com/RoganDawes/P4wnP1_aloa/blob/master/README.md) on Github.  

- - -

Problems, questions, feedback? Join us in the forums:  
https://forums.kali.org/  

