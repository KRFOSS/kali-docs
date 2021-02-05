---
title: VMware Tools for a Kali Guest (Legacy)
description:
icon:
type: archived
weight: 300
author: ["g0tmi1k",]
---

**This page is dated**. You can find the **latest version** here: [https://www.kali.org/docs/virtualization/install-vmware-tools-kali-guest/](/docs/virtualization/install-vmware-guest-tools/).

- - -

As of September 2015, **VMware [recommends](https://blogs.vmware.com/vsphere/2015/09/open-vm-tools-ovt-the-future-of-vmware-tools-for-linux.html) using the distribution-specific [open-vm-tools](https://packages.debian.org/testing/open-vm-tools) (OVT)** instead of the VMware Tools package for guest machines (as in this guide).

The latest version of VMwareTools at this date does compiles against [our kernel](https://pkg.kali.org/pkg/linux), albeit with several warnings. We utilise a set of VMwareTools patches to facilitate the installation.

## Preparing

First, we need to make sure to install any package and also make a local copy of the Git repository.

```console
kali@kali:~$ sudo apt update
...
kali@kali:~$
kali@kali:~$ sudo apt install -y git gcc make linux-headers-$( uname -r )
...
kali@kali:~$
kali@kali:~$ sudo git clone https://github.com/rasa/vmware-tools-patches.git /opt/vmware-tools-patches/
...
kali@kali:~$
```

## Patching & Installing

Next, mount the VMware tools ISO by clicking "**Install VMware Tools**" from the appropriate menu in VMware's Window.

Once the VMware Tools ISO has been attached to the Virtual Machine, copy the installer to the **downloads** directory and then run the installer script from the Git repository.

```console
kali@kali:~$ sudo mkdir -p /media/cdrom/
kali@kali:~$
kali@kali:~$ sudo mount /dev/cdrom /media/cdrom/
kali@kali:~$
kali@kali:~$ cp -f /media/cdrom/VMwareTools-*.tar.gz ~/downloads/
kali@kali:~$
kali@kali:~$ cd /opt/vmware-tools-patches/
kali@kali:/opt/vmware-tools-patches$ ./untar-and-patch-and-compile.sh
kali@kali:/opt/vmware-tools-patches$
```

Afterwards, reboot Kali Linux and when it starts back up, VMware Tools should be now functioning.
