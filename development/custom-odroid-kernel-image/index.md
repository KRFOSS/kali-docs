---
title: Custom ODROID X2 U2 Image
description:
icon:
weight:
author: ["steev",]
---

The following document describes our own method of creating a **custom Kali Linux ODROID image** and is targeted at developers. If you would like to install a pre-made Kali ODROID image, check our [Install Kali on ODROID](/docs/arm/odroid-u/) article.

{{% notice info %}}
You'll need to have root privileges to do this procedure, or the ability to escalate your privileges with the command "sudo su".
{{% /notice %}}

#### 01. Create a Kali rootfs

Start by building a [Kali rootfs](/docs/development/kali-linux-arm-chroot/) as described in our Kali documentation using an **armhf** architecture. By the end of this process, you should have a populated rootfs directory in `~/arm-stuff/rootfs/kali-armhf`.

#### 02. Create the Image File

Next, we create the physical image file which will hold our ODROID rootfs and boot images.

```console
kali@kali:~$ sudo apt install -y kpartx xz-utils uboot-mkimage
kali@kali:~$ mkdir -p ~/arm-stuff/images/
kali@kali:~$ cd ~/arm-stuff/images/
kali@kali:~$ dd if=/dev/zero of=kali-custom-odroid.img bs=4M count=7000
```

#### 03. Partition and Mount the Image File

```console
kali@kali:~$ parted kali-custom-odroid.img --script -- mklabel msdos
kali@kali:~$ parted kali-custom-odroid.img --script -- mkpart primary fat32 4096s 266239s
kali@kali:~$ parted kali-custom-odroid.img --script -- mkpart primary ext4 266240s 100%
kali@kali:~$
kali@kali:~$ loopdevice=`losetup -f --show kali-custom-odroid.img`
kali@kali:~$ device=`kpartx -va $loopdevice| sed -E 's/.*(loop[0-9])p.*/\1/g' | head -1`
kali@kali:~$ device="/dev/mapper/${device}"
kali@kali:~$ bootp=${device}p1
kali@kali:~$ rootp=${device}p2
kali@kali:~$ mkfs.vfat $bootp
kali@kali:~$ mkfs.ext4 -L kaliroot $rootp
kali@kali:~$ mkdir -p boot root
kali@kali:~$ mount $bootp boot
kali@kali:~$ mount $rootp root
```

#### 04. Copy and Modify the Kali rootfs

Copy over the Kali rootfs you bootstrapped earlier using **rsync** to the mounted image.

```console
kali@kali:~$ cd ~/arm-stuff/images/
kali@kali:~$ rsync -HPavz ~/arm-stuff/rootfs/kali-armhf/ root
kali@kali:~$ echo nameserver 8.8.8.8 > root/etc/resolv.conf
```

Edit the **~/arm-stuff/images/root/etc/inittab** file and locate the "Example how to put a getty on a serial line".

```console
kali@kali:~$ vim root/etc/inittab
```

Add the following line to the end of that section.

```plaintext
T1:12345:respawn:/sbin/agetty 115200 ttySAC1 vt100
```

If you want the serial console to autologin as root, use the following line instead:

```plaintext
T1:12345:respawn:/bin/login -f root ttySAC1 /dev/ttySAC1 >&1
```

Now, make sure there is a _ttySAC1_ entry in the **~/arm-stuff/images/root/etc/udev/links.conf** file.

```console
kali@kali:~$ vim root/etc/udev/links.conf
```

If an entry for _ttySAC1_ doesn't already exist, add it to the file so it looks as follows:

```plaintext
M null c 1 3
M console c 5 1
M ttySAC1 c 5 1
```

Add _ttySAC_ entries in the **~/arm-stuff/images/root/etc/udev/links.conf** file.

```console
kali@kali:~$ cat <<EOF >> root/etc/securetty
ttySAC0
ttySAC1
ttySAC2
EOF
```

Place a basic xorg.conf file in the rootfs.

```console
kali@kali:~$ cat <<EOF > root/etc/X11/xorg.conf
# X.Org X server configuration file for xfree86-video-mali

Section "Device"
Identifier "Mali-Fbdev"
# Driver "mali"
Option "fbdev" "/dev/fb1"
Option "DRI2" "true"
Option "DRI2_PAGE_FLIP" "true"
Option "DRI2_WAIT_VSYNC" "true"
Option "UMP_CACHED" "true"
Option "UMP_LOCK" "false"
EndSection

Section "Screen"
Identifier "Mali-Screen"
Device "Mali-Fbdev"
DefaultDepth 24
EndSection

Section "DRI"
Mode 0666
EndSection
EOF
```

Link **init** in the root, rootfs directory:

```console
kali@kali:~$ cd ~/arm-stuff/images/root/
kali@kali:~$ ln -s /sbin/init init
```

#### 05. Compile the ODROID Kernel and Modules

If you're not using ARM hardware as the development environment, you will need to set up an [ARM cross-compilation environment](/docs/development/arm-cross-compilation-environment/) to build an ARM kernel and modules. Once that's done, proceed with the following instructions.

We next need to fetch the ODROID kernel sources and place them in our development tree structure:

```console
kali@kali:~$ mkdir -p ~/arm-stuff/kernel/
kali@kali:~$ cd ~/arm-stuff/kernel/
kali@kali:~$ git clone --depth 1 https://github.com/hardkernel/linux.git -b odroid-3.8.y odroid
kali@kali:~$ cd odroid/
kali@kali:~$ touch .scmversion
```

Configure, then cross-compile the ODROID kernel.

```console
kali@kali:~$ export ARCH=arm
kali@kali:~$ export CROSS_COMPILE=~/arm-stuff/kernel/toolchains/arm-eabi-linaro-4.6.2/bin/arm-eabi-
kali@kali:~$
kali@kali:~$ # for ODROID-X2
kali@kali:~$ make odroidx2_defconfig
kali@kali:~$ # for ODROID-U2
kali@kali:~$ make odroidu2_defconfig
kali@kali:~$ # configure your kernel !
kali@kali:~$ make menuconfig
kali@kali:~$ # and enable
kali@kali:~$ CONFIG_HAVE_KERNEL_LZMA=y
kali@kali:~$ CONFIG_RD_LZMA=y
kali@kali:~$
kali@kali:~$ # If cross compiling, run this once
kali@kali:~$ sed -i 's/if defined(__linux__)/if defined(__linux__) ||defined(__KERNEL__) /g' include/uapi/drm/drm.h
kali@kali:~$
kali@kali:~$ make -j $(cat /proc/cpuinfo|grep processor | wc -l)
kali@kali:~$ make modules_install INSTALL_MOD_PATH=~/arm-stuff/images/root/
```

Chroot into the rootfs and create an [initrd](https://en.wikipedia.org/wiki/Initrd). Make sure to use the correct kernel version/extraversion for the **mkinitramfs** command. In our case, it was "3.8.13".

```console
kali@kali:~$ LANG=C chroot ~/arm-stuff/images/root/
kali@kali:~$ sudo apt install -y initramfs-tools uboot-mkimage
kali@kali:~$ cd /
kali@kali:~$ # Change the example "3.8.13" to your current odroid kernel revision
kali@kali:~$ mkinitramfs -c lzma -o ./initramfs 3.8.13
kali@kali:~$ mkimage -A arm -O linux -T ramdisk -C none -a 0 -e 0 -n initramfs -d ./initramfs ./uInitrd
kali@kali:~$ rm initramfs
kali@kali:~$ exit
```

#### 06. Prepare the Boot Partition

Copy the kernel and generated initrd file to the mounted boot partition as shown below.

```console
kali@kali:~$ mv ~/arm-stuff/images/root/uInitrd ~/arm-stuff/images/boot/
kali@kali:~$ cp arch/arm/boot/zImage ~/arm-stuff/images/boot/
```

Dump a **boot.txt** file, which contains required boot parameters for the ODROID in the boot partition.

```console
kali@kali:~$ cat <<EOF > ~/arm-stuff/images/boot/boot.txt
setenv initrd_high "0xffffffff"
setenv fdt_high "0xffffffff"
setenv bootcmd "fatload mmc 0:1 0x40008000 zImage; fatload mmc 0:1 0x42000000 uInitrd; bootm 0x40008000 0x42000000"
setenv bootargs "console=tty1 console=ttySAC1,115200n8 root=LABEL=kaliroot rootwait ro mem=2047M"
boot
EOF
```

Generate a `boot.scr` file, which is required to boot the ODROID.

```console
kali@kali:~$ mkimage -A arm -T script -C none -n "Boot.scr for ODROID" -d ~/arm-stuff/images/boot/boot.txt ~/arm-stuff/images/boot/boot.scr
```

Unmount the root and boot partitions, then umount the loop device.

```console
kali@kali:~$ cd ~/arm-stuff/images/
kali@kali:~$ umount $bootp
kali@kali:~$ umount $rootp
kali@kali:~$ kpartx -dv $loopdevice
kali@kali:~$
kali@kali:~$ wget http://www.mdrjr.net/odroid/mirror/old-releases/BSPs/Alpha4/unpacked/boot.tar.gz
kali@kali:~$ tar -zxpf boot.tar.gz
kali@kali:~$ cd boot/
kali@kali:~$ sh sd_fusing.sh $loopdevice
kali@kali:~$ cd ../
kali@kali:~$ losetup -d $loopdevice
```

Now, image the file onto your USB storage device. Our device is **/dev/sdb**. Change this as needed.

```console
kali@kali:~$ dd if=kali-linux-odroid.img of=/dev/sdb bs=4M
```

Once this operation is complete, connect your UART serial cable to the ODROID and boot it up with the microSD/SD card plugged in. Through the serial console, you will be able to log in to Kali (root / toor) and startx.

If everything works and you want the ODROID to start on boot, make sure to use the "autologin" line in the inittab given above and add the following to your bash_profile:

```console
# If you don't have a .bash_profile, copy it from /etc/skel/.profile first
kali@kali:~$ cat <<EOF >> ~/.bash_profile
if [ -z "$DISPLAY" ] && [ $(tty) = /dev/ttySAC1 ]; then
startx
fi
EOF
```

#### 08. Install Mali Graphic Drivers (Optional)

These steps are experimental and not fully tested yet. They should be performed inside the Kali rootfs.

```console
kali@kali:~$ # http://malideveloper.arm.com/develop-for-mali/drivers/open-source-mali-gpus-linux-exadri2-and-x11-display-drivers/
kali@kali:~$ sudo apt install -y build-essential autoconf automake make libtool xorg xorg-dev xutils-dev libdrm-dev
kali@kali:~$ wget http://malideveloper.arm.com/downloads/drivers/DX910/r3p2-01rel0/DX910-SW-99003-r3p2-01rel0.tgz
kali@kali:~$ wget http://malideveloper.arm.com/downloads/drivers/DX910/r3p2-01rel0/DX910-SW-99006-r3p2-01rel0.tgz
kali@kali:~$ wget --no-check-certificate https://dl.dropbox.com/u/65312725/mali_opengl_hf_lib.tgz
kali@kali:~$
kali@kali:~$ tar -xzvf mali_opengl_hf_lib.tgz
kali@kali:~$ cp mali_opengl_hf_lib/* /usr/lib/
kali@kali:~$
kali@kali:~$ tar -xzvf DX910-SW-99003-r3p2-01rel0.tgz
kali@kali:~$ tar -xzvf DX910-SW-99006-r3p2-01rel0.tgz
kali@kali:~$ cd DX910-SW-99003-r3p2-01rel0/x11/xf86-video-mali-0.0.1/
kali@kali:~$ ./autogen.sh
kali@kali:~$ chmod +x configure
kali@kali:~$
kali@kali:~$ CFLAGS="-O3 -Wall -W -Wextra -I/usr/include/libdrm -IDX910-SW-99006-r3p2-01rel0/driver/src/ump/include" LDFLAGS="-L/usr/lib -lMali -lUMP -lpthread" ./configure --prefix=/usr --x-includes=/usr/include --x-libraries=/usr/lib
kali@kali:~$ cp -rf ../../../DX910-SW-99006-r3p2-01rel0/driver/src/ump/include/ump src/
kali@kali:~$ mkdir -p umplock/
kali@kali:~$ cd umplock/
kali@kali:~$ wget http://service.i-onik.de/a10_source_1.5/lichee/linux-3.0/modules/mali/DX910-SW-99002-r3p0-04rel0/driver/src/devicedrv/umplock/umplock_ioctl.h
kali@kali:~$ cd ../
kali@kali:~$
kali@kali:~$ make
kali@kali:~$ make install
```
