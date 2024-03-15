---
title: Fixing Dual Boot
description:
icon:
weight: 12
author: ["arnaudr"]
---

This page explains how to fix Dual Boot, in the particular case where the GRUB boot menu only allows you to boot Kali Linux, and doesn't show entries for other operating systems.

## Assumptions

This guide assumes that you have Kali Linux installed alongside another operating system. In this setup, when you boot your machine, you're supposed to see the GRUB boot menu (GRUB is the bootloader installed by Kali Linux). This menu lists the different operating systems that are installed, so that you can select which one to boot.

This guide also assumes that the GRUB bootloader was installed by Kali Linux. This is easy to see, as in this case the Kali Linux theming is applied to the GRUB menu. Note that if you run Kali alongside another Linux distribution, it's possible that the GRUB bootloader was installed by this _other_ distribution, and in this case this guide will not help you.

Finally, for the purpose of this guide, we assume that the _other operating system_ is Debian. In practice, it doesn't matter, it could be any other operating system, but we need to pick one for the text and the screenshots.

## Troubleshooting and fixing

Let's assume that one day, when you boot your machine, you only see the Kali Linux entries in the GRUB boot menu, and you don't see any entry for the other operating system (Debian in this case). Those entries mysteriously disappeared, and now you can only boot Kali.

The GRUB boot menu looks more or less like the screenshot below:

![](grub-boot-menu-1.png)

In order to fix it, let's boot Kali Linux. Then open a root terminal, and edit the file `/etc/default/grub`. Find the line `GRUB_DISABLE_OS_PROBER=false` and make sure it's NOT commented out (ie. it doesn't start with a hash):

```console
kali@kali:~$ cat /etc/default/grub | grep GRUB_DISABLE_OS_PROBER
#GRUB_DISABLE_OS_PROBER=false
kali@kali:~$
kali@kali:~$ sudo sed -i 's/#GRUB_DISABLE_OS_PROBER=false/GRUB_DISABLE_OS_PROBER=false/' /etc/default/grub
kali@kali:~$
kali@kali:~$ cat /etc/default/grub | grep GRUB_DISABLE_OS_PROBER
GRUB_DISABLE_OS_PROBER=false
kali@kali:~$
```

Then run the command `update-grub`. The output should look like that:

```console
kali@kali:~$ sudo update-grub
Generating grub configuration file ...
Found theme: /boot/grub/themes/kali/theme.txt
Found background image: /usr/share/images/desktop-base/desktop-grub.png
Found linux image: /boot/vmlinuz-6.6.9-amd64
Found initrd image: /boot/initrd.img-6.6.9-amd64
Warning: os-prober will be executed to detect other bootable partitions.
Its output will be used to detect bootable binaries on them and create new boot entries.
Found Debian GNU/Linux 12 (bookworm) on /dev/sda2
Adding boot menu entry for UEFI Firmware Settings ...
done
kali@kali:~$
```

The lines that matter in the screenshot above are:
- « _os-prober will be executed to detect other bootable partitions_ » which means that the command attempts to find os operating systems.
- « _Found Debian GNU/Linux 12 (bookworm) on ..._ » which means that the other operating system (Debian in this case) was found. 

Assuming that all went well, the problem is fixed! Just reboot your machine, and this time the GRUB boot menu shows entries for both Kali Linux and Debian:

![](grub-boot-menu-2.png)
