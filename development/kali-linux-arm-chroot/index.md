---
title: 칼리 리눅스 ARM chroot 준비하기
description:
icon:
weight: 70
author: ["steev",]
번역: ["xenix4845"]
---

[다운로드 영역](/get-kali/)에서 미리 빌드된 Kali ARM 이미지를 다운로드할 수 있지만, ARM을 위한 자신만의 커스텀 부트스트랩된 Kali rootfs를 빌드해야 하는 경우가 있을 수 있어요.

다음 절차는 꽤 일반적인 Kali **armhf** rootfs를 빌드하는 예시를 보여줘요. **armel**용으로 빌드하려면 **architecture** 환경 변수를 내보낼 때 "armhf" 대신 해당 값을 사용하세요.

{{% notice info %}}
이 절차를 수행하려면 root 권한이 필요하거나 "sudo su" 명령으로 권한을 상승시킬 수 있어야 해요.
{{% /notice %}}

### 이 절차에 대한 몇 가지 참고사항

이 글의 목적은 실제 수동 절차보다는 빌드 스크립트가 어떻게 작동하는지에 대한 상위 수준의 개요를 제공하는 것이에요(물론 명령줄에서 이 예제를 따라할 수도 있어요). 이 스타일은 미리 빌드된 이미지를 만드는 데 사용되는 빌드 스크립트에서 사용된 것과 유사해요. 특히, 여러 곳에서 다음과 같은 구조를 볼 수 있을 거예요:

```console
kali@kali:~$ cat <<EOF > kali-$architecture/etc/apt/sources.list
deb http://http.kali.org/kali kali-rolling main contrib non-free non-free-firmware
EOF
```

이것은 단순히 `~/arm-stuff/rootfs/kali-armhf/etc/apt/sources.list`라는 새 파일을 만들고 다음 내용을 넣는 것이에요:

```plaintext
deb http://http.kali.org/kali kali-rolling main contrib non-free non-free-firmware
```

### ARM 장치를 위한 실제 커스텀 칼리 리눅스 빌드

예제를 살펴보기 전에, 실제로 커스텀 ARM 빌드가 어떻게 수행되는지 보는 것이 좋을 거예요. 일반적으로 로컬에서 라즈베리 파이용 이미지를 빌드하려면 다음과 같은 과정을 거쳐요. 초기 일회성 설정으로 [깃허브에 있는 ARM 빌드 스크립트 저장소](https://gitlab.com/kalilinux/build-scripts/kali-arm)를 클론하고 빌드 전제 조건을 설치하세요:

```console
kali@kali:~$ cd ~/
kali@kali:~$ git clone https://gitlab.com/kalilinux/build-scripts/kali-arm.git
kali@kali:~$ dpkg --add-architecture i386
kali@kali:~$ sudo apt update
kali@kali:~$ sudo apt install -y debootstrap qemu-user-static device-tree-compiler lzma lzop u-boot-tools libncurses5:i386 pixz
```

ARM 빌드를 수행하려면 현재 셸 세션에서 교차 컴파일을 활성화해야 해요:

```console
kali@kali:~$ export ARCH=arm
kali@kali:~$ mkdir -p ~/arm-stuff/kernel/toolchains/
kali@kali:~$ cd ~/arm-stuff/kernel/toolchains/
kali@kali:~$ git clone git://gitlab.com/kalilinux/packages/gcc-arm-eabi-linaro-4-6-2.git
kali@kali:~$ export CROSS_COMPILE=~/arm-stuff/kernel/toolchains/gcc-arm-eabi-linaro-4.6.2/bin/arm-eabi-
```

그런 다음 특정 플랫폼에 대한 빌드 스크립트를 간단히 실행하면 돼요. 예를 들어, [칼리 리눅스 2016.2](https://www.kali.org/blog/kali-linux-2016-2-release/)의 라즈베리 파이 빌드는 다음 명령을 실행하면 돼요:

```console
kali@kali:~$ cd ~/
kali@kali:~$ kali-arm-build-scripts/rpi.sh 2016.2
```

ARM 빌드 스크립트는 빌드 전제 조건의 초기 일회성 설치를 제외하면 모두 완전 독립적이에요. ARM 빌드 스크립트를 처음 실행할 때는 누락된 도구 등의 오류가 있는지 출력을 _철저히 검사_하고, 오류를 수정한 다음 클린 빌드가 될 때까지 스크립트를 다시 실행하는 것이 _매우 중요_해요. 그런 후에야 원하는 특정 "레시피"를 만들기 위해 기본 빌드 스크립트를 맞춤 설정할 수 있어요.

[이전 글](/docs/development/arm-cross-compilation-environment/)에서 설명한 것처럼 **[approx](https://packages.debian.org/testing/approx)**(권장) 또는 **[apt-cacher-ng](https://packages.debian.org/testing/apt-cacher-ng)**와 같은 캐싱 프록시를 사용하여 다운로드하는 패키지를 캐싱함으로써 빌드 속도를 높일 수 있어요. 이 기능은 빌드 전에 특정 줄의 주석을 제거하지 않으면 일부 표준 빌드 스크립트가 작동하지 않을 수 있다는 점을 유의하세요. 스크립트에 해당 내용이 표시되어 있어요. **approx** 또는 **apt-cacher-ng**를 사용한다면 _스크립트를 확인_하여 필요한 변경 사항이 있는지 확인하세요.

안정적이고 예측 가능한 결과를 얻으려면 **미리 존재하는 [최신](/docs/general-use/updating-kali/) 칼리 리눅스 환경 내에서** 칼리 리눅스 ARM chroot를 빌드하세요. 이 가이드는 이미 [ARM 교차 컴파일 환경을 설정](/docs/development/arm-cross-compilation-environment/)했다고 가정해요.

## 일반 ARM용 칼리 리눅스 빌드의 주석 달린 예제

여기서 설명하는 빌드는 최소화되고 일반적이에요. 소수의 기본 패키지가 포함되며, 실제 플랫폼에 필요한 더 완전한 구성은 명확성을 위해 생략되었어요. 이 예제를 공식 ARM 빌드 스크립트에서 어떤 작업이 진행되는지 이해하고 자신만의 빌드 스크립트를 작성하기 위한 상위 수준 가이드로 사용하세요. 명령줄에서 독립 실행형 튜토리얼로 성공적으로 따라할 수 있지만, 생성된 이미지는 특정 디바이스에서 실행될 가능성이 낮아요. 구체적인 하드웨어용 작동하는 이미지를 생성하려면 [미리 빌드된 빌드 스크립트](https://gitlab.com/kalilinux/build-scripts/kali-arm)를 그대로 사용하거나 자신만의 커스터마이징을 통해 사용하세요.

### 필요한 도구 및 종속성 설치하기

이것은 일반적인 설정 작업이며, 한 번만 수행하면 돼요:

```console
kali@kali:~$ sudo apt install -y debootstrap qemu-user-static
```

### 교차 컴파일 활성화하기

Linaro 교차 컴파일을 활성화하려면 모든 세션 시작 시 다음 환경 변수를 설정해야 해요:

```console
kali@kali:~$ export ARCH=arm
kali@kali:~$ export CROSS_COMPILE=~/arm-stuff/kernel/toolchains/gcc-arm-eabi-linaro-4.6.2/bin/arm-eabi-
```

### 아키텍처 및 커스텀 패키지 정의하기

여기서는 필요한 ARM 아키텍처(armel vs armhf)와 이미지에 설치할 [패키지](https://pkg.kali.org/) 목록에 대한 일부 환경 변수를 정의해요. 이 변수들은 이 글 전체에서 사용되므로 필요에 따라 수정해주세요:

```console
kali@kali:~$ export packages="xfce4 kali-menu wpasupplicant kali-defaults initramfs-tools u-boot-tools nmap openssh-server"
kali@kali:~$ export architecture="armhf"
```

### Kali rootfs 빌드하기

#### 기본 rootfs 설정하기

시작점으로 표준 디렉토리 구조를 만들고 **debootstrap**을 사용하여 칼리 리눅스 저장소에서 기본 ARM rootfs를 설치해요. 그런 다음 2단계 chroot를 시작하기 위해 호스트 머신에서 ARM 에뮬레이터인 **qemu-arm-static**을 rootfs에 복사해요:

```console
kali@kali:~$ mkdir -p ~/arm-stuff/kernel # x-compilation 설정 시 이미 생성되었어야 함
kali@kali:~$ mkdir -p ~/arm-stuff/rootfs
kali@kali:~$ cd ~/arm-stuff/rootfs/
kali@kali:~$
kali@kali:~$ debootstrap --foreign --arch $architecture kali-rolling kali-$architecture http://http.kali.org/kali
kali@kali:~$ cp /usr/bin/qemu-arm-static kali-$architecture/usr/bin/
```

#### 2단계 chroot

먼저, 새로 생성된 기본 rootfs로 chroot한 후 **debootstrap**을 두 번째로 사용하여 2단계 rootfs를 구성하고, 저장소(in `/etc/apt/sources.list`), 호스트명(in `/etc/hostname`), 기본 네트워크 인터페이스 및 동작(in `/etc/network/interfaces` 및 `/etc/resolv.conf`) 등의 기본 이미지 설정을 구성해요. 이러한 설정을 요구 사항에 맞게 변경하세요:

```console
kali@kali:~$ cd ~/arm-stuff/rootfs/
kali@kali:~$ LANG=C chroot kali-$architecture /debootstrap/debootstrap --second-stage
kali@kali:~$
cat <<EOF > kali-$architecture/etc/apt/sources.list
deb http://http.kali.org/kali kali-rolling main contrib non-free non-free-firmware
EOF
kali@kali:~$
kali@kali:~$ echo "kali" > kali-$architecture/etc/hostname
kali@kali:~$
kali@kali:~$ cat <<EOF > kali-$architecture/etc/network/interfaces
auto lo
iface lo inet loopback
auto eth0
iface eth0 inet dhcp
EOF
kali@kali:~$
kali@kali:~$ cat <<EOF > kali-$architecture/etc/resolv.conf
nameserver 8.8.8.8
EOF
```

이제 3단계 chroot를 구성할 준비가 되었어요.

#### 3단계 chroot

여기서 특정 맞춤 설정이 이루어져요. **$packages** 목록과 키맵이 설치되고, "kali"라는 기본 kali 사용자 비밀번호가 설정되며, 기타 구성 변경 및 수정이 적용돼요:

```console
kali@kali:~$ export MALLOC_CHECK_=0 # LP: #520465의 해결 방법
kali@kali:~$ export LC_ALL=C
kali@kali:~$ export DEBIAN_FRONTEND=noninteractive
kali@kali:~$
kali@kali:~$ mount -t proc proc kali-$architecture/proc
kali@kali:~$ mount -o bind /dev/ kali-$architecture/dev/
kali@kali:~$ mount -o bind /dev/pts kali-$architecture/dev/pts
kali@kali:~$
kali@kali:~$ cat <<EOF > kali-$architecture/debconf.set
console-common console-data/keymap/policy select Select keymap from full list
console-common console-data/keymap/full select en-latin1-nodeadkeys
EOF
```

이제 3단계 chroot를 수행할 스크립트를 만들어요:

```console
kali@kali:~$ cat <<EOF > kali-$architecture/third-stage
#!/bin/sh
dpkg-divert --add --local --divert /usr/sbin/invoke-rc.d.chroot --rename /usr/sbin/invoke-rc.d
cp /bin/true /usr/sbin/invoke-rc.d

apt-get update
apt-get install -y locales-all
#locale-gen en_US.UTF-8

debconf-set-selections /debconf.set
rm -f /debconf.set
apt-get update
apt-get install -y locales-all
apt-get install -y git-core binutils ca-certificates initramfs-tools u-boot-tools
apt-get install -y locales console-common less vim git
echo "kali:kali" | chpasswd
sed -i -e 's/KERNEL\!=\"eth\*|/KERNEL\!=\"/' /lib/udev/rules.d/75-persistent-net-generator.rules
rm -f /etc/udev/rules.d/70-persistent-net.rules
apt-get install -y --force-yes ${packages}

rm -f /usr/sbin/invoke-rc.d
dpkg-divert --remove --rename /usr/sbin/invoke-rc.d

rm -f /third-stage
EOF
```

이제 2단계 chroot 내에서 실행해요:

```console
kali@kali:~$ chmod +x kali-$architecture/third-stage
kali@kali:~$ LANG=C chroot kali-$architecture /third-stage
```

### chroot 내 수동 구성

rootfs 환경에서 추가 수정이 필요한 경우, 다음 명령으로 수동으로 chroot하여 필요한 변경을 할 수 있어요:

```console
kali@kali:~$ LANG=C chroot kali-$architecture
```

수정을 완료한 후에는 다음 명령으로 chroot된 rootfs를 종료하세요:

```console
kali@kali:~$ exit
```

### 정리

마지막으로, 캐시된 파일이 사용한 공간을 확보하고 필요한 다른 정리 작업을 실행하는 정리 스크립트를 chroot에 만들고 실행한 후, rootfs에서 사용하던 디렉토리를 마운트 해제해요:

```console
kali@kali:~$ cat <<EOF > kali-$architecture/cleanup
#!/bin/sh
rm -rf /root/.bash_history
apt-get update
apt-get clean
rm -f cleanup
EOF
kali@kali:~$
kali@kali:~$ chmod +x kali-$architecture/cleanup
kali@kali:~$ LANG=C chroot kali-$architecture /cleanup
kali@kali:~$
kali@kali:~$ umount kali-$architecture/proc
kali@kali:~$ umount kali-$architecture/dev/pts
kali@kali:~$ umount kali-$architecture/dev/
kali@kali:~$
kali@kali:~$ cd ../
```

축하해요! 커스텀 Kali ARM rootfs가 `~/arm-stuff/rootfs/kali-$architecture` 디렉토리에 위치해 있어요. 이제 이 디렉토리를 tar로 압축하거나 이미지 파일로 변환하여 추가 작업을 할 수 있어요.

{{% notice info %}}
ARM 기기에서 칼리 리눅스를 실행하면 표준 x86 시스템에 비해 일부 성능 제한이 있을 수 있지만, 많은 취약점 테스트 도구를 휴대성 있게 사용할 수 있다는 장점이 있습니다.
{{% /notice %}}
