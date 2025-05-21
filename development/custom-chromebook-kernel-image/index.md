---
title: 커스텀 Chromebook 이미지
description:
icon:
weight:
author: ["steev",]
번역: ["xenix4845"]
---

이 문서는 **커스텀 칼리 리눅스 Samsung Chromebook ARM 이미지**를 만드는 우리만의 방법을 설명하며 개발자를 대상으로 해요. 미리 만들어진 칼리 이미지를 설치하고 싶다면, [Samsung Chromebook에 Kali 설치하기](/docs/arm/chromebook-exynos/) 문서를 참고하세요.

{{% notice info %}}
이 가이드에서는 두 개의 부트 파티션이 있는 이미지를 만들어요 - 하나는 SD 카드에서 부팅하도록 하드코딩된 커널을 포함하고, 다른 하나는 USB에서 부팅하도록 하드코딩된 커널을 포함해요. USB 저장 장치 유형에 따라 이 가이드의 마지막 단계에서 설명하는 대로 이미지를 USB 장치에 dd로 복사한 후 관련 부트 파티션에 더 높은 우선순위를 표시해야 해요.
{{% /notice %}}

{{% notice info %}}
이 절차를 수행하려면 루트 권한이 필요하거나, "sudo su" 명령어로 권한을 상승시킬 수 있어야 해요.
{{% /notice %}}

#### 01. Kali rootfs 만들기

칼리 문서에 설명된 대로 **armhf** 아키텍처를 사용하여 [Kali rootfs(루트 파일 시스템)](/docs/development/kali-linux-arm-chroot/)를 빌드하는 것부터 시작하세요. 이 과정이 끝나면 `~/arm-stuff/rootfs/kali-armhf`에 필요한 파일이 채워진 rootfs 디렉토리가 있어야 해요.

#### 02. 이미지 파일 만들기

다음으로, Chromebook rootfs와 부팅 이미지를 담을 물리적 이미지 파일을 만들어요:

```console
kali@kali:~$ sudo apt install -y kpartx xz-utils gdisk uboot-mkimage u-boot-tools vboot-kernel-utils vboot-utils cgpt
kali@kali:~$ mkdir -p ~/arm-stuff/images/
kali@kali:~$ cd ~/arm-stuff/images/
kali@kali:~$ dd if=/dev/zero of=kali-custom-chrome.img conv=fsync bs=4M count=7000
```

#### 03. 이미지 파일 파티션 나누기 및 마운트하기

```console
kali@kali:~$ parted kali-custom-chrome.img --script -- mklabel msdos
kali@kali:~$ parted kali-custom-chrome.img --script -- mktable gpt
kali@kali:~$ gdisk kali-custom-chrome.img <<EOF
x
l
8192
m
n
1

+16M
7f00
n
2

+16M
7f00
n
3

w
y
EOF
```

```console
kali@kali:~$ loopdevice=`losetup -f --show kali-custom-chrome.img`
kali@kali:~$ device=`kpartx -va $loopdevice| sed -E 's/.*(loop[0-9])p.*/\1/g' | head -1`
kali@kali:~$ device="/dev/mapper/${device}"
kali@kali:~$ bootp1=${device}p1
kali@kali:~$ bootp2=${device}p2
kali@kali:~$ rootp=${device}p3

kali@kali:~$ mkfs.ext4 $rootp
kali@kali:~$ mkdir -p root
kali@kali:~$ mount $rootp root
```

#### 04. Kali rootfs 복사 및 수정하기

이전에 부트스트랩한 Kali rootfs를 **[rsync](https://packages.debian.org/testing/rsync)**를 사용하여 마운트된 이미지로 복사하세요:

```console
kali@kali:~$ cd ~/arm-stuff/images/
kali@kali:~$ rsync -HPavz ~/arm-stuff/rootfs/kali-armhf/ root
kali@kali:~$
kali@kali:~$ echo nameserver 8.8.8.8 > root/etc/resolv.conf
kali@kali:~$
kali@kali:~$ mkdir -p root/etc/X11/xorg.conf.d/
kali@kali:~$ cat <<EOF > root/etc/X11/xorg.conf.d/50-touchpad.conf
Section "InputClass"
Identifier "touchpad"
MatchIsTouchpad "on"
Driver "synaptics"
Option "TapButton1" "1"
Option "TapButton2" "3"
Option "TapButton3" "2"
Option "FingerLow" "15"
Option "FingerHigh" "20"
Option "FingerPress" "256"
EndSection
EOF
```

#### 05. Samsung Chromium 커널 및 모듈 컴파일하기

ARM 하드웨어를 개발 환경으로 사용하지 않는 경우, ARM 커널 및 모듈을 빌드하기 위해 [ARM 크로스 컴파일 환경](/docs/development/arm-cross-compilation-environment/)을 설정해야 해요. 설정이 완료되면 다음 지침을 따르세요.

Chromium 커널 소스를 가져와 개발 트리 구조에 배치하세요:

```console
kali@kali:~$ mkdir -p ~/arm-stuff/kernel/
kali@kali:~$ cd ~/arm-stuff/kernel/
kali@kali:~$ git clone http://git.chromium.org/chromiumos/third_party/kernel.git -b chromeos-3.4 chromeos
kali@kali:~$ cd chromeos/
```

```console
kali@kali:~$ cat <<EOF > kernel.its
/dts-v1/;

/ {
description = "Chrome OS kernel image with one or more FDT blobs";
#address-cells = ;
images {
kernel@1{
description = "kernel";
data = /incbin/("arch/arm/boot/zImage");
type = "kernel_noload";
arch = "arm";
os = "linux";
compression = "none";
load = ;
entry = ;
};
fdt@1{
description = "exynos5250-snow.dtb";
data = /incbin/("arch/arm/boot/exynos5250-snow.dtb");
type = "flat_dt";
arch = "arm";
compression = "none";
hash@1{
algo = "sha1";
};
};
};
configurations {
default = "conf@1";
conf@1{
kernel = "kernel@1";
fdt = "fdt@1";
};
};
};
EOF
```

커널을 패치하세요. 여기서는 무선 인젝션 패치를 사용합니다:

```console
kali@kali:~$ mkdir -p ../patches/
kali@kali:~$ wget http://patches.aircrack-ng.org/mac80211.compat08082009.wl_frag+ack_v1.patch -O ../patches/mac80211.patch
kali@kali:~$ wget http://patches.aircrack-ng.org/channel-negative-one-maxim.patch -O ../patches/negative.patch
kali@kali:~$ patch -p1 < ../patches/negative.patch
kali@kali:~$ patch -p1 < ../patches/mac80211.patch
```

Chromium 커널을 설정하고 크로스 컴파일하세요:

```console
kali@kali:~$ export ARCH=arm
kali@kali:~$ export CROSS_COMPILE=~/arm-stuff/kernel/toolchains/arm-eabi-linaro-4.6.2/bin/arm-eabi-
kali@kali:~$
kali@kali:~$ ./chromeos/scripts/prepareconfig chromeos-exynos5

# LSM 비활성화
kali@kali:~$ sed -i 's/CONFIG_SECURITY_CHROMIUMOS=y/# CONFIG_SECURITY_CHROMIUMOS is not set/g' .config

# 크로스 컴파일 시, 한 번만 수행:
kali@kali:~$ sed -i 's/if defined(__linux__)/if defined(__linux__) ||defined(__KERNEL__) /g' include/drm/drm.h

kali@kali:~$ make menuconfig
kali@kali:~$ make -j$(cat /proc/cpuinfo|grep processor | wc -l)
kali@kali:~$ make dtbs
kali@kali:~$ cp ./scripts/dtc/dtc /usr/bin/
kali@kali:~$ mkimage -f kernel.its kernel.itb
kali@kali:~$ make modules_install INSTALL_MOD_PATH=~/arm-stuff/images/root/

# 펌웨어 복사. Chromebook의 원래 펌웨어(/lib/firmware)를 사용하는 것이 이상적입니다.
kali@kali:~$ git clone git://git.kernel.org/pub/scm/linux/kernel/git/dwmw2/linux-firmware.git
kali@kali:~$ cp -rf linux-firmware/* ~/arm-stuff/images/root/lib/firmware/
kali@kali:~$ rm -rf linux-firmware
```

```console
kali@kali:~$ echo "console=tty1 debug verbose root=/dev/mmcblk1p3 rootwait rw rootfstype=ext4" > /tmp/config-sd
kali@kali:~$ echo "console=tty1 debug verbose root=/dev/sda3 rootwait rw rootfstype=ext4" > /tmp/config-usb
kali@kali:~$
kali@kali:~$ vbutil_kernel --pack /tmp/newkern-sd --keyblock /usr/share/vboot/devkeys/kernel.keyblock --version 1 --signprivate /usr/share/vboot/devkeys/kali@kali:~$ kernel_data_key.vbprivk --config=/tmp/config-sd --vmlinuz kernel.itb --arch arm
kali@kali:~$ vbutil_kernel --pack /tmp/newkern-usb --keyblock /usr/share/vboot/devkeys/kernel.keyblock --version 1 --signprivate /usr/share/vboot/devkeys/kali@kali:~$ kernel_data_key.vbprivk --config=/tmp/config-usb --vmlinuz kernel.itb --arch arm
```

#### 06. 부트 파티션 준비하기

```console
kali@kali:~$ dd if=/tmp/newkern-sd of=$bootp1 conv=fsync # SD용 첫 번째 부트 파티션
kali@kali:~$ dd if=/tmp/newkern-usb of=$bootp2 conv=fsync # USB용 두 번째 부트 파티션
kali@kali:~$
kali@kali:~$ umount $rootp
kali@kali:~$
kali@kali:~$ kpartx -dv $loopdevice
kali@kali:~$ losetup -d $loopdevice
```

#### 07. 이미지 dd 및 USB 드라이브 부팅 가능 설정

{{% notice info %}}
'/dev/sdX'가 명령어에서 사용되지만, '/dev/sdX'는 적절한 장치 레이블로 대체되어야 해요. '/dev/sdX'는 어떤 장치도 덮어쓰지 않으며, 문서에서 안전하게 사용될 수 있어요. 올바른 장치 레이블을 사용하세요.
{{% /notice %}}

```console
kali@kali:~$ dd if=kali-linux-chrome.img of=/dev/sdX conv=fsync bs=4M
kali@kali:~$ cgpt repair /dev/sdX
```

{{% notice info %}}
이 시점에서 부트 파티션 1 또는 2 중 하나에 더 높은 우선순위를 표시해야 해요. 더 높은 우선순위를 가진 번호가 먼저 부팅됩니다. 아래 예시는 첫 번째 파티션(-i)에 우선순위 10을 부여하여 SD 카드에서 성공적으로 부팅되도록 합니다.
{{% /notice %}}

```console
kali@kali:~$ cgpt add -i 1 -S 1 -T 5 -P 10 -l KERN-A /dev/sdX
kali@kali:~$ cgpt add -i 2 -S 1 -T 5 -P 5 -l KERN-B /dev/sdX
```

파티션 목록과 순서를 확인하려면 **cgpt show** 명령어를 사용하세요:

```console
kali@kali:~$ cgpt show /dev/sdX
start size part contents
0 1 PMBR
1 1 Pri GPT header
2 32 Pri GPT table
8192 32768 1 Label: "KERN-A"
Type: ChromeOS kernel
UUID: 63AD6EC9-AD94-4B42-80E4-798BBE6BE46C
Attr: priority=10 tries=5 successful=1
40960 32768 2 Label: "KERN-B"
Type: ChromeOS kernel
UUID: 37CE46C9-0A7A-4994-80FC-9C0FFCB4FDC1
Attr: priority=5 tries=5 successful=1
73728 3832490 3 Label: "Linux filesystem"
Type: 0FC63DAF-8483-4772-8E79-3D69D8477DE4
UUID: E9E67EE1-C02E-481C-BA3F-18E721515DBB
125045391 32 Sec GPT table
125045423 1 Sec GPT header
kali@kali:~$
```

이 작업이 완료되면 SD/USB 장치가 연결된 상태에서 Samsung Chromebook을 부팅하세요. 개발자 모드 부팅 화면에서 CTRL+u를 눌러 USB 저장 장치에서 부팅하세요. 그런 다음 Kali에 로그인(`root` / `toor`)하고 `startx`를 실행하세요.
