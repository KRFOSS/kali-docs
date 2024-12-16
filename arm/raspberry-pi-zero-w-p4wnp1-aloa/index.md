---
title: Raspberry Pi Zero W P4wnP1 A.L.O.A
description:
icon:
weight:
author: ["steev",]
---

## Introduction

The Raspberry Pi Zero W P4wnP1 A.L.O.A. (**A** **L**ittle **O**ffensive **A**pplication) image is a highly customized version of Kali Linux. It allows you to connect the Raspberry Pi to a computer, and send commands, or use its networking, all without having to interact with it, although you can do that too!

The P4wnP1 A.L.O.A software includes a number of [features](#features) that the [original P4wnP1](https://p4wnp1.readthedocs.io/en/latest/) had such as Plug & Play USB device emulation, and Wi-Fi via a modified copy of the [Nexmon](https://github.com/seemoo-lab/nexmon) firmware which allows for KARMA attacks, Bluetooth support, Wi-Fi covert channel, and while monitor mode is included, it is **NOT** supported, but also adds HIDScript which is similar to [DuckyScript](https://github.com/hak5darren/USB-Rubber-Ducky/wiki/Duckyscript) for payloads but based on JavaScript.

{{% notice info %}}
Currently the P4wnP1 A.L.O.A. image **only** supports the Raspberry Pi Zero W, **not** Zero 2 W.
<!-- There is support for Nexmon for Raspberry Pi Zero 2 W, but it doesn't work with Wi-Fi covert channel support. -->
{{% /notice %}}

## Quick install and usage

1. Download _and validate_ the `Kali Linux Raspberry Pi Zero W P4wnP1 ALOA` image from the [downloads](/get-kali/#kali-arm) area. The process for validating an image is described in more detail on [Downloading Kali Linux](/docs/introduction/download-official-kali-linux-images/).

2. Use the **[dd](https://manpages.debian.org/testing/coreutils/dd.1.en.html)** utility to image this file to your microSD card. In our example, we use a microSD which is located at `/dev/sdX`. **_Change this as needed._**

{{% notice info %}}
This process will wipe out your microSD card. If you choose the wrong storage device, you may wipe out your computers hard disk.
{{% /notice %}}

```console
$ xzcat kali-linux-2024.4-raspberry-pi-zero-w-p4wnp1-aloa-armel.img.xz | sudo dd of=/dev/sdX bs=4M status=progress
```

This process can take a while, depending on your PC, your microSD card's speed, and the size of the Kali Linux image.

3. Once the _dd_ operation is complete, plug the microSD card into your Raspberry Pi Zero W.

4. From another computer, connect to the default wireless network `💥🖥💥 Ⓟ➃ⓌⓃ🅟❶` of P4wnP1 A.L.O.A. with the password `MaMe82-P4wnP1`

5. Once you are connected to the P4wnP1 A.L.O.A. wireless network, you can access the system via either:

- The web interface (<http://172.24.0.1:8000>)
- Command line using SSH (`ssh kali@172.24.0.1`), then run the `P4wnP1_cli` command
- Locally compile the P4wnP1 A.L.O.A. CLI software, and pass `host` along with your commands

{{% notice info %}}
One of the important customizations of this Kali Linux image, is that both the `root` and `kali` users can SSH in.
The `root` user has the [default password](/docs/introduction/default-credentials/) of `toor`.
{{% /notice %}}

6. Go wild

## Features

P4wnP1 A.L.O.A. by MaMe82 is a framework which turns a Raspberry Pi Zero W into a flexible, low-cost platform for pentesting, red teaming and physical engagements... or into "A Little Offensive Appliance".

P4wnP1 A.L.O.A. is not meant to:

- Be a "weaponized" tool
- Provide RTR payloads, which could be carried out by everybody, without understanding what's going on or which risks are involved

P4wnP1 A.L.O.A. is meant to:

- Be a flexible, low-cost, pocket sized platform
- Serve as enabler for tasks like the one described here
- Support prototyping, testing and carrying out all kinds of USB related tasks, commonly used during pentest or redteam engagements, without providing a finalized static solution

 P4wnP1 A.L.O.A. provides a configuration, which utilizes the given components to do the following things:

- Drive-by against Windows hosts in order to deliver in-memory client code to download stage2 via HID covert channel, based on keystroke injection (HIDScript)
- Starting the keystroke injection, as soon as P4wnP1 is connected to a USB host (TriggerAction issuing HIDScript)
- Bring up the stager, which delivers the Wi-Fi covert channel client agent via HID covert channel, as soon as the keystroke injection starts (TriggerAction running a bash script, which again starts the external server)
- Bring up the Wi-Fi covert channel server, when needed (same TriggerAction and BashScript)
- Deploy a USB setup which provides a USB keyboard (to allow keystroke injection) and an additional raw HID device (serves as covert channel for stage2 delivery) - the USB settings are stored in a settings template
- Deploy a Wi-Fi setup, which allows remote access to P4wnP1, in order to allow interaction with the CLI frontend of the Wi-Fi covert channel server - the Wi-Fi settings are stored in a settings template
- Provide a single point of entry, to deploy all the needed configurations at once (done by a Master Template, which consists of proper Wi-Fi settings, proper USB settings and the TriggerActions needed to start the HIDScript)

The best place for up to date information is on the project's [README](https://github.com/RoganDawes/P4wnP1_aloa/blob/master/README.md).

If you need a reminder of the [default passwords](/docs/introduction/default-credentials/) again:

- SSH:
   - `root`/`toor`
   - `kali`/`kali`

- Wi-Fi:
   - SSID: `💥🖥💥 Ⓟ➃ⓌⓃ🅟❶`
   - PSK: `MaMe82-P4wnP1`

- - -

Problems, questions, feedback? Join us in the [forums](https://forums.kali.org/)
