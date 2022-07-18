---
title: Raspberry Pi Zero W P4wnP1 A.L.O.A
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

## Quick install and usage:

1. [Download image from here](/get-kali/#kali-arm) and write to microSD card
2. Insert card into Raspberry Pi Zero W
3. Connect: OTG adapter in smartphone, standard cable in Pi-Tail (power) to power up Pi-Tail
4. Connect to wireless network "💥🖥💥 Ⓟ➃ⓌⓃ🅟❶" with password "MaMe82-P4wnP1"
4. Connect to the P4wnP1 A.L.O.A. 
   a) Go to http://172.24.0.1:8000 in your web browser
   b) ssh to 172.24.0.1 with the username "root" and password "toor"
5. Go wild

- - -

## How it works:

The RaspberryPi Zero W P4wnP1 A.L.O.A. image is a highly customized version of Kali, running the P4wnP1 A.L.O.A software which includes a number of features such as Plug & Play USB device emulation, HIDScript which is similar to DuckyScript for payloads, Bluetooth support, and WiFi which includes a modified copy of the Nexmon firmware which allows for KARMA attacks, WiFi covert channel, and while monitor mode is included, it is NOT supported. 

You can configure the P4wnP1 A.L.O.A via CLI or webclient.  The CLI is written in Go, so it is able to be run anywhere that Go supports.  You can compile the CLI from the git sources at https://github.com/RoganDawes/P4wnP1_aloa

Passwords:  
ssh: root / toor  
  
Default Wi-Fi: P4wnP1

You can find more info at [README](https://github.com/RoganDawes/P4wnP1_aloa/blob/master/README.md)  

- - -

Problems, questions, feedback? Join us in the forums:  
https://forums.kali.org/  

