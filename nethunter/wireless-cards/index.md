---
title: Wireless Cards and NetHunter
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

External wireless cards are necessary because Android devices do not support monitor mode on most devices. There are some devices that can support monitor mode with a modified firmware and kernel such as the Nexus 5, 7 (2012), and Nexus 6P. Right now, only a specially modified version of Nexus 5 supports monitor mode for Nethunter.

A couple of limitations are that Android devices require a USB-OTG cable and the power output is limited. Because of these limitations, not all wireless cards can receive the necessary power output and may not have external power (y-cable) support.

When asking the question "What is the best card for use with NetHunter?", you need to ask yourself what your use case is.
While all cards will likely perform similar at closer ranges, some of them have increased transmit power and antenna attachments which allow them to work at longer distances than small form factor cards.
There is also the possibility that your device may only provide 450 or less mA of power over OTG rather than the full USB 500 mA specification. If this is the case, you may want to consider devices with lower transmit power.

**The following chipsets are supported by default in most, if not all, NetHunter kernels:**

Atheros
* ATH9KHTC (AR9271, AR7010)

Ralink
* RT3070

Realtek
* RTL8192CU

**The following devices are confirmed to be working with a NetHunter build:**
* TP-Link TL-WN722N v1 (Please note that v2 & v3 have unsupported chipsets)
* TP-Link TL-WN822N v1 - v3
* Alfa Networks AWUS036NEH (recommended by @jcadduono)
* Alfa Networks AWUS036NHA
* Alfa Networks AWUSO36NH
* Panda PAU05 Nano

**The following devices are confirmed to be partially working with a NetHunter build:**
* Alfa Networks AWUS051NH (dual band 5 GHz support may be unreliable)

**The following devices are confirmed to NOT be working with a NetHunter build:**
* TP-Link TL-WN822N v4
