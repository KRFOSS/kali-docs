---
title: Custom Raspberry Pi Image
description:
icon:
weight:
author: ["steev",]
---

The following document describes our own method of creating a **custom Kali Linux Raspberry Pi ARM image** and is targeted at developers. If you would like to install a pre-made Kali image, check out our [Install Kali on Raspberry Pi](/docs/arm/kali-linux-raspberry-pi/) article.

{{% notice info %}}
You'll need to have root privileges to do this procedure, or the ability to escalate your privileges with the command "sudo su".
{{% /notice %}}

### 01. Create a Kali rootfs

Build a [Kali rootfs](/docs/development/kali-linux-arm-chroot/) as described in our Kali documentation, using an **armel** architecture. By the end of this process, you should have a populated rootfs directory in **~/arm-stuff/rootfs/kali-armel**.

### 02. Create the Image File

Next, we create the physical image file, which will hold our Raspberry Pi rootfs and boot images.

```console
kali@kali:~$ sudo apt install -y kpartx xz-utils sharutils
kali@kali:~$ mkdir -p ~/arm-stuff/images/
kali@kali:~$ cd ~/arm-stuff/images/
kali@kali:~$ dd if=/dev/zero of=kali-custom-rpi.img bs=4M count=7000
```

### 03. Partition and Mount the Image File

```console
kali@kali:~$ parted kali-custom-rpi.img --script -- mklabel msdos
kali@kali:~$ parted kali-custom-rpi.img --script -- mkpart primary fat32 0 64
kali@kali:~$ parted kali-custom-rpi.img --script -- mkpart primary ext4 64 -1
```

```console
kali@kali:~$ loopdevice=`losetup -f --show kali-custom-rpi.img`
kali@kali:~$ device=`kpartx -va $loopdevice | sed -E 's/.*(loop[0-9])p.*/\1/g' | head -1`
kali@kali:~$ device="/dev/mapper/${device}"
kali@kali:~$ bootp=${device}p1
kali@kali:~$ rootp=${device}p2
kali@kali:~$
kali@kali:~$ mkfs.vfat $bootp
kali@kali:~$ mkfs.ext4 $rootp
kali@kali:~$ mkdir -p root
kali@kali:~$ mkdir -p boot
kali@kali:~$ mount $rootp root
kali@kali:~$ mount $bootp boot
```

### 04. Copy and Modify the Kali rootfs

```console
kali@kali:~$ rsync -HPavz /root/arm-stuff/rootfs/kali-armel/ root
kali@kali:~$ echo nameserver 8.8.8.8 > root/etc/resolv.conf
```

### 05. Compile the Raspberry Pi Kernel and Modules

If you're not using ARM hardware as the development environment, you will need to set up an [ARM cross-compilation environment](/docs/development/arm-cross-compilation-environment/) to build an ARM kernel and modules. Once that's done, proceed with the following instructions.

```console
kali@kali:~$ mkdir -p ~/arm-stuff/kernel/
kali@kali:~$ cd ~/arm-stuff/kernel/
kali@kali:~$ git clone https://github.com/raspberrypi/tools.git
kali@kali:~$ git clone https://github.com/raspberrypi/linux.git raspberrypi
kali@kali:~$ cd raspberrypi/
kali@kali:~$ touch .scmversion
kali@kali:~$ export ARCH=arm
kali@kali:~$ export CROSS_COMPILE=~/arm-stuff/kernel/toolchains/arm-eabi-linaro-4.6.2/bin/arm-eabi-
kali@kali:~$ make bcmrpi_cutdown_defconfig

kali@kali:~$ # configure your kernel !
kali@kali:~$ make menuconfig
kali@kali:~$ make -j$(cat /proc/cpuinfo|grep processor | wc -l)
kali@kali:~$ make modules_install INSTALL_MOD_PATH=~/arm-stuff/images/root
kali@kali:~$ cd ../tools/mkimage/
kali@kali:~$ python imagetool-uncompressed.py ../../raspberrypi/arch/arm/boot/Image
```

```console
kali@kali:~$ cd ~/arm-stuff/images/
kali@kali:~$ git clone git://github.com/raspberrypi/firmware.git rpi-firmware
kali@kali:~$ cp -rf rpi-firmware/boot/* boot/
kali@kali:~$ rm -rf rpi-firmware
kali@kali:~$
kali@kali:~$ cp ~/arm-stuff/kernel/tools/mkimage/kernel.img boot/
kali@kali:~$ echo "dwc_otg.lpm_enable=0 console=ttyAMA0,115200 kgdboc=ttyAMA0,115200 console=tty1 root=/dev/mmcblk0p2 rootfstype=ext4 rootwait" > boot/cmdline.txt
```

```console
kali@kali:~$ umount $rootp
kali@kali:~$ umount $bootp
kali@kali:~$ kpartx -dv $loopdevice
kali@kali:~$ losetup -d $loopdevice
```

Use the **[dd](https://packages.debian.org/testing/dd)** command to image this file to your SD card. In our example, we assume the storage device is located at `/dev/sdb`. **Change this as needed**.

```console
kali@kali:~$ dd if=kali-linux-rpi.img of=/dev/sdb bs=4M
```

Once the dd operation is complete, unmount and eject the SD card and boot your Pi into Kali Linux
