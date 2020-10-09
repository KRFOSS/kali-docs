---
title: Mini ISO Install
description:
icon:
type: archived
weight: 410
author: ["gamb1t",]
---

**This image is dated**. We would now recommend using [Kali Linux "NetInstaller" images](https://www.kali.org/downloads/) instead.

The reasoning behind favouring NetInstaller is, when the [Kali Linux kernel](https://pkg.kali.org/pkg/linux) gets updated, it breaks the mini ISO image. This means you will have to re-download the mini image again.

The NetInstaller image has as it has the kernel pre-loaded, meaning the image does not break but is a larger size.

- - -

The Kali Linux "Mini ISO" is a convenient way to install a Kali Linux system which is always up-to-date. The mini install ISO will download all required packages from our repositories every time you install Kali Linux. This means you will always get the latest version of each package straight after installing. However, you will need to have a fast Internet connection to use this installation method.

### Installation Prerequisites

This guide will make the following assumptions when following:

- You have read our [single boot Kali Linux install guide](/docs/installation/hard-disk-install/), as this has the same Installation Prerequisites (System requirements, setup assumptions and install media).
- Downloaded the Kali Linux Mini ISO
	- [x64 Graphical Installer](http://http.kali.org/kali/dists/kali-rolling/main/installer-amd64/current/images/netboot/gtk/mini.iso)
	- [x86 Graphical Installer](http://http.kali.org/kali/dists/kali-rolling/main/installer-i386/current/images/netboot/gtk/mini.iso)
    - [x64 Text Installer](http://http.kali.org/kali/dists/kali-rolling/main/installer-amd64/current/images/netboot/mini.iso)
    - [x86 Text Installer](http://http.kali.org/kali/dists/kali-rolling/main/installer-i386/current/images/netboot/mini.iso)

### Kali Linux Installation Procedure

The installation procedure is the same as our [Kali Linux Hard Disk install](/docs/installation/hard-disk-install/) guide.

### Post Installation

Now that you've completed installing Kali Linux, it's time to customize your system.

The [General Use section](/docs/general-use/) has more information and you can also find tips on how to get the most out of Kali Linux in our [User Forums](https://forums.kali.org/).
