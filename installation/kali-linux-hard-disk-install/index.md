---
title: Single Boot Kali Linux
description:
icon:
type: post
weight: 120
author: ["gamb1t",]
---

Installing Kali Linux on your computer is an easy process. First, you'll need compatible computer hardware. Kali Linux is supported on **i386**, **amd64**, and **ARM** (both armel and armhf) platforms. The hardware requirements are minimal as listed below, although better hardware will naturally provide better performance.

The i386 images have a default [PAE](http://en.wikipedia.org/wiki/Physical_Address_Extension) kernel, so you can run them on systems with over 4GB of RAM. 

Download the [Kali Linux Installer image](/docs/introduction/download-official-kali-linux-images/) and either burn the ISO to DVD, or [prepare a USB stick with Kali Linux](/docs/usb/kali-linux-live-usb-install/) as the installation medium. If you do not have a DVD drive or USB port on your computer, check out the [Kali Linux Network Install](/docs/installation/kali-linux-network-pxe-install/).

#### Installation Prerequisites

The installation requirements for Kali Linux vary depending on what you would like to install.

- On the low end, you can set up Kali as a basic Secure Shell (SSH) server with no desktop, using **as little as 128 MB of RAM (512 MB recommended)** and **2 GB of disk space**.
- On the higher end, if you opt to install the default Xfce4 desktop and the `kali-linux-default` [metapackage](/docs/general-use/metapackages/), you should really aim for **at least 2048 MB of RAM** and **20 GB of disk space**.
- CD-DVD Drive / USB boot support

### Preparing for the Installation

1. [Download Kali Linux](/docs/introduction/download-official-kali-linux-images/) (We recommend the image marked (Installer)[*](/docs/introduction/what-image-to-download/#which-image-to-choose) .
2. Burn The Kali Linux ISO to DVD or [Image Kali Linux Live to USB](/docs/usb/kali-linux-live-usb-install/).
3. Ensure that your computer is set to boot from CD / USB in your BIOS.

### Kali Linux Installation Procedure

1. To start your installation, boot with your chosen installation medium. You should be greeted with the Kali Boot screen. Choose either _Graphical_ or _Text-Mode_ install. In this example, we chose a graphical install.

    ![01-install-select](kali-default-install-18.png)

2. Select your preferred language.

    ![02-language-select](kali-default-install-17.png)

3. Specify your geographic location.

    ![03-location](kali-default-install-16.png)

4. Select your keyboard layout.

    ![03-location](kali-default-install-15.png)

5. The installer will copy the image to your hard disk, probe your network interfaces, and then prompt you to enter a hostname for your system. In the example below, we've entered "kali" as our hostname.

    ![05-hostname](kali-default-install-14.png)

6. You may optionally provide a default domain name for this system to use.

    ![06-domain](kali-default-install-13.png)

7. Next, create the user account for the system.

    ![01-user](kali-user-1.png)
    ![02-user](kali-user-2.png)
    ![03-user](kali-user-3.png)

8. Next, set your time zone.

    ![09-timezone](kali-default-install-11.png)

9. The installer will now probe your disks and offer you four choices. In our example, we're using the entire disk on our computer and not configuring LVM (logical volume manager). Experienced users can use the "Manual" partitioning method for more granular configuration options.

    ![10-partitionmethod](kali-default-install-10.png)

10. Select the disk to be partitioned.

    ![11-selectdisk](kali-default-install-9.png)

11. Depending on your needs, you can choose to keep all your files in a single partition — the default — or to have separate partitions for one or more of the top-level directories. If you're not sure which you want, you want "All files in one partition".

    ![12-partitioningscheme](kali-default-install-8.png)
    ![12-partitioningscheme2](kali-default-install-7.png)

12. Next, you'll have one last chance to review your disk configuration before the installer makes irreversible changes. After you click _Continue_, the installer will go to work and you'll have an almost finished installation.

    ![13-finish-partitioning](kali-default-install-6.png)

13. Kali uses a central repository to distribute applications. You'll need to enter any appropriate proxy information as needed.

    ![14-networkmirror](kali-default-install-5.png)

14. Next you can select which metapackages you would like to install. The default selections will install a standard Kali Linux system and you don't really have to change anything here.
    Please [refer to this guide](/docs/introduction/what-image-to-download/#which-desktop-environment-and-software-collection-to-choose-during-installation) if you prefer to change the default selections.
    The 2020.1 release images and the current weekly images differ in the selection layout.
    The 2020.1 release provides the following selection screen:

    ![15-packageselection](kali-default-packages.png)

    The current weekly images provide the following software selection screen:

    ![15-packageselection-weekly](kali-default-packages-weekly1.png)

    Note: A working Internet connection may be required to obtain the full list of options as displayed above. The installer will only display packages that are available during installation so you may see a condensed version as shown below if no Internet connection is detected:

    ![15-packageselection-nonet](kali-default-packages-nonet.png)

    The actual list may differ based on the image chosen for the installation, as thay mey contain different sets of packages.

15. Next confirm to install the GRUB boot loader...

    ![16-install-grub](kali-default-install-3.png)

16. ... and select the hard drive to install the GRUB bootloader in.

    ![16b-install-grub](kali-default-install-2.png)

17. Finally, click Continue to reboot into your new Kali installation.

    ![17-install-complete](kali-default-install-1.png)

## Post Installation

Now that you've completed installing Kali Linux, it's time to customize your system. The Kali General Use section of our site has more information and you can also find tips on how to get the most out of Kali in our [User Forums](https://forums.kali.org/).
