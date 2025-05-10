---
title: 커스텀 MK/SS808 이미지
description:
icon:
weight:
author: ["steev",]
번역: ["xenix4845"]
---

이 문서는 **커스텀 Kali Linux MK/SS808 ARM 이미지**를 만드는 우리만의 방법을 설명하며 개발자를 대상으로 해요. 미리 만들어진 Kali 이미지를 설치하고 싶다면, [MK/SS808에 Kali 설치하기](/docs/arm/ss808-mk808/) 문서를 참고하세요.

{{% notice info %}}
이 과정을 수행하려면 루트 권한이 필요하거나, "sudo su" 명령어로 권한을 상승시킬 수 있어야 해요.
{{% /notice %}}

### 01. Kali rootfs 만들기

Kali 문서에 설명된 대로 **armhf** 아키텍처를 사용하여 [Kali rootfs(루트 파일 시스템)](/docs/development/kali-linux-arm-chroot/)를 빌드하세요. 이 과정이 끝나면 `~/arm-stuff/rootfs/kali-armhf`에 필요한 파일이 채워진 rootfs 디렉토리가 있어야 해요.

### 02. 이미지 파일 만들기

다음으로, MK/SS808 rootfs와 부팅 이미지를 담을 물리적 이미지 파일을 만들어요:

```console
kali@kali:~$ sudo apt install -y kpartx xz-utils sharutils
kali@kali:~$ mkdir -p ~/arm-stuff/images/
kali@kali:~$ cd ~/arm-stuff/images/
kali@kali:~/arm-stuff/images$ dd if=/dev/zero of=kali-custom-ss808.img conv=fsync bs=4M count=7000
```

### 03. 이미지 파일 파티션 나누기 및 마운트하기

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

### 04. Kali rootfs 복사 및 수정하기

```console
kali@kali:~$ rsync -HPavz /root/arm-stuff/rootfs/kali-armhf-xfce4/ root
kali@kali:~$ echo nameserver 8.8.8.8 > root/etc/resolv.conf
```

### 05. rk3066 커널 및 모듈 컴파일하기

개발 환경으로 ARM 하드웨어를 사용하지 않는 경우, ARM 커널 및 모듈을 빌드하려면 [ARM 크로스 컴파일 환경](/docs/development/arm-cross-compilation-environment/)을 설정해야 해요. 완료되면 다음 단계를 따르세요:

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
kali@kali:~$ # UG802 및 MK802 III를 위한 기본 구성
kali@kali:~$ # make rk30_hotdog_ti_defconfig

kali@kali:~$ # MK808을 위한 기본 구성
kali@kali:~$ make rk30_hotdog_defconfig

kali@kali:~$ # 커널 구성하기!
kali@kali:~$ make menuconfig

kali@kali:~$ # http://www.armtvtech.com/armtvtechforum/viewtopic.php?f=66&t;=835에 따라 커널 구성하기
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

### 07. 이미지를 USB 장치에 dd로 복사하기

{{% notice info %}}
명령어에 '/dev/sdX'가 사용되지만, '/dev/sdX'는 적절한 장치 라벨로 바꿔야 해요. '/dev/sdX'는 어떤 장치도 덮어쓰지 않으며, 실수로 덮어쓰는 것을 방지하기 위해 문서에서 안전하게 사용될 수 있어요. 올바른 장치 라벨을 사용해주세요.
{{% /notice %}}

**[dd](https://manpages.debian.org/testing/coreutils/dd.1.en.html)** 명령어를 사용하여 이 파일을 SD 카드에 이미징하세요. 아래 예시에서는 저장 장치가 `/dev/sdX`에 위치한다고 가정해요. **필요에 따라 변경하세요**:

```console
kali@kali:~$ dd if=kali-linux-ss808.img of=/dev/sdX conv=fsync bs=4M
```

dd 작업이 완료되면, SD 카드를 마운트 해제하고 꺼낸 다음 MK/SS808을 Kali Linux로 부팅하세요
