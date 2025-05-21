---
title: 커스텀 EfikaMX 이미지
description:
icon:
weight:
author: ["steev",]
번역: ["xenix4845"]
---

이 문서는 **커스텀 칼리 리눅스 EfikaMX ARM 이미지**를 만드는 우리만의 방법을 설명하며 개발자를 대상으로 해요. 미리 만들어진 Kali 이미지를 설치하고 싶다면, [EfikaMX에 Kali 설치하기](/docs/arm/efikamx/) 문서를 참고하세요.

{{% notice info %}}
이 과정을 수행하려면 루트 권한이 필요하거나, "sudo su" 명령어로 권한을 상승시킬 수 있어야 해요.
{{% /notice %}}

### 01. Kali rootfs 만들기

Kali 문서에 설명된 대로 **armhf** 아키텍처를 사용하여 [Kali rootfs(루트 파일 시스템)](/docs/development/kali-linux-arm-chroot/)를 빌드하세요. 이 과정이 끝나면 `~/arm-stuff/rootfs/kali-armhf`에 필요한 파일이 채워진 rootfs 디렉토리가 있어야 해요.

### 02. 이미지 파일 만들기

다음으로, EfikaMX rootfs와 부팅 이미지를 담을 물리적 이미지 파일을 만들어요:

```console
kali@kali:~$ sudo apt install -y kpartx xz-utils sharutils
kali@kali:~$ mkdir -p ~/arm-stuff/images/
kali@kali:~$ cd ~/arm-stuff/images/
kali@kali:~$ dd if=/dev/zero of=kali-custom-efikamx.img conv=fsync bs=4M count=7000
```

### 03. 이미지 파일 파티션 나누기 및 마운트하기

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

### 04. Kali rootfs 복사 및 수정하기

```console
kali@kali:~$ rsync -HPavz /root/arm-stuff/rootfs/kali-armhf/ root
kali@kali:~$ echo nameserver 8.8.8.8 > root/etc/resolv.conf
kali@kali:~$ sed 's/0-1/0//g' root/etc/init.d/udev
```

### 05. EfikaMX 커널 및 모듈 컴파일하기

개발 환경으로 ARM 하드웨어를 사용하지 않는 경우, ARM 커널 및 모듈을 빌드하려면 [ARM 크로스 컴파일 환경](/docs/development/arm-cross-compilation-environment/)을 설정해야 해요. 완료되면 다음 지침을 따르세요:

```console
kali@kali:~$ mkdir -p ~/arm-stuff/kernel/
kali@kali:~$ cd ~/arm-stuff/kernel/
kali@kali:~$ git clone --depth 1 https://github.com/genesi/linux-legacy.git
kali@kali:~$ cd linux-legacy/
kali@kali:~$ export ARCH=arm
kali@kali:~$ export CROSS_COMPILE=~/arm-stuff/kernel/toolchains/arm-eabi-linaro-4.6.2/bin/arm-eabi-
kali@kali:~$ make efikamx_defconfig

# 커널 설정하기!
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

{{% notice info %}}
명령어에 '/dev/sdX'가 사용되지만, '/dev/sdX'는 적절한 장치 라벨로 바꿔야 해요. '/dev/sdX'는 어떤 장치도 덮어쓰지 않으며, 실수로 덮어쓰는 것을 방지하기 위해 문서에서 안전하게 사용될 수 있어요. 올바른 장치 라벨을 사용해주세요.
{{% /notice %}}

**[dd](https://manpages.debian.org/testing/coreutils/dd.1.en.html)** 명령어를 사용하여 이 파일을 SD 카드에 이미징하세요. 아래 예시에서는 저장 장치가 `/dev/sdX`에 위치한다고 가정해요. **필요에 따라 변경하세요**:

```console
kali@kali:~$ dd if=kali-linux-efikamx.img of=/dev/sdX conv=fsync bs=4M
```

dd 작업이 완료되면, SD 카드를 마운트 해제하고 꺼낸 다음 EfikaMX를 칼리 리눅스로 부팅하세요
