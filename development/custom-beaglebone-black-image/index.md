---
title: 커스텀 Beaglebone Black 이미지
description:
icon:
weight:
author: ["steev",]
번역: ["xenix4845"]
---

이 문서는 **커스텀 칼리 리눅스 Beaglebone Black ARM 이미지**를 만드는 우리만의 방법을 설명하며 개발자를 대상으로 해요. 미리 만들어진 Kali 이미지를 설치하고 싶다면, [Beaglebone Black에 Kali 설치하기](/docs/arm/beaglebone-black/) 문서를 참고하세요.

{{% notice info %}}
이 과정을 수행하려면 루트 권한이 필요하거나, "sudo su" 명령어로 권한을 상승시킬 수 있어야 해요.
{{% /notice %}}

### 01. Kali rootfs 만들기

Kali 문서에 설명된 대로 **armhf** 아키텍처를 사용하여 [Kali rootfs(루트 파일 시스템)](/docs/development/kali-linux-arm-chroot/)를 빌드하세요. 이 과정이 끝나면 `~/arm-stuff/rootfs/kali-armhf`에 필요한 파일이 채워진 rootfs 디렉토리가 있어야 해요.

### 02. 이미지 파일 만들기

다음으로, Beaglebone Black rootfs와 부팅 이미지를 담을 물리적 이미지 파일을 만들어요:

```console
kali@kali:~$ sudo apt install -y kpartx xz-utils sharutils
kali@kali:~$ mkdir -p ~/arm-stuff/images/
kali@kali:~$ cd ~/arm-stuff/images/
kali@kali:~$ dd if=/dev/zero of=kali-custom-bbb.img conv=fsync bs=4M count=7000
```

### 03. 이미지 파일 파티션 나누기 및 마운트하기

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

### 04. Kali rootfs 복사 및 수정하기

```console
kali@kali:~$ rsync -HPavz /root/arm-stuff/rootfs/kali-armhf/ root
kali@kali:~$ echo nameserver 8.8.8.8 > root/etc/resolv.conf
```

### 05. Beaglebone Black 커널 및 모듈 컴파일하기

ARM 하드웨어를 개발 환경으로 사용하지 않는 경우, ARM 커널 및 모듈을 빌드하기 위해 [ARM 크로스 컴파일 환경](/docs/development/arm-cross-compilation-environment/)을 설정해야 합니다. 설정이 완료되면 다음 지침을 따르세요:

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

{{% notice info %}}
명령어에서 '/dev/sdX'가 사용되지만, '/dev/sdX'는 적절한 장치 레이블로 대체되어야 해요. '/dev/sdX'는 어떤 장치도 덮어쓰지 않으며, 문서에서 안전하게 사용할 수 있습니다. 올바른 장치 레이블을 사용하세요.
{{% /notice %}}

**[dd](https://manpages.debian.org/testing/coreutils/dd.1.en.html)** 명령어를 사용하여 이 파일을 SD 카드에 이미지화하세요. 예제에서는 저장 장치가 `/dev/sdX`에 위치한다고 가정할거에요. **필요에 따라 변경하세요!**:

```console
kali@kali:~$ dd if=kali-linux-bbb.img of=/dev/sdX conv=fsync bs=4M
```

dd 작업이 완료되면 SD 카드를 마운트 해제하고 꺼내서 Beaglebone Black을 칼리 리눅스로 부팅하세요. 부팅할 때 "BOOT" 버튼을 누르고 있어야 해요. 이 버튼은 microSD 카드에 가장 가까운 버튼이에요.
