---
title: Hyper-V for a Kali Guest
description:
icon:
date: 2020-02-11
type: post
weight: 25
author: ["mimura1133",]
tags: ["",]
keywords: ["",]
og_description:
---

If you run Kali Linux as a "guest" within Hyper-V, this article will help you to enable the "Enhanced Session Mode".

Enhanced Session Mode lets Hyper-V connect to virtual machines using RDP (remote desktop protocol) ,and improve your virtual machine viewing experience.

![kali-hyper-v-enhancedmode](kali-hyper-v-enhancedmode.png)

## Execute the install script on Kali Linux.

Start up your Kali Linux virtual machine, open a terminal window and issue the following commands.

```markdown
git clone https://github.com/mimura1133/linux-vm-tools
chmod 555  linux-vm-tools/kali/2020.x/install.sh
sudo linux-vm-tools/kali/2020.x/install.sh
```

![kali-hyperv-step1](kali-hyperv-step1.png)

the script will install xrdp and modify the configuration file.

if the script says "Reboot your machine to begin using XRDP", please shutdown the vm and close the window of "Virtual Machine Connection".

## Changing the Setting of the Virtual Machine.

You need to change the transport type to HVSocket from VMBus.

Open a PowerShell with Administrator on the Host, issue the following commands.

```markdown
Set-VM "(YOUR VM NAME HERE)" -EnhancedSessionTransportType HVSocket
```

![kali-hyperv-step2](kali-hyperv-step2.png)

start the virtual machine again, check if the screen by a xrdp has appeared.

![kali-hyperv-step3](kali-hyperv-step3.png)
