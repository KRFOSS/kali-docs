---
title: Custom Beaglebone Black Image
description:
icon:
weight:
author: ["steev",]
---

The following document describes our own method of creating a **custom Kali Linux Beaglebone Black ARM image** and is targeted at developers. If you would like to install a pre-made Kali image, check out our [Install Kali on Beaglebone Black](/docs/arm/beaglebone-black/) article.

{{% notice info %}}
You'll need to have root privileges to do this procedure, or the ability to escalate your privileges with the command "sudo su".
{{% /notice %}}

### 01. Create a Kali rootfs

Build a [Kali rootfs](/docs/development/kali-linux-arm-chroot/) as described in our Kali documentation, using an **armhf** architecture. By the end of this process, you should have a populated rootfs directory in `~/arm-stuff/rootfs/kali-armhf`.

### 02. Create the Image File

Next, we create the physical image file, which will hold our Beaglebone Black rootfs and boot images.

```console
kali@kali:~$ sudo apt install -y kpartx xz-utils sharutils
kali@kali:~$ mkdir -p ~/arm-stuff/images/
kali@kali:~$ cd ~/arm-stuff/images/
kali@kali:~$ dd if=/dev/zero of=kali-custom-bbb.img bs=4M count=7000
```

### 03. Partition and Mount the Image File

```console
kali@kali:~$ parted --script kali-custom-bbb.img mklabel msdos
kali@kali:~$ fdisk kali-custom-bbb.img <<EOF
n
p
1

+64M
t
e
p
w
EOF
kali@kali:~$ parted --script kali-custom-bbb.img set 1 boot on
kali@kali:~$ fdisk kali-custom-bbb.img <<EOF
n
p
2

w
EOF
```

```console
kali@kali:~$ loopdevice=`losetup -f --show kali-custom-bbb.img`
kali@kali:~$ device=`kpartx -va $loopdevice| sed -E 's/.*(loop[0-9])p.*/\1/g' | head -1`
kali@kali:~$ device="/dev/mapper/${device}"
kali@kali:~$ bootp=${device}p1
kali@kali:~$ rootp=${device}p2
kali@kali:~$
kali@kali:~$ mkfs.vfat -F 16 $bootp -n boot
kali@kali:~$ mkfs.ext4 $rootp -L kaliroot
kali@kali:~$ mkdir -p boot
kali@kali:~$ mkdir -p root
kali@kali:~$ mount $bootp boot
kali@kali:~$ mount $rootp root
```

### 04. Copy and Modify the Kali rootfs

```console
kali@kali:~$ rsync -HPavz /root/arm-stuff/rootfs/kali-armhf/ root
kali@kali:~$ echo nameserver 8.8.8.8 > root/etc/resolv.conf
```

### 05. Compile the Beaglebone Black Kernel and Modules

If you're not using ARM hardware as the development environment, you will need to set up an [ARM cross-compilation environment](/docs/development/arm-cross-compilation-environment/) to build an ARM kernel and modules. Once that's done, proceed with the following instructions.

```console
kali@kali:~$ cd ~/arm-stuff/
kali@kali:~$ wget https://launchpad.net/linaro-toolchain-binaries/trunk/2013.03/+download/gcc-linaro-arm-linux-gnueabihf-4.7-2013.03-20130313_linux.tar.bz2
kali@kali:~$ tar -xjf gcc-linaro-arm-linux-gnueabihf-4.7-2013.03-20130313_linux.tar.bz2
kali@kali:~$ export CC=`pwd`/gcc-linaro-arm-linux-gnueabihf-4.7-2013.03-20130313_linux/bin/arm-linux-gnueabihf-
kali@kali:~$
kali@kali:~$ git clone git://git.denx.de/u-boot.git
kali@kali:~$ cd u-boot/
kali@kali:~$ git checkout v2013.04 -b beaglebone-black
kali@kali:~$ wget https://raw.github.com/eewiki/u-boot-patches/master/v2013.04/0001-am335x_evm-uEnv.txt-bootz-n-fixes.patch
kali@kali:~$ patch -p1 < 0001-am335x_evm-uEnv.txt-bootz-n-fixes.patch
kali@kali:~$ make ARCH=arm CROSS_COMPILE=${CC} distclean
kali@kali:~$ make ARCH=arm CROSS_COMPILE=${CC} am335x_evm_config
kali@kali:~$ make ARCH=arm CROSS_COMPILE=${CC}
kali@kali:~$ cd ../
kali@kali:~$
kali@kali:~$ mkdir -p kernel/
kali@kali:~$ cd kernel/
kali@kali:~$ git clone git://github.com/RobertCNelson/linux-dev.git
kali@kali:~$ cd linux-dev/
kali@kali:~$ git checkout origin/am33x-v3.8 -b tmp
kali@kali:~$ ./build_kernel.sh
kali@kali:~$ mkdir -p ../patches/
kali@kali:~$ wget http://patches.aircrack-ng.org/mac80211.compat08082009.wl_frag+ack_v1.patch -O ../patches/mac80211.patch
kali@kali:~$ cd KERNEL/
kali@kali:~$ patch -p1 --no-backup-if-mismatch < ../../patches/mac80211.patch
kali@kali:~$ cd ../
kali@kali:~$ ./tools/rebuild.sh
kali@kali:~$ cd ../
kali@kali:~$
kali@kali:~$ cat <<EOF > boot/uEnv.txt
mmcroot=/dev/mmcblk0p2 ro
mmcrootfstype=ext4 rootwait fixrtc
uenvcmd=run loaduimage; run loadfdt; run mmcargs; bootz 0x80200000 - 0x80F80000
EOF
kali@kali:~$
kali@kali:~$ cp -v kernel/linux-dev/deploy/3.8.13-bone20.zImage boot/zImage
kali@kali:~$ mkdir -p boot/dtbs
kali@kali:~$ tar -xovf kernel/linux-dev/deploy/3.8.13-bone20-dtbs.tar.gz -C boot/dtbs/
kali@kali:~$
kali@kali:~$ tar -xovf kernel/linux-dev/deploy/3.8.13-bone20-modules.tar.gz -C root/
kali@kali:~$ tar -xovf kernel/linux-dev/deploy/3.8.13-bone20-firmware.tar.gz -C root/lib/firmware/
kali@kali:~$
kali@kali:~$ cat <<EOF > root/etc/fstab
/dev/mmcblk0p2 / auto errors=remount-ro 0 1
/dev/mmcblk0p1 /boot/uboot auto defaults 0 0
EOF
kali@kali:~$
```

```console
kali@kali:~$ umount $rootp
kali@kali:~$ kpartx -dv $loopdevice
kali@kali:~$ losetup -d $loopdevice
```

Use the **[dd](https://packages.debian.org/testing/dd)** command to image this file to your SD card. In our example, we assume the storage device is located at `/dev/sdb`. **Change this as needed**.

```console
kali@kali:~$ dd if=kali-linux-bbb.img of=/dev/sdb bs=4M
```

Once the dd operation is complete, unmount and eject the SD card and boot your Beaglebone Black into Kali Linux. When booting you will need to press and hold the "BOOT" button, it's the one closest to the microSD card.
