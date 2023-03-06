---
title: Custom CuBox Image
description:
icon:
weight:
author: ["steev",]
---

The following document describes our own method of creating a **custom Kali Linux CuBox ARM image** and is targeted at developers. If you would like to install a pre-made Kali image, check out our [Install Kali on CuBox](/docs/arm/cubox/) article.

{{% notice info %}}
You'll need to have root privileges to do this procedure, or the ability to escalate your privileges with the command "sudo su".
{{% /notice %}}

### 01. Create a Kali rootfs

Build a [Kali rootfs](/docs/development/kali-linux-arm-chroot/) as described in our Kali documentation, using an **armhf** architecture. By the end of this process, you should have a populated rootfs directory in `~/arm-stuff/rootfs/kali-armhf`.

### 02. Create the Image File

Next, we create the physical image file, which will hold our CuBox rootfs and boot images:

```console
kali@kali:~$ sudo apt install -y kpartx xz-utils sharutils
kali@kali:~$ mkdir -p ~/arm-stuff/images/
kali@kali:~$ cd ~/arm-stuff/images/
kali@kali:~$ dd if=/dev/zero of=kali-custom-cubox.img conv=fsync bs=4M count=7000
```

### 03. Partition and Mount the Image File

```console
kali@kali:~$ parted kali-custom-cubox.img --script -- mklabel msdos
kali@kali:~$ parted kali-custom-cubox.img --script -- mkpart primary ext4 0 -1
```

```console
kali@kali:~$ loopdevice=$(losetup -f --show kali-custom-cubox.img)
kali@kali:~$ device=`kpartx -va $loopdevice| sed -E 's/.*(loop[0-9])p.*/\1/g' | head -1`
kali@kali:~$ device="/dev/mapper/${device}"
kali@kali:~$ rootp=${device}p1
kali@kali:~$
kali@kali:~$ mkfs.ext4 $rootp
kali@kali:~$ mkdir -p root
kali@kali:~$ mount $rootp root
```

### 04. Copy and Modify the Kali rootfs

```console
kali@kali:~$ rsync -HPavz /root/arm-stuff/rootfs/kali-armhf/ root
kali@kali:~$ echo nameserver 8.8.8.8 > root/etc/resolv.conf
```

### 05. Compile the CuBox Kernel and Modules

If you're not using ARM hardware as the development environment, you will need to set up an [ARM cross-compilation environment](/docs/development/arm-cross-compilation-environment/) to build an ARM kernel and modules. Once that's done, proceed with the following instructions:

```console
kali@kali:~$ mkdir -p ~/arm-stuff/kernel/
kali@kali:~$ cd ~/arm-stuff/kernel/
kali@kali:~$ git clone --depth 1 https://github.com/rabeeh/linux.git
kali@kali:~$ cd linux/
kali@kali:~$ touch .scmversion
kali@kali:~$ mkdir -p ../patches/
kali@kali:~$ wget http://patches.aircrack-ng.org/mac80211.compat08082009.wl_frag+ack_v1.patch -O ../patches/mac80211.patch
kali@kali:~$ patch -p1 --no-backup-if-mismatch < ../patches/mac80211.patch
kali@kali:~$ export ARCH=arm
kali@kali:~$ export CROSS_COMPILE=~/arm-stuff/kernel/toolchains/arm-eabi-linaro-4.6.2/bin/arm-eabi-
kali@kali:~$ make cubox_defconfig

# configure your kernel !
kali@kali:~$ make menuconfig
kali@kali:~$ make -j$( cat /proc/cpuinfo|grep processor | wc -l )
kali@kali:~$ make modules_install INSTALL_MOD_PATH=~/arm-stuff/images/root
kali@kali:~$ make uImage
kali@kali:~$ cp arch/arm/boot/uImage ~/arm-stuff/images/root/boot
kali@kali:~$
kali@kali:~$ cat <<EOF > ~/arm-stuff/images/root/boot/boot.txt
echo "== Executing ${directory}${bootscript} on ${device_name} partition ${partition} =="
setenv unit_no 0
setenv root_device ?

if itest.s ${device_name} -eq usb; then
itest.s $root_device -eq ? && ext4ls usb 0:1 /dev && setenv root_device /dev/sda1 && setenv unit_no 0
itest.s $root_device -eq ? && ext4ls usb 1:1 /dev && setenv root_device /dev/sda1 && setenv unit_no 1
fi

if itest.s ${device_name} -eq mmc; then
itest.s $root_device -eq ? && ext4ls mmc 0:2 /dev && setenv root_device /dev/mmcblk0p2
itest.s $root_device -eq ? && ext4ls mmc 0:1 /dev && setenv root_device /dev/mmcblk0p1
fi

if itest.s ${device_name} -eq ide; then
itest.s $root_device -eq ? && ext4ls ide 0:1 /dev && setenv root_device /dev/sda1
fi

if itest.s $root_device -ne ?; then
setenv bootargs "console=ttyS0,115200n8 vmalloc=448M video=dovefb:lcd0:1920x1080-32@60-edid clcd.lcd0_enable=1 clcd.lcd1_enable=0 root=${root_device} rootfstype=ext4"
setenv loadimage "${fstype}load ${device_name} ${unit_no}:${partition} 0x00200000 ${directory}${image_name}"
$loadimage && bootm 0x00200000
echo "!! Unable to load ${directory}${image_name} from ${device_name} ${unit_no}:${partition} !!"
exit
fi

echo "!! Unable to locate root partition on ${device_name} !!"
EOF
kali@kali:~$
kali@kali:~$ mkimage -A arm -T script -C none -n "Boot.scr for CuBox" -d ~/arm-stuff/images/root/boot/boot.txt ~/arm-stuff/images/root/boot/boot.scr
```

```console
kali@kali:~$ umount $rootp
kali@kali:~$ kpartx -dv $loopdevice
kali@kali:~$ losetup -d $loopdevice
```

Use the **[dd](https://packages.debian.org/testing/dd)** command to image this file to your SD card. In our example, we assume the storage device is located at `/dev/sdb`. **Change this as needed**:

```console
kali@kali:~$ dd if=kali-linux-cubox.img of=/dev/sdb conv=fsync bs=4M
```

Once the dd operation is complete, unmount and eject the SD card and boot your CuBox into Kali Linux
