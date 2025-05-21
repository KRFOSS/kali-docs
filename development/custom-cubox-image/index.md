---
title: 커스텀 CuBox 이미지
description:
icon:
weight:
author: ["steev",]
번역: ["xenix4845"]
---

이 문서는 **커스텀 칼리 리눅스 CuBox ARM 이미지**를 만드는 우리만의 방법을 설명하며 개발자를 대상으로 해요. 미리 만들어진 Kali 이미지를 설치하고 싶다면, [CuBox에 Kali 설치하기](/docs/arm/cubox/) 문서를 참고하세요.

{{% notice info %}}
이 과정을 수행하려면 루트 권한이 필요하거나, "sudo su" 명령어로 권한을 상승시킬 수 있어야 해요.
{{% /notice %}}

### 01. Kali rootfs 만들기

Kali 문서에 설명된 대로 **armhf** 아키텍처를 사용하여 [Kali rootfs(루트 파일 시스템)](/docs/development/kali-linux-arm-chroot/)를 빌드하세요. 이 과정이 끝나면 `~/arm-stuff/rootfs/kali-armhf`에 필요한 파일이 채워진 rootfs 디렉토리가 있어야 해요.

### 02. 이미지 파일 만들기

다음으로, CuBox rootfs와 부팅 이미지를 담을 물리적 이미지 파일을 만들어요:

```console
kali@kali:~$ sudo apt install -y kpartx xz-utils sharutils
kali@kali:~$ mkdir -p ~/arm-stuff/images/
kali@kali:~$ cd ~/arm-stuff/images/
kali@kali:~$ dd if=/dev/zero of=kali-custom-cubox.img conv=fsync bs=4M count=7000
```

### 03. 이미지 파일 파티션 나누기 및 마운트하기

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

### 04. Kali rootfs 복사 및 수정하기

```console
kali@kali:~$ rsync -HPavz /root/arm-stuff/rootfs/kali-armhf/ root
kali@kali:~$ echo nameserver 8.8.8.8 > root/etc/resolv.conf
```

### 05. CuBox 커널 및 모듈 컴파일하기

개발 환경으로 ARM 하드웨어를 사용하지 않는 경우, ARM 커널 및 모듈을 빌드하려면 [ARM 크로스 컴파일 환경](/docs/development/arm-cross-compilation-environment/)을 설정해야 해요. 완료되면 다음 지침을 따르세요:

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

# 커널 설정하기!
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

{{% notice info %}}
명령어에 '/dev/sdX'가 사용되지만, '/dev/sdX'는 적절한 장치 라벨로 바꿔야 해요. '/dev/sdX'는 어떤 장치도 덮어쓰지 않으며, 실수로 덮어쓰는 것을 방지하기 위해 문서에서 안전하게 사용될 수 있어요. 올바른 장치 라벨을 사용해주세요.
{{% /notice %}}

**[dd](https://manpages.debian.org/testing/coreutils/dd.1.en.html)** 명령어를 사용하여 이 파일을 SD 카드에 이미징하세요. 아래 예시에서는 저장 장치가 `/dev/sdX`에 위치한다고 가정해요. **필요에 따라 변경하세요**:

```console
kali@kali:~$ dd if=kali-linux-cubox.img of=/dev/sdX conv=fsync bs=4M
```

dd 작업이 완료되면, SD 카드를 마운트 해제하고 꺼낸 다음 CuBox를 칼리 리눅스로 부팅하세요
