---
title: Custom EfikaMX Image
description:
icon:
date: 2019-10-26
type: post
weight: 100
author: ["steev",]
tags: ["",]
keywords: ["",]
og_description:
---

The following document describes our own method of creating a **custom Kali Linux EfikaMX ARM image** and is targeted at developers. If you would like to install a pre-made Kali image, check out our [Install Kali on an EfikaMX](/docs/arm/kali-linux-efikamx/) article.

{{% notice info %}}
You'll need to have root privileges to do this procedure, or the ability to escalate your privileges with the command "sudo su".
{{% /notice %}}

### 01. Create a Kali rootfs

Build a [Kali rootfs](/docs/development/kali-linux-arm-chroot/) as described in our Kali documentation, using an **armhf** architecture. By the end of this process, you should have a populated rootfs directory in **~/arm-stuff/rootfs/kali-armhf**.

### 02. Create the Image File

Next, we create the physical image file, which will hold our EfikaMX rootfs and boot images.

```markdown
apt install -y kpartx xz-utils sharutils
cd ~
mkdir -p arm-stuff
cd arm-stuff/
mkdir -p images
cd images
dd if=/dev/zero of=kali-custom-efikamx.img bs=1MB count=7000
```

### 03. Partition and Mount the Image File

```markdown
parted kali-custom-efikamx.img --script -- mklabel msdos
parted kali-custom-efikamx.img --script -- mkpart primary ext2 4096s 266239s
parted kali-custom-efikamx.img --script -- mkpart primary ext4 266240s 100%
```

```markdown
loopdevice=$( losetup -f --show kali-custom-efikamx.img )
device=$( kpartx -va $loopdevice| sed -E 's/.*(loop[0-9])p.*/\1/g' | head -1 )
device="/dev/mapper/${device}"
bootp=${device}p1
rootp=${device}p2

mkfs.ext2 $bootp
mkfs.ext4 $rootp
mkdir -p boot
mkdir -p root
mount $bootp boot
mount $rootp root
```

### 04. Copy and Modify the Kali rootfs

```markdown
rsync -HPavz /root/arm-stuff/rootfs/kali-armhf/ root
echo nameserver 8.8.8.8 > root/etc/resolv.conf
sed 's/0-1/0//g' root/etc/init.d/udev
```

### 05. Compile the EfikaMX Kernel and Modules

If you're not using ARM hardware as the development environment, you will need to set up an [ARM cross-compilation environment](/docs/development/arm-cross-compilation-environment/) to build an ARM kernel and modules. Once that's done, proceed with the following instructions.

```html
cd ~/arm-stuff
mkdir -p kernel
cd kernel
git clone --depth 1 https://github.com/genesi/linux-legacy.git
cd linux-legacy
export ARCH=arm
export CROSS_COMPILE=~/arm-stuff/kernel/toolchains/arm-eabi-linaro-4.6.2/bin/arm-eabi-
make efikamx_defconfig
# configure your kernel !
make menuconfig
make -j$(cat /proc/cpuinfo|grep processor|wc -l)
make modules_install INSTALL_MOD_PATH=~/arm-stuff/images/root
make uImage
cp arch/arm/boot/uImage ~/arm-stuff/images/boot

cat << EOF > ~/arm-stuff/images/boot/boot.script
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

mkimage -A arm -T script -C none -n "Boot.scr for EfikaMX" -d ~/arm-stuff/images/boot/boot.script ~/arm-stuff/images/boot/boot.scr
```

```markdown
umount $bootp
umount $rootp
kpartx -dv $loopdevice
losetup -d $loopdevice
```

Use the **dd** utility to image this file to your SD card. In our example, we assume the storage device is located at /dev/sdb. **Change this as needed.**

```markdown
dd if=kali-custom-efikamx.img of=/dev/sdb bs=1M
```

Once the dd operation is complete, unmount and eject the SD card and boot your EfikaMX into Kali Linux
