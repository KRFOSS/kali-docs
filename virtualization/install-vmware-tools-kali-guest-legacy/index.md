---
title: VMware Tools for a Kali Guest (Legacy)
description:
icon:
date: 2020-01-10
type: archived
weight: 15
author: ["g0tmi1k",]
tags: ["",]
keywords: ["",]
og_description:
---

This page is dated. You can find the latest version here: [https://www.kali.org/docs/virtualization/install-vmware-tools-kali-guest/](/docs/virtualization/install-vmware-tools-kali-guest/).

The latest version of vmware-tools at this date compiles against our kernel, albeit with several warnings. We utilise a set of vmware-tool patches to facilitate the installation.

```markdown
cd ~/
apt install -y git gcc make linux-headers-$(uname -r)
git clone https://github.com/rasa/vmware-tools-patches.git
cd vmware-tools-patches/
```

Next, mount the VMware tools ISO by clicking "Install VMware Tools" from the appropriate menu. Once the VMware Tools ISO has been attached to the virtual machine, copy the installer to the _downloads_ directory and then run the installer script :

```markdown
cd ~/vmware-tools-patches/
cp /media/cdrom/VMwareTools-9.9.0-2304977.tar.gz downloads/
./untar-and-patch-and-compile.sh
```
