---
title: 커스텀 Raspberry Pi 이미지
description:
icon:
weight:
author: ["steev",]
번역: ["kmw0410"]
---

이 문서에서는 개발자를 대상으로 **커스텀 칼리 리눅스 Raspberry Pi ARM 이미지**를 만드는 방법에 대해 설명하고 있어요. 미리 만들어진 Kali 이미지로 설치하려면 [Raspberry Pi에 Kali 설치](/docs/arm/raspberry-pi/) 문서를 확인하세요

{{% notice info %}}
이 절차를 진행하기 위해서는 root 권한이나 "sudo su"로 루트 권한을 얻을 수 있는 권한이 필요해요.
{{% /notice %}}

### 01. Kali rootfs 생성

문서에 설명된 대로 [Kali rootfs](/docs/development/kali-linux-arm-chroot/)를 빌드하려면 **armel** 아키텍처를 사용하세요. 이 프로세스를 끝나면 rootfs 디렉토리가 **~/arm-stuff/rootfs/kali-armel**에 채워져 있어야 해요.

### 02. 이미지 파일 생성

다음으로, 이미지 파일을 만들면 Raspberry Pi의 rootfs와 부트 이미지가 보관돼요:

```console
kali@kali:~$ sudo apt install -y kpartx xz-utils sharutils
kali@kali:~$ mkdir -p ~/arm-stuff/images/
kali@kali:~$ cd ~/arm-stuff/images/
kali@kali:~$ dd if=/dev/zero of=kali-custom-rpi.img conv=fsync bs=4M count=7000
```

### 03. 파티션과 이미지 파일 마운트

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

### 04. Kali rootfs 복사 및 수정

```console
kali@kali:~$ rsync -HPavz /root/arm-stuff/rootfs/kali-armel/ root
kali@kali:~$ echo nameserver 8.8.8.8 > root/etc/resolv.conf
```

### 05. Raspberry Pi 커널과 모듈 컴파일

개발 환경에서 ARM 하드웨어를 사용하지 않으면 ARM 커널과 모듈을 빌드하는 데 [ARM 크로스 컴파일 환경](/docs/development/arm-cross-compilation-environment/) 설치가 필요할 수 있어요. 완료되면 다음 단계를 진행 할 수 있어요:

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

{{% notice info %}}
명령어에서 '/dev/sdX'를 사용할 때, '/dev/sdX'는 적절한 장치 라벨로 변경해야 해요. '/dev/sdX'는 장치를 덮어씌우지 않아 문서에서 안전하게 사용할 수 있어요. 맞는 장치 라벨을 사용하세요.
{{% /notice %}}

**[dd](https://manpages.debian.org/testing/coreutils/dd.1.en.html)** 명령어를 사용하여 SD카드에 이미지를 쓰세요. 우리는 예시로 저장장치 위치를 `/dev/sdX`로 가정했어요. **변경이 필요해요**:

```console
kali@kali:~$ dd if=kali-linux-rpi.img of=/dev/sdX conv=fsync bs=4M
```

dd 작업이 완료되면 SD 카드 마운트를 해제하고 Pi를 칼리 리눅스로 부팅하세요
