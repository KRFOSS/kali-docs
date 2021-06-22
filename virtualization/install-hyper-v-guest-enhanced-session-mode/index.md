---
title: Installing Hyper-V Enhanced Session Mode (Guest Tools)
description:
icon:
weight: 320
author: ["mimura1133",]
---

Installing "Guest VM Packages", gives a better user experience with VMs in general. This is why since Kali Linux 2019.3, during the [setup process](https://gitlab.com/kalilinux/build-scripts/live-build-config/-/blob/master/simple-cdd/profiles/offline.downloads) it should **detect if Kali Linux is inside a VM**. If it is, then **automatically install any additional tools** (in Hyper-V's case, `hyperv-daemons`). However, unlike [VMware](/docs/virtualization/install-vmware-guest-tools/) and [VirtualBox](/docs/virtualization/install-virtualbox-guest-additions/), more still can be done to improve the experience afterwards. This is because Hyper-V can connect to Virtual Machines using **Remote Desktop Protocol** (RDP).

This article will help you to enable the **[Enhanced Session Mode](https://techcommunity.microsoft.com/t5/virtualization/sneak-peek-taking-a-spin-with-enhanced-linux-vms/ba-p/382415)**, which opens up the possibility of clipboard sharing and windows resizing.

![](kali-hyper-v-enhancedmode.png)

## Install

Start up your Kali Linux virtual machine, open a terminal window and issue the following commands.

```console
kali@kali:~$ sudo git clone https://github.com/mimura1133/linux-vm-tools /opt/linux-vm-tools
...
kali@kali:~$
kali@kali:~$ sudo chmod 0755 /opt/linux-vm-tools/kali/2020.x/install.sh
kali@kali:~$
kali@kali:~$ sudo /opt/linux-vm-tools/kali/2020.x/install.sh
...
kali@kali:~$
kali@kali:~$ sudo reboot -f
kali@kali:~$
```

- - -

The script will install [xrdp](https://packages.debian.org/testing/xrdp) and modify the configuration files.

If the [script](https://github.com/mimura1133/linux-vm-tools/blob/master/kali/2020.x/install.sh) says "**Reboot your machine to begin using XRDP**", please **shutdown the VM and close the window** of "Virtual Machine Connection".

## Changing the Setting of the Virtual Machine

You now need to change the **transport type** from VMBus to **HVSocket**.

Open a PowerShell (with Administrator privileges) on the host, issue the following command.

```console
> Set-VM "(YOUR VM NAME HERE)" -EnhancedSessionTransportType HVSocket
```

![](kali-hyperv-step2.png)

- - -

We can test to see if it been a success by starting the virtual machine again, and check if you can see the following screen when trying to use xrdp.

![](kali-hyperv-step3.png)
