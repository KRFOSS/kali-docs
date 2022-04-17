---
title: Custom MK/SS808 Image
description:
icon:
weight:
author: ["steev",]
---

The following document describes our own method of creating a **custom Kali Linux MK/SS808 ARM image** and is targeted at developers. If you would like to install a pre-made Kali image, check out our [Install Kali on MK/SS808](/docs/arm/ss808-mk808/) article.

{{% notice info %}}
You'll need to have root privileges to do this procedure, or the ability to escalate your privileges with the command "sudo su".
{{% /notice %}}

### 01. Create a Kali rootfs

Build yourself a [Kali rootfs](/docs/development/kali-linux-arm-chroot/) as described in our Kali documentation, using an **armhf** architecture. By the end of this process, you should have a populated rootfs directory in `~/arm-stuff/rootfs/kali-armhf`.

### 02. Create the Image File

Next, we create the physical image file which will hold our MK/SS808 rootfs and boot images.

```console
kali@kali:~$ sudo apt install -y kpartx xz-utils sharutils
kali@kali:~$ mkdir -p ~/arm-stuff/images/
kali@kali:~$ cd ~/arm-stuff/images/
kali@kali:~/arm-stuff/images$ dd if=/dev/zero of=kali-custom-ss808.img conv=fsync bs=4M count=7000
```

### 03. Partition and Mount the Image File

```console
kali@kali:~$ parted kali-custom-ss808.img --script -- mklabel msdos
kali@kali:~$ parted kali-custom-ss808.img --script -- mkpart primary ext4 1 -1
```

```console
kali@kali:~$ loopdevice=`losetup -f --show kali-custom-ss808.img`
kali@kali:~$ device=`kpartx -va $loopdevice| sed -E 's/.*(loop[0-9])p.*/\1/g' | head -1`
kali@kali:~$ device="/dev/mapper/${device}"
kali@kali:~$ rootp=${device}p1
kali@kali:~$
kali@kali:~$ mkfs.ext4 $rootp
kali@kali:~$ mkdir -p root/
kali@kali:~$ mount $rootp root
```

### 04. Copy and Modify the Kali rootfs

```console
kali@kali:~$ rsync -HPavz /root/arm-stuff/rootfs/kali-armhf-xfce4/ root
kali@kali:~$ echo nameserver 8.8.8.8 > root/etc/resolv.conf
```

### 05. Compile the rk3066 Kernel and Modules

If you're not using ARM hardware as the development environment, you will need to set up an [ARM cross-compilation environment](/docs/development/arm-cross-compilation-environment/) to build an ARM kernel and modules. Once that's done, proceed with the following steps.

```console
kali@kali:~$ sudo apt install -y xz-utils
kali@kali:~$ mkdir -p ~/arm-stuff/kernel/
kali@kali:~$ cd ~/arm-stuff/kernel/
kali@kali:~$
kali@kali:~$ git clone git://github.com/aloksinha2001/picuntu-3.0.8-alok.git rk3066-kernel
kali@kali:~$ cd rk3066-kernel/
kali@kali:~$ sed -i "/vpu_service/d" arch/arm/plat-rk/Makefile
```

```console
kali@kali:~$ export ARCH=arm
kali@kali:~$ export CROSS_COMPILE=~/arm-stuff/kernel/toolchains/arm-eabi-linaro-4.6.2/bin/arm-eabi-
kali@kali:~$
kali@kali:~$ # A basic configuration for the UG802 and MK802 III
kali@kali:~$ # make rk30_hotdog_ti_defconfig

kali@kali:~$ # A basic configuration for the MK808
kali@kali:~$ make rk30_hotdog_defconfig

kali@kali:~$ # configure your kernel !
kali@kali:~$ make menuconfig

kali@kali:~$ # Configure the kernel as per http://www.armtvtech.com/armtvtechforum/viewtopic.php?f=66&t;=835
kali@kali:~$ mkdir -p ../initramfs/
kali@kali:~$ wget http://208.88.127.99/initramfs.cpio -O ../initramfs/initramfs.cpio
kali@kali:~$
kali@kali:~$ mkdir -p ../patches/
kali@kali:~$ wget http://patches.aircrack-ng.org/mac80211.compat08082009.wl_frag+ack_v1.patch -O ../patches/mac80211.patch
kali@kali:~$ wget http://patches.aircrack-ng.org/channel-negative-one-maxim.patch- O ../patches/negative.patch
kali@kali:~$ patch -p1 < ../patches/mac80211.patch
kali@kali:~$ patch -p1 < ../patches/negative.patch
kali@kali:~$
kali@kali:~$ ./make_kernel_ruikemei.sh
```

```console
kali@kali:~$ make modules -j$(cat /proc/cpuinfo|grep processor | wc -l)
kali@kali:~$ make modules_install INSTALL_MOD_PATH=~/arm-stuff/images/root
kali@kali:~$ git clone git://git.kernel.org/pub/scm/linux/kernel/git/dwmw2/linux-firmware.git firmware-git
kali@kali:~$ mkdir -p ~/arm-stuff/images/root/lib/firmware
kali@kali:~$ cp -rf firmware-git/* ~/arm-stuff/images/root/lib/firmware/
kali@kali:~$ rm -rf firmware-git
```

```console
kali@kali:~$ umount $rootp
kali@kali:~$ kpartx -dv $loopdevice
kali@kali:~$ losetup -d $loopdevice
```

### 07. dd the Image to a USB device

Use the **[dd](https://packages.debian.org/testing/dd)** command to image this file to your SD card. In our example, we assume the storage device is located at `/dev/sdb`. **Change this as needed**.

```console
kali@kali:~$ dd if=kali-linux-ss808.img of=/dev/sdb conv=fsync bs=4M
```

Once the dd operation is complete, unmount and eject the SD card and boot your MK/SS808 into Kali Linux
