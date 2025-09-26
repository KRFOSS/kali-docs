---
title: 라즈베리 파이 - 전체 디스크 암호화
description:
icon:
archived: "false"
draft: false
weight:
author: ["gamb1t", "steev"]
번역: ["xenix4845"]
---

## 개요

기술적인 세부 사항을 본격적으로 살펴보기 전에, 우리가 달성하고자 하는 목표와 그 과정을 간략하게 알아보겠습니다:

- [라즈베리 파이 4](/docs/arm/raspberry-pi-4/)(이하 "RPi")에 [칼리 리눅스 설치](#installing-kali-linux-on-rpi)
- 원격 디스크 잠금 해제를 위한 [시스템 준비](#preparing-the-system)
- 원격 잠금 해제를 위한 [SSH 키 설정](#)(initramfs와 Dropbear 사용)
- 기존 데이터 [백업](#configuring-remote-ssh-unlock)
- 암호화된 파티션 [구성](#configuring-for-encryption)
- 데이터 [복원](#restore-our-data)
- **해킹 가즈아**!

많은 단계가 있지만 실제로는 꽤 간단합니다. 완료되면 우리의 RPi는 다음과 같이 작동합니다:

- 부팅
- DHCP에서 IP 획득
- SSH 키를 사용해 접속할 때까지 대기
- LUKS 잠금 해제 또는 LUKS Nuke 패스프레이즈 제공 가능

그리고 우리가 하고자 하는 일을 마친 후, 나중에 편리하게 기기를 회수하면 됩니다!

- - -

## RPi에 칼리 리눅스 설치하기

{{% notice info %}}
이 과정을 따라한다면, 이미지를 어디에 쓰는지 반드시 알고 있어야 하며, `/dev/sdX`를 교체해야 합니다. 맹목적으로 복사/붙여넣기하지 마세요!
{{% /notice %}}

우리는 기존 칼리 설치에서 드롭 박스 머신을 만들 것입니다. 다른 Debian 기반 배포판에서도 매우 쉽게 할 수 있으며, 다른 운영체제에서도 상대적으로 간단합니다(Windows 사용자 제외!).

먼저 [최신 안정 버전](/releases/) 칼리 RPi 이미지를 [다운로드](/get-kali/#kali-arm)합니다. 이 글 작성 시점에는 [칼리 2025.3](/blog/kali-linux-2025-2-release/)입니다.
우리는 4GB 이상의 RAM을 가지고 있고 [HATs](https://www.raspberrypi.com/news/introducing-raspberry-pi-hats/)(Hardware Attached on Top)를 사용하지 않기 때문에 64비트 이미지를 선택했습니다. 32비트의 경우 파일 이름을 조정한 후 동일한 단계를 따릅니다:

```console
$ wget https://kali.download/arm-images/kali-2025.3/kali-linux-2025.3-raspberry-pi-arm64.img.xz
$ xzcat kali-linux-2025.3-raspberry-pi-arm64.img.xz | sudo dd of=/dev/sdX bs=512k status=progress
```

- - -

## 시스템 준비하기

### chroot 준비

이제 chroot를 위한 준비를 할 것입니다. 마이크로SD 카드를 마운트할 디렉토리를 만들고 마운트합니다:

```console
$ sudo mkdir -vp /mnt/chroot/
$ sudo mount /dev/sdX2 /mnt/chroot/
$ sudo mount /dev/sdX1 /mnt/chroot/boot/
$ sudo mount -t proc none /mnt/chroot/proc
$ sudo mount -t sysfs none /mnt/chroot/sys
$ sudo mount -o bind /dev /mnt/chroot/dev
$ sudo mount -o bind /dev/pts /mnt/chroot/dev/pts
$ sudo apt install -y qemu-user-static
$ sudo cp /usr/bin/qemu-aarch64-static /mnt/chroot/usr/bin/
```  

마지막 두 명령은 나중에 initramfs를 위해 유용합니다.

- - -

### 필요한 패키지 설치

이제 시스템이 설정되었으므로 chroot를 사용하여 RPi 이미지를 암호화를 위해 설정할 수 있습니다. 먼저 chroot로 들어가서 필요한 패키지를 설치합니다:

```console
$ sudo env LANG=C chroot /mnt/chroot/
┌──(root㉿kali)-[/]
└─# apt update

┌──(root㉿kali)-[/]
└─# apt install -y busybox cryptsetup dropbear-initramfs lvm2
```

- - -

시작하기 전에 최신 커널을 사용하고 있는지 확인하고 싶으므로 다음 패키지가 설치되어 있는지 확인합니다:

```console
┌──(root㉿kali)-[/]
└─# apt install -y kalipi-kernel kalipi-bootloader kalipi-re4son-firmware
```

- - -

### 부팅 옵션

다음으로 `/boot/cmdline.txt` 파일을 편집하여 루트 경로를 변경할 것입니다. 루트 경로를 `/dev/mapper/crypt`로 변경한 다음, 바로 뒤에 `cryptdevice=PARTUUID=$partuuid:crypt`를 추가합니다. 이는 커널이 루트 파일시스템의 위치를 알아야 마운트하고 사용할 수 있기 때문입니다. 나중에 루트 파일시스템을 암호화하므로, 부팅 시 암호화로 인해 암호화되지 않은 장치를 볼 수 없습니다! 여기서 이름을 "crypt"로 변경하지만, 실제로는 원하는 이름으로 지정할 수 있습니다. 라즈베리 파이 장치에서 `/boot/cmdline.txt` 파일은 커널 명령줄 옵션을 전달하는 데 사용됩니다.
최종 결과는 다음과 같아야 합니다:

```console
┌──(root㉿kali)-[/]
└─# vim /boot/cmdline.txt

┌──(root㉿kali)-[/]
└─# cat /boot/cmdline.txt
dwc_otg.fiq_fix_enable=2 console=serial0,115200 kgdboc=serial0,115200 console=tty1 root=/dev/mapper/crypt cryptdevice=PARTUUID=ed889dad-02:crypt rootfstype=ext4 fsck.repair=yes rootwait net.ifnames=0
```

- - -

### 파티션 레이아웃

이제 `/etc/fstab` 파일을 업데이트해야 합니다. 이 구성 파일은 시스템에서 사용할 수 있는 모든 디스크, 디스크 파티션 및 이를 처리할 때 사용할 옵션을 포함하고 있습니다.

현재는 루트 파일시스템의 UUID로 채워져 있으며, 우리가 만들 암호화된 파일시스템을 가리키도록 변경해야 합니다. 이 예제에서는 이전 루트 장치의 UUID를 주석 처리하고 우리가 암호화된 파일시스템을 마운트할 때 사용할 `/dev/mapper/crypt`를 가리키도록 했습니다:

```console
┌──(root㉿kali)-[/]
└─# vim /etc/fstab

┌──(root㉿kali)-[/]
└─# cat /etc/fstab
# <file system> <mount point>   <type>  <options>       <dump>  <pass>
proc            /proc           proc    defaults          0       0

/dev/mapper/crypt /               ext4 errors=remount-ro 0       0
#UUID=747bfa7c-edd2-471f-8fff-0ecafc2d3791 /               ext4 errors=remount-ro 0       1
LABEL=BOOT      /boot           vfat    defaults          0       2
```

- - -

### 암호화된 파티션 구성

암호화된 파티션을 사용할 때 `/etc/crypttab` 파일을 편집하거나, 존재하지 않는 경우 생성해야 합니다. 이 파일은 cryptsetup이 암호화된 장치를 잠금 해제하는 데 필요한 옵션을 알기 위해 사용됩니다.

이 파일이 존재하지 않기 때문에, `/etc/crypttab` 파일을 생성하고 필요한 옵션을 입력합니다:

```console
┌──(root㉿kali)-[/]
└─# echo -e 'crypt\tPARTUUID=ed889dad-02\tnone\tluks' > /etc/crypttab
```

- - -

이제 파일 시스템 트릭을 수행합니다. cryptsetup이 암호화된 파티션을 보기 때문에 initramfs에 포함되도록 가짜 LUKS 파일 시스템을 생성합니다. LUKS 파티션을 포맷할 때 비밀번호를 묻는 메시지가 표시되는데, 일반적으로는 강력한 비밀번호를 사용하지만 이 경우에는 단지 cryptsetup을 initramfs에 포함시키기 위한 트릭이므로 이 단계 이후에는 필요하지 않습니다. 이는 `cryptsetup luksFormat` 단계에서 발생하며, `cryptsetup luksOpen` 단계를 실행할 때 `cryptsetup luksFormat` 중에 설정한 비밀번호를 입력하라는 메시지가 표시됩니다.

{{% notice info %}}
비밀번호를 입력할 때 입력 내용이 표시되지 않습니다
{{% /notice %}}

```console
┌──(root㉿kali)-[/]
└─# dd if=/dev/zero of=/tmp/fakeroot.img bs=1M count=20

┌──(root㉿kali)-[/]
└─# exit
$ sudo cryptsetup luksFormat /mnt/chroot/tmp/fakeroot.img
$ sudo cryptsetup luksOpen /mnt/chroot/tmp/fakeroot.img crypt
$ sudo mkfs.ext4 /dev/mapper/crypt
```

- - -

### SSH 키 구성

이제 SSH 키를 복사하거나 새로 생성하여 Dropbear의 `authorized_keys` 파일에 추가해야 합니다.

이미 존재하는 키를 복사하려면:

```console
$ sudo cp ~/.ssh/id_rsa.pub /mnt/chroot/
```

새 키를 생성하려면:

```console
$ ssh-keygen -t rsa -b 4096
[...]
Enter file in which to save the key (/home/kali/.ssh/id_rsa): /home/kali/.ssh/id_rsa_dropbear
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/kali/.ssh/id_rsa_dropbear
Your public key has been saved in /home/kali/.ssh/id_rsa_dropbear.pub
[...]
$ sudo cp ~/.ssh/id_rsa_dropbear.pub /mnt/chroot/
```

{{% notice info %}}
암호문을 입력할 때 입력 내용이 표시되지 않습니다
{{% /notice %}}

- - -

### 암호화 구성

chroot로 돌아가서 몇 가지 새 파일을 만들어야 합니다.

첫 번째는 `initramfs`에 `cryptsetup`에 필요한 파일을 추가하는 `zz-cryptsetup` 훅입니다. 이를 실행 가능하게 설정해야 `mkinitramfs`가 훅을 실행합니다:

```console
$ sudo env LANG=C chroot /mnt/chroot/
┌──(root㉿kali)-[/]
└─# vim /etc/initramfs-tools/hooks/zz-cryptsetup

┌──(root㉿kali)-[/]
└─# cat /etc/initramfs-tools/hooks/zz-cryptsetup
#!/bin/sh
set -e

PREREQ=""
prereqs()
{
	echo "${PREREQ}"
}

case "${1}" in
	prereqs)
		prereqs
		exit 0
		;;
esac

. /usr/share/initramfs-tools/hook-functions

mkdir -p ${DESTDIR}/cryptroot || true
cat /etc/crypttab >> ${DESTDIR}/cryptroot/crypttab
cat /etc/fstab >> ${DESTDIR}/cryptroot/fstab
cat /etc/crypttab >> ${DESTDIR}/etc/crypttab
cat /etc/fstab >> ${DESTDIR}/etc/fstab
copy_file config /etc/initramfs-tools/unlock.sh /etc/unlock.sh

┌──(root㉿kali)-[/]
└─# chmod +x /etc/initramfs-tools/hooks/zz-cryptsetup
```

_나중에 어떤 이유로든 비활성화하려면 실행 권한을 제거하면 됩니다._

- - -

`initramfs-tools`의 modules 파일을 편집하여 `dm-crypt` 모듈을 포함시키고, 파일이 올바른지 확인합니다:

```console
┌──(root㉿kali)-[/]
└─# grep -q dm_crypt /etc/initramfs-tools/modules || echo dm_crypt >> /etc/initramfs-tools/modules

┌──(root㉿kali)-[/]
└─# cat /etc/initramfs-tools/modules
# List of modules that you want to include in your initramfs.
# They will be loaded at boot time in the order below.
#
# Syntax:  module_name [args ...]
#
# You must run update-initramfs(8) to effect this change.
#
# Examples:
#
# raid1
# sd_mod
dm_crypt
```

- - -

### 원격 SSH 잠금 해제 구성

다음 내용으로 `unlock.sh` 스크립트를 생성하고 실행 가능하게 표시하여 `initramfs`에서 실행되도록 합니다:

```console
┌──(root㉿kali)-[/]
└─# vim /etc/initramfs-tools/unlock.sh

┌──(root㉿kali)-[/]
└─# cat /etc/initramfs-tools/unlock.sh
#!/bin/sh

export PATH='/sbin:/bin:/usr/sbin:/usr/bin'

while true; do
	test -e /dev/mapper/crypt && break || cryptsetup luksOpen /dev/disk/by-uuid/$REPLACE_LATER crypt
done

/scripts/local-top/cryptroot
for i in $(ps aux | grep 'cryptroot' | grep -v 'grep' | awk '{print $1}'); do kill -9 $i; done
for i in $(ps aux | grep 'askpass' | grep -v 'grep' | awk '{print $1}'); do kill -9 $i; done
for i in $(ps aux | grep 'ask-for-password' | grep -v 'grep' | awk '{print $1}'); do kill -9 $i; done
for i in $(ps aux | grep '\\-sh' | grep -v 'grep' | awk '{print $1}'); do kill -9 $i; done
exit 0

┌──(root㉿kali)-[/]
└─# chmod +x /etc/initramfs-tools/unlock.sh
```

- - -

다음으로 `/etc/dropbear/initramfs/authorized_keys`의 시작 부분에 다음을 추가해야 합니다. 이는 키가 일치할 때 SSH로 접속하면 이 명령을 실행하도록 지시합니다:

```console
┌──(root㉿kali)-[/]
└─# vim /etc/dropbear/initramfs/authorized_keys

┌──(root㉿kali)-[/]
└─# cat /etc/dropbear/initramfs/authorized_keys
command="/etc/unlock.sh; exit"
```

- - -

다음으로 복사한 SSH 키를 추가하고 카드에서 제거합니다:

```console
┌──(root㉿kali)-[/]
└─# cat id_rsa.pub >> /etc/dropbear/initramfs/authorized_keys && rm -v id_rsa.pub
```

완료되면 `/etc/dropbear/initramfs/authorized_keys`는 다음과 같아야 합니다:

```console
┌──(root㉿kali)-[/]
└─# cat /etc/dropbear/initramfs/authorized_keys
command="/etc/unlock.sh; exit" ssh-rsa <key> kali@kali
```

`authorized_keys` 파일의 모든 내용은 한 줄이어야 하며, 명령의 끝 `"` 와 ssh 키 사이에 공백이 있어야 합니다(예: `[...]exit" ssh-rsa[...]`).

- - -

이제 `/usr/share/initramfs-tools/scripts/init-premount/dropbear`를 편집하여 슬립 타이머를 추가해야 합니다. 이는 Dropbear 시작 _전에_ 네트워킹이 시작되도록 합니다. `dropbear-initramfs` 패키지가 업데이트될 때마다 이 수정 사항을 다시 추가해야 한다는 점에 유의하세요:

```console
┌──(root㉿kali)-[/]
└─# vim /usr/share/initramfs-tools/scripts/init-premount/dropbear

┌──(root㉿kali)-[/]
└─# cat /usr/share/initramfs-tools/scripts/init-premount/dropbear
[ "$BOOT" != nfs ] || configure_networking
sleep 5
run_dropbear &
echo $! >/run/dropbear.pid
```

- - -

이제 cryptsetup을 활성화합니다:

```console
┌──(root㉿kali)-[/]
└─# echo CRYPTSETUP=y >> /etc/cryptsetup-initramfs/conf-hook

┌──(root㉿kali)-[/]
└─# tail /etc/cryptsetup-initramfs/conf-hook
#
# Whether to include the askpass binary to the initramfs image. askpass
# is required for interactive passphrase prompts, and ASKPASS=y (the
# default) is implied when the hook detects that same device needs to be
# unlocked interactively (i.e., not via keyfile nor keyscript) at
# initramfs stage. Setting ASKPASS=n also skips `cryptroot-unlock`
# inclusion as it requires the askpass executable.

#ASKPASS=y
CRYPTSETUP=y
```

- - -

### 커널

다음 단계는 이 과정을 따라하는 분들에게 중요합니다. 사용 중인 RPi 장치에 따라 선택해야 할 커널 이름/에디션/플레이버는 다음과 같습니다 _(주의해주세요!)_:

- `Re4son+`     - 32비트 ARMEL armv6 장치용 - RPi1, RPi0 또는 RPi0w
- `Re4son-v7+`  - 32비트 ARMHF armv7 장치용 - RPi2 v1.2, RPi3 또는 RPi02w
- `Re4son-v8+`  - 64비트 ARM64 armv8 장치용 - RPi2 v1.2, RPi3 또는 RPi02w
- `Re4son-v7l+` - 32비트 ARMHF armv7 장치용 - RPi4 또는 RPi400 장치
- `Re4son-v8l+` - 64비트 ARM64 armv8 장치용 - RPi4 또는 RPi400 장치

{{% notice info %}}
이름의 `l`은 lpae - [Large Physical Address Extension](https://wikipedia.org/wiki/ARM_architecture_family#LPAE)를 의미합니다
{{% /notice %}}

참고로, 우리는 RPi4, 64비트 이미지를 사용하므로 `Re4son-v8l+`가 필요합니다. 자신의 장치에 맞게 조정하세요.
이제 사용할 커널 이름을 알았으니 커널 버전을 찾아야 합니다. 이는 장치마다 다를 수 있으며 칼리가 업데이트됨에 따라 변경됩니다. 현재 RPi에서는 `5.15.44`입니다:

커널 버전은 변경될 수 있지만 이름은 변경되지 않습니다:

```console
┌──(root㉿kali)-[/]
└─# ls -l /lib/modules/ | awk -F" " '{print $9}'
5.15.44-Re4son+
5.15.44-Re4son-v7+
5.15.44-Re4son-v7l+
5.15.44-Re4son-v8+
5.15.44-Re4son-v8l+

┌──(root㉿kali)-[/]
└─# echo "initramfs initramfs.gz followkernel" >> /boot/config.txt
```

{{% notice info %}}
커널 버전(`5.15.44`)은 변경될 수 있지만 커널 이름(`Re4son-v8l+`)은 변경되지 않습니다.
{{% /notice %}}

- - -

이제 `initramfs`를 만들어야 합니다. 이는 커널 버전이 중요한 부분입니다:

```console
┌──(root㉿kali)-[/]
└─# mkinitramfs -o /boot/initramfs.gz 5.15.44-Re4son-v8l+
```

- - -

이제 `initramfs`가 올바르게 생성되었는지 확인합니다. 결과가 없으면 문제가 발생한 것입니다:

```console
┌──(root㉿kali)-[/]
└─# lsinitramfs /boot/initramfs.gz | grep cryptsetup
usr/lib/aarch64-linux-gnu/libcryptsetup.so.12
usr/lib/aarch64-linux-gnu/libcryptsetup.so.12.7.0
usr/lib/cryptsetup
usr/lib/cryptsetup-nuke-password
usr/lib/cryptsetup-nuke-password/crypt
usr/lib/cryptsetup/askpass
usr/lib/cryptsetup/askpass.cryptsetup
usr/lib/cryptsetup/functions
usr/sbin/cryptsetup

┌──(root㉿kali)-[/]
└─# lsinitramfs /boot/initramfs.gz | grep authorized
root-Q2iWOODUwk/.ssh/authorized_keys

┌──(root㉿kali)-[/]
└─# lsinitramfs /boot/initramfs.gz | grep unlock.sh
etc/unlock.sh
```

- - -

### 서비스 비활성화

백업하기 전에 `rpi-resizerootfs`가 비활성화되어 있는지 확인해야 합니다. 이 서비스는 일반적으로 모든 ARM 장치에서 실행하여 루트 파일시스템 파티션을 저장 장치의 전체 크기로 확장합니다. 우리는 이 단계를 수동으로 수행하므로, 루트 파일시스템을 삭제하고 다시 만드는 것을 방지하기 위해 비활성화해야 합니다:

```console
┌──(root㉿kali)-[/]
└─# systemctl disable rpi-resizerootfs
```

## 기존 데이터 백업

이제 모든 변경 사항이 저장되었는지 확인한 다음 디스크를 암호화할 수 있습니다:

```console
┌──(root㉿kali)-[/]
└─# sync

┌──(root㉿kali)-[/]
└─# exit
$ sudo umount /mnt/chroot/{boot,sys,proc,dev/pts,dev}
$ sudo mkdir -vp /mnt/{backup,encrypted}
$ sudo rsync -avh /mnt/chroot/* /mnt/backup/
$ sudo cryptsetup luksClose crypt
$ sudo umount /mnt/chroot
$ echo -e "d\n2\nw" | sudo fdisk /dev/sdX
$ echo -e "n\np\n2\n\n\nw" | sudo fdisk /dev/sdX
```  

## 암호화된 파티션 구성

사용 중인 장치에 따라 두 가지 명령 중 하나를 사용해야 합니다. 4GB 이상의 RPi4를 사용하는 경우 이 명령을 사용합니다:

```console
$ sudo cryptsetup -v -y --cipher aes-cbc-essiv:sha256 --key-size 256 luksFormat /dev/sdX2
```

또는 더 오래된 버전의 LUKS를 사용하는 다음 명령을 사용해야 합니다:

```console
$ sudo cryptsetup -v -y --pbkdf pbkdf2 --cipher aes-cbc-essiv:sha256 --key-size 256 luksFormat /dev/sdX2
```  

## 데이터 복원

이후에 이제 암호화된 파티션에 데이터를 복원할 수 있습니다:

```console
$ sudo cryptsetup -v luksOpen /dev/sdX2 crypt
$ sudo mkfs.ext4 /dev/mapper/crypt
$ sudo mount /dev/mapper/crypt /mnt/encrypted/
$ sudo rsync -avh /mnt/backup/* /mnt/encrypted/
$ sync
```  

- - -

마지막 단계로, 새 LUKS UUID에 맞게 `/etc/fstab` 파일을 수정하거나, `/dev/mapper/crypt`로 두고 unlock 스크립트의 UUID를 교체하고 initramfs 파일을 다시 만들어야 합니다. 이 단계는 중요합니다. 암호화된 파일시스템을 사용하는 정보가 없으면 제대로 부팅되지 않습니다! **여러분의** 시스템 정보를 입력하세요. UUID는 모든 시스템마다 다릅니다:

```console
$ sudo mount /dev/sdX1 /mnt/encrypted/boot/
$ sudo mount -t proc none /mnt/encrypted/proc
$ sudo mount -t sysfs none /mnt/encrypted/sys
$ sudo mount -o bind /dev /mnt/encrypted/dev
$ sudo mount -o bind /dev/pts /mnt/encrypted/dev/pts
$ sudo env LANG=C chroot /mnt/encrypted
┌──(root㉿kali)-[/]
└─# blkid /dev/sdX2
/dev/sdX2: UUID="173e2de4-0501-4d8e-9039-a4923bfa5ee7" TYPE="crypto_LUKS" PARTUUID="e1750e08-02"

┌──(root㉿kali)-[/]
└─# cat /etc/fstab
# <file system> <mount point>   <type>  <options>       <dump>  <pass>
proc            /proc           proc    defaults          0       0

UUID=173e2de4-0501-4d8e-9039-a4923bfa5ee7 /               ext4 errors=remount-ro 0       1
LABEL=BOOT      /boot           vfat    defaults          0       2

┌──(root㉿kali)-[/]
└─# vim /etc/initramfs-tools/unlock.sh

┌──(root㉿kali)-[/]
└─# cat /etc/initramfs-tools/unlock.sh
#!/bin/sh

export PATH='/sbin:/bin:/usr/sbin:/usr/bin'

while true; do
	test -e /dev/mapper/crypt && break || cryptsetup luksOpen /dev/disk/by-uuid/173e2de4-0501-4d8e-9039-a4923bfa5ee7 crypt
done

/scripts/local-top/cryptroot
for i in $(ps aux | grep 'cryptroot' | grep -v 'grep' | awk '{print $1}'); do kill -9 $i; done
for i in $(ps aux | grep 'askpass' | grep -v 'grep' | awk '{print $1}'); do kill -9 $i; done
for i in $(ps aux | grep 'ask-for-password' | grep -v 'grep' | awk '{print $1}'); do kill -9 $i; done
for i in $(ps aux | grep '\\-sh' | grep -v 'grep' | awk '{print $1}'); do kill -9 $i; done
exit 0

┌──(root㉿kali)-[/]
└─# vim /etc/crypttab

┌──(root㉿kali)-[/]
└─# cat /etc/crypttab
crypt	PARTUUID=e1750e08-02	none	luks

┌──(root㉿kali)-[/]
└─# mkinitramfs -o /boot/initramfs.gz 5.15.44-Re4son-v8+
```

{{% notice info %}}
여기서 `cryptsetup: ERROR: Couldn't resolve device PARTUUID=ed889dad-02`와 같은 cryptsetup 오류가 발생하면, `/etc/crypttab` 파일을 편집하고 올바른 PARTUUID를 입력하지 않았음을 의미합니다. fsck.luks가 존재하지 않는다는 경고는 무시해도 됩니다. 그런 것은 없기 때문입니다.
{{% /notice %}}

- - -

이제 모든 것을 마운트 해제하고 정리할 수 있습니다:

```console
┌──(root㉿kali)-[/]
└─# exit
$ sudo umount /mnt/encrypted/{boot,sys,proc,dev/pts,dev}
$ sudo umount /mnt/encrypted
$ sudo cryptsetup luksClose crypt
```

- - -

# LUKS NUKE

사용자가 [LUKS NUKE](/blog/nuke-kali-linux-luks/)도 사용하고 싶다면, 다음 명령을 실행하기만 하면 됩니다:

```console
kali@kali:~$ dpkg-reconfigure cryptsetup-nuke-password
```

# 자동화?

이제 이 모든 과정을 자동화하는 방법은 어떨까요? Richard Nelson(unixabg)의 도움으로, 수동 방법보다 훨씬 적은 시간과 쉬운 방법으로 모든 것을 설정하고 싶은 사람은 누구나 가능합니다!

먼저 [unixabg의 cryptmypi](https://github.com/unixabg/cryptmypi) 스크립트 저장소를 클론합니다:

```console
kali@kali:~$ git clone https://github.com/unixabg/cryptmypi.git
```

클론이 완료되면 cryptmypi의 작업 디렉토리로 이동합니다:

```console
kali@kali:~$ cd cryptmypi/
```

다음으로 사용 가능한 Kali 예제를 나열합니다:

```console
kali@kali:~$ ls -aFl examples/ | grep kali
```

이제 빌드하려는 예제의 cryptmypi.conf를 편집합니다. 이 설정은 개인적인 것이지만 예제를 보여드리겠습니다:

```console
kali@kali:~$ cat kali-encrypted-basic/cryptmypi.conf
###############################################################################
## cryptmypi profile ##########################################################


# ENCRYPTED KALI 예제 설정
#   암호화된 칼리 시스템을 생성합니다:
#   - 부팅 시 암호 해제 비밀번호를 입력받음
#   - ssh 서버 제공(부팅 후 사용 가능)
#       id_rsa.pub 공개키가 authorized_keys에 추가됨
#
#   stage2에서 일부 optional hook이 정의됨:
#   - "optional-sys-rootpassword"는 root 비밀번호를 설정함


# 일반 설정 ------------------------------------------------------------
# RPi 버전에 맞는 커널을 선택해야 합니다.
#   Kali RPi 이미지는 다음과 같이 커널 이름을 가집니다:
#   - Re4son+ : armv6 장치용(RPi1, RPi0, RPi0w)
#   - v7+, v8+ 접미사는 32/64비트 armv7 장치용(RPi 3 등)
#   - l+ 접미사는 RPi4용
export _KERNEL_VERSION_FILTER="v8+"

# HOSTNAME
#   각 호스트네임 요소는 1~63자, 전체는 253자 이하, 영문 소문자/숫자/하이픈만 허용
export _HOSTNAME="kali-encrypted-basic"

# BLOCK DEVICE
#   SD 카드 또는 USB SD 카드 리더의 블록 장치
#   - USB 드라이브는 /dev/sdX, /dev/sdc 등으로 표시됨
#   - MMC/SD카드는 USB 연결 시 동일하게 표시될 수 있음
#   - 내장 리더는 /dev/mmcblk0, /dev/mmcblk1 등으로 표시됨
#   lsblk 명령으로 블록 장치 목록을 쉽게 확인할 수 있음
export _BLKDEV="/dev/sdX"

# LUKS 암호화 -------------------------------------------------------------
## 암호화 알고리즘
export _LUKSCIPHER="aes-cbc-essiv:sha256"

## 암호화 비밀번호
export _LUKSPASSWD="luks_password"

## 추가 옵션
# rpi0-1-2-3에서는 unlock에 필요한 메모리를 줄이고 싶을 수 있음
#  _LUKSEXTRA="--pbkdf-memory 131072"
export _LUKSEXTRA=""


# 리눅스 이미지 파일 ------------------------------------------------------------
export _IMAGEURL=https://kali.download/arm-images/kali-linux-2025.3/kali-linux-2025.3-raspberry-pi-arm64.img.xz
export _IMAGESHA="9ef1a0c011c274a81baaa626206ec985e1caa9494dab2b88ecec0a2473d6cf1f"

# 패키지 작업 -------------------------------------------------------------
export _PKGSPURGE=""
export _PKGSINSTALL="tree htop"


# 최소 SSH 설정 ----------------------------------------------------------
#   원격 ssh 접속에 사용할 키파일
#   공개키가 시스템 root .ssh/authorized_keys에 추가됨
export _SSH_LOCAL_KEYFILE="$_USER_HOME/.ssh/id_rsa"


###############################################################################
## Stage 1 Settings ###########################################################

# Custom Stage1 Profile
#   functions/stage1profiles.fns 참고. 여기서 hook을 지정하거나 미리 정의된 stage1profile 함수를 호출할 수 있음.
# Optional: stage1_hooks 함수가 정의되지 않으면 프롬프트가 표시됨
stage1_hooks(){
    stage1profile_encryption
}


###############################################################################
## Stage-2 Settings ###########################################################


# Optional stage 2 hooks
#   선언하면 stage2 빌드 중 stage2-runoptional hook에서 호출됨
#
#   Optional function: 생략 가능
stage2_optional_hooks(){
    myhooks "optional-sys-rootpassword"
}


###############################################################################
##Optional Hook Settings #####################################################


# ROOT PASSWORD CHANGER settings ----------------------------------------------
# Hooks
#   optional-sys-rootpassword
#       시스템 root 비밀번호 변경

## 새 root 비밀번호
export _ROOTPASSWD="root_password"
```

빌드하려는 예제를 변경한 후, 빌드를 시작하고 지시를 따르는 것만 남았습니다:

```console
kali@kali:~$ sudo ./cryptmypi.sh examples/kali-encrypted-basic
```

마지막에는 선택한 예제의 기능이 활성화된 완전히 암호화된 파일시스템이 있어야 합니다. 자동화된 빌드에 문제가 있다면, 프로젝트의 [이슈](https://github.com/unixabg/cryptmypi/issues) 페이지를 확인하세요. 새로운 이슈라고 생각되면 새 이슈를 제출해 주세요.
