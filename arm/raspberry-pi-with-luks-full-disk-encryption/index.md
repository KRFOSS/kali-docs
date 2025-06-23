---
title: 라즈베리 파이 - 디스크 암호화
description:
icon:
archived: "true"
weight:
author: ["steev",]
번역: ["xenix4845"]
---

새로운 **라즈베리 파이 2(또는 3까지도!)** 같은 작고 빠른 ARM 하드웨어(이제 칼리 이미지가 제공됨)의 출현과 함께, 이러한 작은 장치들을 "**일회용 해킹 도구**"로 사용하는 사례가 점점 더 많아지고 있습니다. 이것이 새롭고 독특한 기술일 수도 있지만, **한 가지 큰 단점**이 있습니다 - 바로 장치 자체에 저장된 **데이터의 기밀성**입니다. 우리가 본 대부분의 설정들은 이러한 작은 컴퓨터의 SD 카드에 저장된 민감한 정보를 보호하는 데 거의 신경 쓰지 않습니다. 이러한 사실과 주변 친구들의 조언이 결합되어 우리는 LUKS 암호화, NUKE 기능을 갖춘 라즈베리 파이 장치용 칼리 리눅스 이미지를 만들게 되었습니다. 다음 글에서는 이 과정을 설명하므로, 여러분도 이를 따라 할 수 있습니다.

### 디스크 암호화 과정 개요

아래 설명된 과정은 **라즈베리 파이 B+와 라즈베리 파이 2/3**(이하 총칭하여 "RPi"라고 함)에서 성공적으로 시험되고 테스트되었지만, 이 지침을 칼리 리눅스를 실행하는 모든 ARM 장치에 적용하는 것은 어렵지 않을 것입니다. 시작하기 전에, 우리가 무엇을 할 것인지 잠시 설명하겠습니다 - 이 과정은 복잡하지는 않지만 **여러 단계가 포함됩니다**. 기본적으로 다음과 같은 단계를 거칩니다:

1. 필요한 칼리 RPi 이미지를 [다운로드](/get-kali/)하고 **dd** 명령어로 SD 카드에 기록합니다.
2. RPi 이미지에 chroot하여 암호화된 부팅을 준비하기 위해 여러 파일을 설치/업데이트합니다.
3. Dropbear와 새로 생성된 SSH 키가 포함된 initramfs 파일을 생성합니다.
4. 수정된 루트 파일 시스템을 임시 백업 위치에 rsync로 복사한 다음 SD 카드에서 루트 파일 시스템 파티션을 삭제합니다.
5. 그런 다음 암호화된 파티션을 새로 만들고 여기에 루트 파티션 데이터를 복원합니다. 이게 전부입니다!

모든 것이 잘 진행되면, RPi가 부팅되고 LUKS가 실행되어 루트 드라이브를 복호화하기 위한 비밀번호를 요청하는 동시에 Dropbear SSH 세션을 열어 **SSH로 접속하여 부팅 복호화 비밀번호를 제공**할 수 있습니다. 아, 그리고 이 이미지가 **LUKS NUKE 기능**도 있다는 것을 언급했나요?

### 실제 작업하기

항상 그렇듯이, 모든 ARM 개발은 칼리 amd64 머신에서 수행되며 필요한 모든 [종속성](https://gitlab.com/kalilinux/build-scripts/kali-arm/blob/main/build-deps.sh)이 설치되어 있는지 확인했습니다. [최신 칼리 RPi3 이미지](/get-kali/)(2025.2)를 다운로드하고, 압축을 풀고, **dd** 명령어를 사용하여 SD 카드에 기록합니다. 우리의 경우 SD 카드는 /dev/sdb2로 인식되었습니다 - 필요에 따라 조정하세요!

```console
$ dd if=kali-linux-2025.2-rpi3-nexmon.img of=/dev/sdb conv=fsync bs=4M
```

dd 작업이 완료되면, 각 파티션을 마운트하고 칼리 RPi3 이미지로 chroot합니다:

```console
kali@kali:~$ mkdir -p /mnt/chroot/boot
kali@kali:~$
kali@kali:~$ mount /dev/sdb2 /mnt/chroot/
kali@kali:~$ mount /dev/sdb1 /mnt/chroot/boot/
kali@kali:~$
kali@kali:~$ mount -t proc none /mnt/chroot/proc
kali@kali:~$ mount -t sysfs none /mnt/chroot/sys
kali@kali:~$ mount -o bind /dev /mnt/chroot/dev
kali@kali:~$ mount -o bind /dev/pts /mnt/chroot/dev/pts
kali@kali:~$ sudo apt install -y qemu-user-static
kali@kali:~$
kali@kali:~$ cp /usr/bin/qemu-arm-static /mnt/chroot/usr/bin/
kali@kali:~$ LANG=C chroot /mnt/chroot/
```

그런 다음 이미지를 업데이트하고 이 과정에 필요한 필수 패키지를 설치합니다:

```console
kali@kali:~$ sudo apt update
kali@kali:~$ sudo apt install -y busybox cryptsetup dropbear-initramfs cryptsetup-nuke-password
```

초기 initramfs 파일을 생성하여 dropbear SSH 키 생성을 트리거합니다. 먼저 다음과 같이 모듈 디렉토리 버전 번호를 찾습니다(이는 다양한 이미지 버전과 칼리 릴리스에 따라 달라질 수 있음):

```console
kali@kali:~$ ls -l /lib/modules/ | awk -F" " '{print $9}'
4.9.59-Re4son-Kali-Pi+
```

그런 다음 해당 버전 정보를 사용하여 초기 initramfs 파일을 생성합니다:

```console
kali@kali:~$ mkinitramfs -o /boot/initramfs.gz 4.9.59-Re4son-Kali-Pi+
```

기본 루트 비밀번호를 변경합니다:

```console
kali@kali:~$ sudo passwd root
```

다음으로 cmdline.txt와 config.txt에서 부팅 매개변수를 수정합니다:

```console
kali@kali:~$ vim /boot/cmdline.txt
```

...그리고 다음 매개변수를 추가/변경합니다:

```console
kali@kali:~$ root=/dev/mapper/crypt_sdcard cryptdevice=/dev/mmcblk0p2:crypt_sdcard rootfstype=ext4
```

다음으로 /boot/config.txt 파일을 생성하거나 수정합니다:

```console
kali@kali:~$ echo initramfs initramfs.gz >> /boot/config.txt
```

이제 Dropbear SSH 접근을 설정합니다. 노트북에서 SSH 개인 키를 복사하거나 이 용도로 특별히 하나를 만듭니다:

#### 복사:

```console
kali@kali:~$ cp /root/.ssh/id_rsa.pub /etc/dropbear-initramfs/authorized_keys
kali@kali:~$ chmod 0600 /etc/dropbear-initramfs/authorized_keys
```

생성(chroot 내가 아닌 호스트 머신에서):

```console
kali@kali:~$ ssh-keygen -N "" -f kali-luks-unlock
kali@kali:~$ cat kali-luks-unlock.pub > /mnt/chroot/etc/dropbear-initramfs/authorized_keys
kali@kali:~$ chmod 0600 /mnt/chroot/etc/dropbear-initramfs/authorized_keys
```

그리고 SSH 연결을 cryptroot 애플리케이션과의 상호 작용만 허용하도록 제한합니다:

```console
kali@kali:~$ vim /etc/dropbear-initramfs/authorized_keys
```

ssh 공개 키 시작 **전**에 다음 내용을 붙여넣습니다:

```plaintext
command="/scripts/local-top/cryptroot && kill -9 `ps | grep -m 1 'cryptroot' | cut -d ' ' -f 3`"
```

그런 다음 initramfs에 cryptsetup이 포함되도록 cryptsetup initramfs 훅을 편집합니다:

```console
kali@kali:~$ echo "CRYPTSETUP=y" >> /etc/cryptsetup-initramfs/conf-hook
```

그런 다음 fstab와 crypttab을 구성된 부팅 장치로 편집하고 chroot를 종료합니다:

```console
kali@kali:~$ cat > /etc/fstab < /etc/crypttab
```

테스트 중에 일부 경우에 USB 포트가 깨어나는 데 시간이 걸려 initrd Dropbear 네트워크 초기화가 중단되는 것을 발견했습니다. 이를 해결하기 위해 initrd 자체의 configure_networking 함수에 5초 지연을 추가합니다:

```console
kali@kali:~$ vim /usr/share/initramfs-tools/scripts/functions
```

다음과 같이 변경:

```plaintext
configure_networking()
{
[...]
```

다음으로 변경:

```plaintext
configure_networking()
{

echo "Waiting 5 seconds for USB to wake"
sleep 5
[...]
```

{{% notice info %}}
"..."를 추가하지 마세요. 이는 우리가 편집하지 않는 더 많은 내용이 있다는 것을 나타내는 자리 표시자입니다.
{{% /notice %}}

파일을 저장한 다음 initramfs를 다시 생성하고 chroot를 종료합니다. cryptsetup 및 device-mapper 경고는 무시하셔도 됩니다:

```console
kali@kali:~$ mkinitramfs -o /boot/initramfs.gz 4.9.59-Re4son-Kali-Pi+
kali@kali:~$ exit
```

이제 chroot를 해제하고 rootfs 파티션을 백업합니다:

```console
kali@kali:~$ umount /mnt/chroot/boot
kali@kali:~$ umount /mnt/chroot/sys
kali@kali:~$ umount /mnt/chroot/proc
kali@kali:~$ umount /mnt/chroot/dev/pts
kali@kali:~$ umount /mnt/chroot/dev
kali@kali:~$ mkdir -p /mnt/backup
kali@kali:~$ rsync -avh /mnt/chroot/* /mnt/backup/
```

백업이 완료되면 모든 것을 언마운트합니다:

```console
kali@kali:~$ umount /mnt/chroot
```

이제 SD 카드의 기존 2번 파티션을 삭제하고 빈 파티션을 다시 만듭니다. 이 파티션은 LUKS 암호화를 위해 설정할 것입니다:

```console
kali@kali:~$ echo -e "d\n2\nw" | fdisk /dev/sdb
kali@kali:~$ echo -e "n\np\n2\n\n\nw" | fdisk /dev/sdb
```

SD 카드를 뽑았다가 다시 꽂아 새 파티션이 인식되게 한 다음, 암호화된 파티션을 설정합니다:

```console
kali@kali:~$ cryptsetup -v -y --cipher aes-xts-plain64 --key-size 256 luksFormat /dev/sdb2
kali@kali:~$ cryptsetup -v luksOpen /dev/sdb2 crypt_sdcard
kali@kali:~$ mkfs.ext4 /dev/mapper/crypt_sdcard
```

준비가 완료되면 이제 암호화된 파티션에 rootfs 백업을 복원합니다:

```console
kali@kali:~$ mkdir -p /mnt/encrypted
kali@kali:~$ mount /dev/mapper/crypt_sdcard /mnt/encrypted/
kali@kali:~$ rsync -avh /mnt/backup/* /mnt/encrypted/
kali@kali:~$ umount /mnt/encrypted/
kali@kali:~$ rm -rf /mnt/backup
kali@kali:~$ sync
```

그런 다음 볼륨을 언마운트하고 닫습니다:

```console
kali@kali:~$ cryptsetup luksClose /dev/mapper/crypt_sdcard
```

### 완료되었습니다!

이제 수정된 SD 카드를 사용하여 RPi를 부팅하는 일만 남았습니다. initramfs는 Dropbear를 로드하고 로컬 LAN에서 DHCP 주소를 가져와(IP를 직접 지정할 수도 있음) 부팅 중인 RPi에 SSH로 접속하여 복호화 비밀번호를 입력할 수 있게 합니다. 비밀번호가 수락되면 Dropbear가 종료되고 RPi는 부팅을 계속합니다. 다음과 같은 내용이 표시됩니다:

```console
kali@kali:~$ ssh -i key 192.168.13.37
The authenticity of host '192.168.13.37 (192.168.13.37)' can't be established.
RSA key fingerprint is a6:a2:ad:7d:cb:d8:70:58:d1:ed:81:e8:4a:d5:23:3a.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '192.168.13.37' (RSA) to the list of known hosts.
Unlocking the disk /dev/mmcblk0p2 (crypt_sdcard)
Enter passphrase: cryptsetup: crypt_sdcard set up successfully
Connection to 192.168.13.37 closed.
kali@kali:~$
```

### 파이에 LUKS NUKE 기능도 추가할 수 있나요?

[칼리 리눅스 LUKS NUKE](/blog/nuke-kali-linux-luks/) 기능에 익숙하지 않다면 놓치고 계신 겁니다. 이 단계는 선택 사항이지만, LUKS 암호화 드라이브에 비상 자체 파괴 비밀번호를 구성하고 적용할 수 있게 해줍니다. 이를 위해 특수 목적 패키지인 cryptsetup-nuke-password로 Nuke 비밀번호를 정의합니다:

```console
kali@kali:~$ dpkg-reconfigure cryptsetup-nuke-password
```

Nuke 비밀번호가 정의되면 이제 LUKS 복호화 키 슬롯을 원격으로 지워 SD 카드의 데이터에 접근할 수 없게 만들 수 있습니다.

### 라즈베리 파이 디스크 암호화 영상

과정에 대한 시각적 컨텍스트를 더 제공하기 위해, 라즈베리 파이 B+에서 LUKS 디스크 암호화를 작동시키는 데 사용된 명령 시퀀스를 보여주는 짧은 영상을 만들었습니다. 즐겁게 보세요!

칼리 리눅스가 실행되는 라즈베리 파이에서 LUKS 디스크 암호화 설정하기. LUKS Nuke 기능도 지원합니다!

{{< vimeo 121449299 >}}

### 참고 자료

우리는 이 절차를 인터넷의 여러 소스, 특히 아래 두 소스에서 아이디어와 지침을 가져와 개발했습니다. 라즈베리 파이 커뮤니티에 큰 감사를 드립니다!

1. [ofthedeed.org/posts/Encrypted_Raspberry_Pi/](https://www.ofthedeed.org/posts/Encrypted_Raspberry_Pi/)
2. [raspberrypi.org/forums/viewtopic.php?f=28&t=7626](https://www.raspberrypi.org/forums/viewtopic.php?f=28&t=7626)
