---
title: Custom Beaglebone Black Image
description:
icon:
date: 2020-02-22
type: post
weight: 100
author: ["steev",]
tags: ["",]
keywords: ["",]
og_description:
---

The following document describes our own method of creating a **custom Kali Linux Beaglebone Black ARM image** and is targeted at developers. If you would like to install a pre-made Kali image, check out our [Install Kali on Beaglebone Black](/docs/arm/kali-linux-beaglebone-black/) article.

{{% notice info %}}
You'll need to have root privileges to do this procedure, or the ability to escalate your privileges with the command "sudo su".
{{% /notice %}}

### 01. Create a Kali rootfs

Build a [Kali rootfs](/docs/development/kali-linux-arm-chroot/) as described in our Kali documentation, using an **armhf** architecture. By the end of this process, you should have a populated rootfs directory in `~/arm-stuff/rootfs/kali-armhf`.

### 02. Create the Image File

Next, we create the physical image file, which will hold our Beaglebone Black rootfs and boot images.

```markdown
apt install -y kpartx xz-utils sharutils
cd ~/
mkdir -p arm-stuff/images/
cd arm-stuff/images/
dd if=/dev/zero of=kali-custom-bbb.img bs=1MB count=7000
```

### 03. Partition and Mount the Image File

```plaintext
parted --script kali-custom-bbb.img mklabel msdos
fdisk kali-custom-bbb.img << __EOF__
n
p
1

+64M
t
e
p
w
__EOF__
parted --script kali-custom-bbb.img set 1 boot on
fdisk kali-custom-bbb.img << __EOF__
n
p
2

w
__EOF__
```

```html
loopdevice=`losetup -f --show kali-custom-bbb.img`
device=`kpartx -va $loopdevice| sed -E 's/.*(loop[0-9])p.*/\1/g' | head -1`
device="/dev/mapper/${device}"
bootp=${device}p1
rootp=${device}p2

mkfs.vfat -F 16 $bootp -n boot
mkfs.ext4 $rootp -L kaliroot
mkdir -p boot
mkdir -p root
mount $bootp boot
mount $rootp root
```

### 04. Copy and Modify the Kali rootfs

```markdown
rsync -HPavz /root/arm-stuff/rootfs/kali-armhf/ root
echo nameserver 8.8.8.8 > root/etc/resolv.conf
```

### 05. Compile the Beaglebone Black Kernel and Modules

If you're not using ARM hardware as the development environment, you will need to set up an [ARM cross-compilation environment](/docs/development/arm-cross-compilation-environment/) to build an ARM kernel and modules. Once that's done, proceed with the following instructions.

```plaintext
cd ~/arm-stuff/
wget https://launchpad.net/linaro-toolchain-binaries/trunk/2013.03/+download/gcc-linaro-arm-linux-gnueabihf-4.7-2013.03-20130313_linux.tar.bz2
tar -xjf gcc-linaro-arm-linux-gnueabihf-4.7-2013.03-20130313_linux.tar.bz2
export CC=`pwd`/gcc-linaro-arm-linux-gnueabihf-4.7-2013.03-20130313_linux/bin/arm-linux-gnueabihf-

git clone git://git.denx.de/u-boot.git
cd u-boot/
git checkout v2013.04 -b beaglebone-black
wget https://raw.github.com/eewiki/u-boot-patches/master/v2013.04/0001-am335x_evm-uEnv.txt-bootz-n-fixes.patch
patch -p1 < 0001-am335x_evm-uEnv.txt-bootz-n-fixes.patch
make ARCH=arm CROSS_COMPILE=${CC} distclean
make ARCH=arm CROSS_COMPILE=${CC} am335x_evm_config
make ARCH=arm CROSS_COMPILE=${CC}
cd ../

mkdir -p kernel/
cd kernel/
git clone git://github.com/RobertCNelson/linux-dev.git
cd linux-dev/
git checkout origin/am33x-v3.8 -b tmp
./build_kernel.sh
mkdir -p ../patches/
wget http://patches.aircrack-ng.org/mac80211.compat08082009.wl_frag+ack_v1.patch -O ../patches/mac80211.patch
cd KERNEL/
patch -p1 --no-backup-if-mismatch < ../../patches/mac80211.patch
cd ../
./tools/rebuild.sh
cd ../

cat << EOF > boot/uEnv.txt
mmcroot=/dev/mmcblk0p2 ro
mmcrootfstype=ext4 rootwait fixrtc
uenvcmd=run loaduimage; run loadfdt; run mmcargs; bootz 0x80200000 - 0x80F80000
EOF

cp -v kernel/linux-dev/deploy/3.8.13-bone20.zImage boot/zImage
mkdir -p boot/dtbs
tar -xovf kernel/linux-dev/deploy/3.8.13-bone20-dtbs.tar.gz -C boot/dtbs/

tar -xovf kernel/linux-dev/deploy/3.8.13-bone20-modules.tar.gz -C root/
tar -xovf kernel/linux-dev/deploy/3.8.13-bone20-firmware.tar.gz -C root/lib/firmware/

cat << EOF > root/etc/fstab
/dev/mmcblk0p2 / auto errors=remount-ro 0 1
/dev/mmcblk0p1 /boot/uboot auto defaults 0 0
EOF
```

```markdown
umount $rootp
kpartx -dv $loopdevice
losetup -d $loopdevice
```

Use the **[dd](https://packages.debian.org/testing/dd)** command to image this file to your SD card. In our example, we assume the storage device is located at `/dev/sdb`. **Change this as needed.**

```markdown
dd if=kali-custom-bbb.img of=/dev/sdb bs=1M
```

Once the dd operation is complete, unmount and eject the SD card and boot your Beaglebone Black into Kali Linux. When booting you will need to press and hold the "BOOT" button, it's the one closest to the microSD card.
