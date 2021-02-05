---
title: Custom EfikaMX Image
description:
icon:
type: post
weight:
author: ["steev",]
---

The following document describes our own method of creating a **custom Kali Linux EfikaMX ARM image** and is targeted at developers. If you would like to install a pre-made Kali image, check out our [Install Kali on an EfikaMX](/docs/arm/kali-linux-efikamx/) article.

{{% notice info %}}
You'll need to have root privileges to do this procedure, or the ability to escalate your privileges with the command "sudo su".
{{% /notice %}}

### 01. Create a Kali rootfs

Build a [Kali rootfs](/docs/development/kali-linux-arm-chroot/) as described in our Kali documentation, using an **armhf** architecture. By the end of this process, you should have a populated rootfs directory in `~/arm-stuff/rootfs/kali-armhf`.

### 02. Create the Image File

Next, we create the physical image file, which will hold our EfikaMX rootfs and boot images.

```console
kali@kali:~$ sudo apt install -y kpartx xz-utils sharutils
kali@kali:~$ mkdir -p ~/arm-stuff/images/
kali@kali:~$ cd ~/arm-stuff/images/
kali@kali:~$ dd if=/dev/zero of=kali-custom-efikamx.img bs=4M count=7000
```

### 03. Partition and Mount the Image File

```console
kali@kali:~$ parted kali-custom-efikamx.img --script -- mklabel msdos
kali@kali:~$ parted kali-custom-efikamx.img --script -- mkpart primary ext2 4096s 266239s
kali@kali:~$ parted kali-custom-efikamx.img --script -- mkpart primary ext4 266240s 100%
```

```console
kali@kali:~$ loopdevice=$( losetup -f --show kali-custom-efikamx.img )
kali@kali:~$ device=$( kpartx -va $loopdevice| sed -E 's/.*(loop[0-9])p.*/\1/g' | head -1 )
kali@kali:~$ device="/dev/mapper/${device}"
kali@kali:~$ bootp=${device}p1
kali@kali:~$ rootp=${device}p2
kali@kali:~$
kali@kali:~$ mkfs.ext2 $bootp
kali@kali:~$ mkfs.ext4 $rootp
kali@kali:~$ mkdir -p boot
kali@kali:~$ mkdir -p root
kali@kali:~$ mount $bootp boot
kali@kali:~$ mount $rootp root
```

### 04. Copy and Modify the Kali rootfs

```console
kali@kali:~$ rsync -HPavz /root/arm-stuff/rootfs/kali-armhf/ root
kali@kali:~$ echo nameserver 8.8.8.8 > root/etc/resolv.conf
kali@kali:~$ sed 's/0-1/0//g' root/etc/init.d/udev
```

### 05. Compile the EfikaMX Kernel and Modules

If you're not using ARM hardware as the development environment, you will need to set up an [ARM cross-compilation environment](/docs/development/arm-cross-compilation-environment/) to build an ARM kernel and modules. Once that's done, proceed with the following instructions.

```console
kali@kali:~$ mkdir -p ~/arm-stuff/kernel/
kali@kali:~$ cd ~/arm-stuff/kernel/
kali@kali:~$ git clone --depth 1 https://github.com/genesi/linux-legacy.git
kali@kali:~$ cd linux-legacy/
kali@kali:~$ export ARCH=arm
kali@kali:~$ export CROSS_COMPILE=~/arm-stuff/kernel/toolchains/arm-eabi-linaro-4.6.2/bin/arm-eabi-
kali@kali:~$ make efikamx_defconfig

# configure your kernel !
kali@kali:~$ make menuconfig
kali@kali:~$ make -j$(cat /proc/cpuinfo|grep processor | wc -l)
kali@kali:~$ make modules_install INSTALL_MOD_PATH=~/arm-stuff/images/root
kali@kali:~$ make uImage
kali@kali:~$ cp arch/arm/boot/uImage ~/arm-stuff/images/boot
kali@kali:~$
kali@kali:~$ cat <<EOF > ~/arm-stuff/images/boot/boot.script
setenv ramdisk uInitrd;
setenv kernel uImage;
setenv bootargs console=tty1 root=/dev/mmcblk0p2 rootwait rootfstype=ext4 rw quiet;
${loadcmd} ${ramdiskaddr} ${ramdisk};
if imi ${ramdiskaddr}; then; else
setenv bootargs ${bootargs} noinitrd;
setenv ramdiskaddr "";
fi;
${loadcmd} ${kerneladdr} ${kernel}
if imi ${kerneladdr}; then
bootm ${kerneladdr} ${ramdiskaddr}
fi;
EOF
kali@kali:~$
kali@kali:~$ mkimage -A arm -T script -C none -n "Boot.scr for EfikaMX" -d ~/arm-stuff/images/boot/boot.script ~/arm-stuff/images/boot/boot.scr
```

```console
kali@kali:~$ umount $bootp
kali@kali:~$ umount $rootp
kali@kali:~$ kpartx -dv $loopdevice
kali@kali:~$ losetup -d $loopdevice
```

Use the **[dd](https://packages.debian.org/testing/dd)** command to image this file to your SD card. In our example, we assume the storage device is located at `/dev/sdb`. **Change this as needed**.

```console
kali@kali:~$ dd if=kali-custom-efikamx.img of=/dev/sdb bs=4M
```

Once the dd operation is complete, unmount and eject the SD card and boot your EfikaMX into Kali Linux
