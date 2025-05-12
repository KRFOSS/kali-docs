---
title: 커스텀 ODROID X2 U2 이미지
description:
icon:
weight:
author: ["steev",]
번역: ["xenix4845"]
---

이 문서는 **커스텀 Kali Linux ODROID 이미지**를 만드는 우리만의 방법을 설명하며 개발자를 대상으로 해요. 미리 만들어진 Kali ODROID 이미지를 설치하고 싶다면, [ODROID에 Kali 설치하기](/docs/arm/odroid-u/) 문서를 참고하세요.

{{% notice info %}}
이 과정을 수행하려면 루트 권한이 필요하거나, "sudo su" 명령어로 권한을 상승시킬 수 있어야 해요.
{{% /notice %}}

#### 01. Kali rootfs 만들기

Kali 문서에 설명된 대로 **armhf** 아키텍처를 사용하여 [Kali rootfs(루트 파일 시스템)](/docs/development/kali-linux-arm-chroot/)를 빌드하는 것부터 시작하세요. 이 과정이 끝나면 `~/arm-stuff/rootfs/kali-armhf`에 필요한 파일이 채워진 rootfs 디렉토리가 있어야 해요.

#### 02. 이미지 파일 만들기

다음으로, ODROID rootfs와 부팅 이미지를 담을 물리적 이미지 파일을 만들어요:

```console
kali@kali:~$ sudo apt install -y kpartx xz-utils uboot-mkimage
kali@kali:~$ mkdir -p ~/arm-stuff/images/
kali@kali:~$ cd ~/arm-stuff/images/
kali@kali:~$ dd if=/dev/zero of=kali-custom-odroid.img conv=fsync bs=4M count=7000
```

#### 03. 이미지 파일 파티션 나누기 및 마운트하기

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

#### 04. Kali rootfs 복사 및 수정하기

이전에 부트스트랩한 Kali rootfs를 **rsync**를 사용하여 마운트된 이미지에 복사하세요:

```console
kali@kali:~$ cd ~/arm-stuff/images/
kali@kali:~$ rsync -HPavz ~/arm-stuff/rootfs/kali-armhf/ root
kali@kali:~$ echo nameserver 8.8.8.8 > root/etc/resolv.conf
```

**~/arm-stuff/images/root/etc/inittab** 파일을 편집하고 "시리얼 라인에 getty를 넣는 예제" 부분을 찾으세요:

```console
kali@kali:~$ vim root/etc/inittab
```

해당 섹션 끝에 다음 줄을 추가하세요:

```plaintext
T1:12345:respawn:/sbin/agetty 115200 ttySAC1 vt100
```

루트로 자동 로그인하는 시리얼 콘솔을 원한다면 대신 다음 줄을 사용하세요:

```plaintext
T1:12345:respawn:/bin/login -f root ttySAC1 /dev/ttySAC1 >&1
```

이제 **~/arm-stuff/images/root/etc/udev/links.conf** 파일에 _ttySAC1_ 항목이 있는지 확인하세요:

```console
kali@kali:~$ vim root/etc/udev/links.conf
```

_ttySAC1_에 대한 항목이 아직 없다면, 파일에 추가하여 다음과 같이 보이도록 하세요:

```plaintext
M null c 1 3
M console c 5 1
M ttySAC1 c 5 1
```

**~/arm-stuff/images/root/etc/udev/links.conf** 파일에 _ttySAC_ 항목들을 추가하세요:

```console
kali@kali:~$ cat <<EOF >> root/etc/securetty
ttySAC0
ttySAC1
ttySAC2
EOF
```

rootfs에 기본 xorg.conf 파일을 배치하세요:

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

루트 디렉토리에 **init**을 링크하세요:

```console
kali@kali:~$ cd ~/arm-stuff/images/root/
kali@kali:~$ ln -s /sbin/init init
```

#### 05. ODROID 커널 및 모듈 컴파일하기

개발 환경으로 ARM 하드웨어를 사용하지 않는 경우, ARM 커널 및 모듈을 빌드하려면 [ARM 크로스 컴파일 환경](/docs/development/arm-cross-compilation-environment/)을 설정해야 해요. 완료되면 다음 지침을 따르세요.

다음으로 ODROID 커널 소스를 가져와 개발 트리 구조에 배치해야 해요:

```console
kali@kali:~$ mkdir -p ~/arm-stuff/kernel/
kali@kali:~$ cd ~/arm-stuff/kernel/
kali@kali:~$ git clone --depth 1 https://github.com/hardkernel/linux.git -b odroid-3.8.y odroid
kali@kali:~$ cd odroid/
kali@kali:~$ touch .scmversion
```

ODROID 커널을 설정하고 크로스 컴파일하세요:

```console
kali@kali:~$ export ARCH=arm
kali@kali:~$ export CROSS_COMPILE=~/arm-stuff/kernel/toolchains/arm-eabi-linaro-4.6.2/bin/arm-eabi-
kali@kali:~$
kali@kali:~$ # ODROID-X2용
kali@kali:~$ make odroidx2_defconfig
kali@kali:~$ # ODROID-U2용
kali@kali:~$ make odroidu2_defconfig
kali@kali:~$ # 커널 설정하기!
kali@kali:~$ make menuconfig
kali@kali:~$ # 다음을 활성화하세요
kali@kali:~$ CONFIG_HAVE_KERNEL_LZMA=y
kali@kali:~$ CONFIG_RD_LZMA=y
kali@kali:~$
kali@kali:~$ # 크로스 컴파일을 한다면 이 명령어를 한 번 실행하세요
kali@kali:~$ sed -i 's/if defined(__linux__)/if defined(__linux__) ||defined(__KERNEL__) /g' include/uapi/drm/drm.h
kali@kali:~$
kali@kali:~$ make -j $(cat /proc/cpuinfo|grep processor | wc -l)
kali@kali:~$ make modules_install INSTALL_MOD_PATH=~/arm-stuff/images/root/
```

rootfs로 chroot하고 [initrd](https://en.wikipedia.org/wiki/Initrd)(초기 램디스크)를 만드세요. **mkinitramfs** 명령어에 올바른 커널 버전/추가 버전을 사용해야 해요. 우리의 경우에는 "3.8.13"이었어요:

```console
kali@kali:~$ LANG=C chroot ~/arm-stuff/images/root/
kali@kali:~$ sudo apt install -y initramfs-tools uboot-mkimage
kali@kali:~$ cd /
kali@kali:~$ # 예제의 "3.8.13"을 현재 odroid 커널 버전으로 변경하세요
kali@kali:~$ mkinitramfs -c lzma -o ./initramfs 3.8.13
kali@kali:~$ mkimage -A arm -O linux -T ramdisk -C none -a 0 -e 0 -n initramfs -d ./initramfs ./uInitrd
kali@kali:~$ rm initramfs
kali@kali:~$ exit
```

#### 06. 부트 파티션 준비하기

아래와 같이 커널과 생성된 initrd 파일을 마운트된 부트 파티션에 복사하세요:

```console
kali@kali:~$ mv ~/arm-stuff/images/root/uInitrd ~/arm-stuff/images/boot/
kali@kali:~$ cp arch/arm/boot/zImage ~/arm-stuff/images/boot/
```

ODROID에 필요한 부팅 매개변수를 포함하는 **boot.txt** 파일을 부트 파티션에 만드세요:

```console
kali@kali:~$ cat <<EOF > ~/arm-stuff/images/boot/boot.txt
setenv initrd_high "0xffffffff"
setenv fdt_high "0xffffffff"
setenv bootcmd "fatload mmc 0:1 0x40008000 zImage; fatload mmc 0:1 0x42000000 uInitrd; bootm 0x40008000 0x42000000"
setenv bootargs "console=tty1 console=ttySAC1,115200n8 root=LABEL=kaliroot rootwait ro mem=2047M"
boot
EOF
```

ODROID 부팅에 필요한 `boot.scr` 파일을 생성하세요:

```console
kali@kali:~$ mkimage -A arm -T script -C none -n "Boot.scr for ODROID" -d ~/arm-stuff/images/boot/boot.txt ~/arm-stuff/images/boot/boot.scr
```

루트 및 부트 파티션을 마운트 해제한 다음 루프 장치를 마운트 해제하세요:

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

{{% notice info %}}
명령어에 '/dev/sdX'가 사용되지만, '/dev/sdX'는 적절한 장치 라벨로 바꿔야 해요. '/dev/sdX'는 어떤 장치도 덮어쓰지 않으며, 실수로 덮어쓰는 것을 방지하기 위해 문서에서 안전하게 사용될 수 있어요. 올바른 장치 라벨을 사용해주세요.
{{% /notice %}}

이제 파일을 USB 저장 장치에 이미징하세요. 우리의 장치는 **/dev/sdX**입니다. 필요에 따라 변경하세요:

```console
kali@kali:~$ dd if=kali-linux-odroid.img of=/dev/sdX conv=fsync bs=4M
```

이 작업이 완료되면 UART 시리얼 케이블을 ODROID에 연결하고 microSD/SD 카드를 꽂은 상태로 부팅하세요. 시리얼 콘솔을 통해 Kali(`root` / `toor`)에 로그인하고 `startx`를 실행할 수 있어요.

모든 것이 잘 작동하고 ODROID가 부팅 시 자동으로 시작되길 원한다면, 위에서 제공한 inittab의 "autologin" 줄을 사용하고 다음을 `bash_profile`에 추가하세요:

```console
# .bash_profile이 없다면 먼저 /etc/skel/.profile에서 복사하세요
kali@kali:~$ cat <<EOF >> ~/.bash_profile
if [ -z "$DISPLAY" ] && [ $(tty) = /dev/ttySAC1 ]; then
startx
fi
EOF
```

#### 08. Mali 그래픽 드라이버 설치하기 (선택 사항)

이 단계는 실험적이며 아직 완전히 테스트되지 않았어요. Kali rootfs 내부에서 수행해야 해요:

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
