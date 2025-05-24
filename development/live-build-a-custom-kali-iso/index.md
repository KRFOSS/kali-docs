---
title: 커스텀 칼리 ISO 만들기
description:
icon:
weight: 51
author: ["g0tmi1k",]
번역: ["xenix4845"]
---

<!-- @g0tmi1k: Is it Kali ISO or Kali Image? -->

## 자신만의 칼리 ISO 빌드하기 소개

커스터마이징된 칼리 리눅스 이미지를 빌드하는 것은 생각하는 것만큼 복잡하지 않아요. 쉽고 재미있으며 보람 있는 일이에요! 칼리 리눅스는 전통적으로 **라이브 이미지**였지만, [칼리 2020.1](/blog/kali-linux-2020-1-release/)부터 **인스톨러 이미지**가 도입되었어요. 이 두 이미지는 [각기 다른 기능](/docs/introduction/what-image-to-download/)을 가지고 있으며, 빌드 방식도 다르답니다.

- 라이브 이미지 - 시스템을 변경하지 않고 칼리를 시도해볼 수 있어요([USB](/docs/usb/)에 최적). [live-build](https://live-team.pages.debian.net/live-manual/html/live-manual/index.en.html)를 사용하여 생성돼요
- 인스톨러 이미지 - 설치 중 패키징을 선택하여 칼리를 커스터마이징할 수 있어요. [데스크톱 환경](/docs/general-use/switching-desktop-environments/) 선택은 물론 어떤 [메타패키지](/docs/general-use/metapackages/)를 설치할지도 정할 수 있어요. 이 이미지는 [simple-cdd](https://wiki.debian.org/Simple-CDD)로 구동돼요 _(`debian-cd`를 사용하여 `Debian-Installer`를 만들어요)_.

칼리 네트워크 저장소 외부의 패키지 추가, 무인 설치부터 기본 배경화면 변경까지 칼리 ISO 빌드의 거의 모든 측면을 구성할 수 있어요. 우리의 빌드 스크립트는 구성 세트를 사용하여 이미지 빌드의 모든 측면을 자동화하고 커스터마이징하는 프레임워크를 제공해요. 칼리 리눅스 개발팀은 공식 칼리 ISO 릴리스를 생산하기 위해 동일한 빌드 스크립트를 사용해요.

## ISO를 어디서 빌드해야 할까요?

이상적으로는 **기존 칼리 환경 내에서** 커스텀 칼리 ISO를 빌드해야 해요. 문제가 발생할 가능성이 적거든요. 하지만 칼리가 아니지만 여전히 데비안 기반 시스템에서 이미지를 생성하는 것도 가능해요.

## 칼리 환경

#### 준비하기 - 빌드 스크립트 칼리 시스템 설정

먼저 다음 명령어로 필요한 패키지를 설치하고 설정하여 칼리 ISO 빌드 환경을 준비해야 해요:

```console
kali@kali:~$ sudo apt update
kali@kali:~$ sudo apt install -y git live-build simple-cdd cdebootstrap curl
kali@kali:~$
kali@kali:~$ git clone https://gitlab.com/kalilinux/build-scripts/live-build-config.git
```

#### 업데이트된 라이브 이미지 빌드하기

이제 `live-build-config/` 디렉토리에 들어가서 우리의 `build.sh` 래퍼 스크립트를 실행하여 업데이트된 칼리 ISO _(기본 구성 사용)_를 간단히 빌드할 수 있어요:

```console
kali@kali:~$ cd live-build-config/
kali@kali:~/live-build-config$ ./build.sh --verbose
[...]
***
GENERATED KALI IMAGE: ./images/kali-linux-rolling-live-amd64.iso
***
kali@kali:~$
```

`build.sh` 스크립트는 ISO를 만드는 데 필요한 모든 패키지를 다운로드하므로 완료하는 데 시간이 걸려요. 음료수를 마시기 좋은 시간이에요.

#### 업데이트된 인스톨러 이미지 빌드하기

기본적으로 **라이브 이미지**를 생성해요. **인스톨러 이미지**를 원한다면 `--installer`를 추가하세요:

```console
kali@kali:~/live-build-config$ ./build.sh --verbose --installer
```

`--verbose`는 출력이 `build.log`에만 캡처되는 것보다 화면에 더 많이 출력되도록 하기 위해 사용되고 있어요. 더 많은 출력을 원한다면 `--debug`를 대신 사용할 수 있는데, 그러면 훨씬 더 많은 정보를 제공할 거예요.

- - -

## 칼리가 아닌 데비안 기반 환경

#### 칼리가 아닌 데비안 기반 시스템 빌드 스크립트 설정

칼리 리눅스가 아닌 다른 데비안 기반 시스템에서 칼리 ISO를 빌드할 수 있어요. 아래 지침은 데비안과 우분투 모두에서 작동하도록 테스트되었어요.

먼저 시스템이 완전히 업데이트되도록 준비한 다음 칼리 아카이브 키링과 패키지를 다운로드해요:

```console
$ sudo apt update
$ sudo apt full-upgrade -y
$
$ wget https://http.kali.org/pool/main/k/kali-archive-keyring/kali-archive-keyring_2022.1_all.deb
$ wget https://http.kali.org/pool/main/l/live-build/live-build_20230502+kali3_all.deb
```

_참고: `kali-archive-keyring_20YY.X_all.deb`와 `live-build_20YYMMDD+kaliX_all.deb`가 최신 파일인지 확인해야 해요._

완료되면 추가 의존성과 이전에 다운로드한 파일들을 설치해요:

```console
$ sudo apt install -y git live-build simple-cdd cdebootstrap curl
$
$ sudo dpkg -i kali-archive-keyring_2022.1_all.deb
$ sudo dpkg -i live-build_20230502+kali3_all.deb
```

환경이 모두 준비되면 빌드 스크립트 프로필을 설정하고 빌드 구성을 복제하여 프로세스를 시작해요:

```console
$ cd /usr/share/debootstrap/scripts/
$ (echo "default_mirror http://http.kali.org/kali"; sed -e "s/debian-archive-keyring.gpg/kali-archive-keyring.gpg/g" sid) > /tmp/kali
$ sudo mv /tmp/kali .
$ sudo ln -s kali kali-rolling
$
$ cd ~/
$ git clone https://gitlab.com/kalilinux/build-scripts/live-build-config.git
$
$ cd live-build-config/
```

이 시점에서 호스트 OS와 버전에 따라 **debootstrap**의 버전 확인을 우회하기 위해 `build.sh`를 편집해야 할 수도 있어요. 아래의 `exit 1`을 주석 처리하여 이를 수행해요:

```console
$ cat build.sh
[...]
		ver_debootstrap=$(dpkg-query -f '${Version}' -W debootstrap)
		if dpkg --compare-versions "$ver_debootstrap" lt "1.0.97"; then
			echo "ERROR: You need debootstrap (>= 1.0.97), you have $ver_debootstrap" >&2
			exit 1
		fi
[...]
$
```

위의 변경사항이 적용되면 `build.sh`는 다음과 같이 보일 거예요:

```console
$ cat build.sh
[...]
		ver_debootstrap=$(dpkg-query -f '${Version}' -W debootstrap)
		if dpkg --compare-versions "$ver_debootstrap" lt "1.0.97"; then
			echo "ERROR: You need debootstrap (>= 1.0.97), you have $ver_debootstrap" >&2
			#exit 1
		fi
[...]
$
```

이 시점에서 평소처럼 ISO를 빌드할 수 있어요:

```console
$ ./build.sh --verbose
```

- - -

## 최신 칼리 이미지 재빌드하기

[Kali 브랜치](/docs/general-use/kali-branches/) 브랜치를 사용하면 최신 배포 이미지를 재생성할 수 있어요. `--distribution kali-last-snapshot`을 사용하여 이를 수행할 수 있어요:

```console
kali@kali:~$ time ./build.sh \
  --verbose \
  --installer \
  --distribution kali-last-snapshot \
  --version 2025.1 \
  --subdir kali-2025.1
[...]
***
GENERATED KALI IMAGE: ./images/kali-2025.1/kali-linux-2025.1-installer-amd64.iso
***
kali@kali:~$
```

- - -

## 칼리 ISO 빌드 구성하기 (선택사항)

칼리 리눅스 ISO를 커스터마이징하고 싶다면 이 섹션에서 일부 세부사항을 설명할게요. `kali-config/` 디렉토리를 통해 [live-build](https://live-team.pages.debian.net/live-manual/html/live-manual/customization-overview.en.html) 페이지에 잘 문서화된 광범위한 커스터마이징 옵션이 있어요. Simple-CD는 옵션이 조금 더 제한적이에요. 성급한 분들을 위해 주요 내용들을 소개할게요.

#### 다양한 데스크톱 환경으로 칼리 라이브 빌드하기

[칼리 2.0](/blog/kali-linux-2-0-release/)부터 Xfce _(기본)_, Gnome, KDE, E17, I3WM, LXDE, MATE를 포함한 다양한 [데스크톱 환경](/docs/general-use/switching-desktop-environments/)에 대한 내장 구성을 지원해요. 이들 중 하나를 빌드하려면 다음과 유사한 구문을 사용하면 돼요:

```console
kali@kali:~/live-build-config$ # These are the different Desktop Environment build options:
kali@kali:~/live-build-config$ #./build.sh --variant {xfce,gnome,kde,mate,e17,lxde,i3} --verbose
kali@kali:~/live-build-config$
kali@kali:~/live-build-config$ # To build a Gnome ISO:
kali@kali:~/live-build-config$ ./build.sh --variant gnome --verbose
kali@kali:~/live-build-config$
kali@kali:~/live-build-config$ # To build a KDE ISO:
kali@kali:~/live-build-config$ ./build.sh --variant kde --verbose
```

인스톨러 이미지에서는 기본적으로 Xfce, Gnome, KDE가 포함되어 있으므로 이것이 필요하지 않아요. 아래 섹션에서 설명하는 대로 패키지를 포함하여 다른 것들을 추가할 수 있어요.

#### 빌드에 포함된 패키지 제어하기

빌드에 포함된 패키지 목록은 해당 `kali-config/` 디렉토리에 있을 거예요. 예를 들어, 편집하고 싶다면:

- 기본 인스톨러 ISO는 다음 패키지 목록 파일을 사용해요 - `kali-config/installer-default/packages`
- 기본 라이브 ISO는 다음 패키지 목록 파일을 사용해요 - `kali-config/variant-default/package-lists/kali.list.chroot`
- Gnome과 같은 기본이 아닌 라이브 ISO 데스크톱 환경 - `kali-config/variant-gnome/package-lists/kali.list.chroot` _(Gnome을 지원되는 데스크톱 환경으로 바꿀 수 있어요)_

기본적으로 이 목록들은 [**kali-linux-default** 메타패키지](/docs/general-use/metapackages/)와 일부 다른 것들을 포함할 거예요. 더 세밀한 제어를 위해 이들을 주석 처리하고 ISO에 포함할 패키지의 수동 목록으로 바꿀 수 있어요.

#### 빌드에서 파일 오버레이하기

라이브 이미지에서는 `includes.{chroot,binary,installer}` 디렉토리 내에서 기존 파일 시스템에 오버레이하여 빌드에 추가 파일이나 스크립트를 포함하는 옵션이 있어요.

예를 들어, ISO의 `/root/` 디렉토리에 우리만의 커스텀 스크립트를 포함하고 싶다면(**chroot** 단계에 해당), ISO를 빌드하기 전에 이 스크립트 파일을 `kali-config/common/includes.chroot/` 디렉토리에 넣으면 돼요.

더 많은 정보는 [live-build 문서](https://live-team.pages.debian.net/live-manual/html/live-manual/customizing-contents.en.html)를 참조하세요.

<!--For installer images, TODO -->

#### 빌드 훅, 바이너리 및 Chroot

라이브 이미지의 경우 live-build는 칼리 ISO 라이브 이미지의 다양한 단계에서 "훅 스크립트"를 사용할 수 있게 해주는 훅을 지원해요. 훅과 사용 방법에 대한 더 자세한 정보는 [live-build 매뉴얼](https://live-team.pages.debian.net/live-manual/html/live-manual/customizing-contents.en.html#507)을 참조하세요.

예시로 `kali-config/common/hooks/`의 기존 훅들을 확인해보는 것을 권장해요.

<!--For installer images, TODO -->

- - -

## 다양한 아키텍처용 칼리 리눅스 ISO 빌드하기 (선택사항)

기본적으로 빌드 스크립트는 현재 운영체제의 아키텍처를 기반으로 칼리 이미지를 생성해요. 이를 변경하고 싶다면:

- x64: `./build.sh --verbose --arch amd64`
- arm64: `./build.sh --verbose --arch arm64`

- - -

## 빌드용 커스텀 네트워크 미러 사용하기 (선택사항)

여러 이미지를 빌드하면 `build.sh`가 완료되기를 자주 기다리게 될 거예요. 빌드 프로세스를 가속화하는 몇 가지 방법이 있어요:

- 라이브 이미지보다 빠르게 빌드되는 경우가 많은 인스톨러 이미지 빌드
- 포함된 패키지 수 줄이기 (`kali-linux-default`를 `kali-linux-top10`으로 전환하는 등)
- 패키지 접근 개선

패키지가 다운로드되기를 기다리는 경우를 자주 발견할 거예요. 같은 머신에 로컬 프록시를 설정하거나(`approx` 또는 `apt-cacher-ng` 같은) [로컬 네트워크 미러](/docs/community/setting-up-a-kali-linux-mirror/)를 설정할 수 있어요.

다음과 같이 하여 빌드 스크립트가 다른 미러를 사용하도록 지시할 수 있어요(네트워크 미러가 `http://192.168.0.101/kali`에 있다고 가정):

```console
kali@kali:~/live-build-config$ echo "http://192.168.0.101/kali/" > .mirror
kali@kali:~/live-build-config$ ./build.sh --verbose
```

- - -

## 도움말 화면

`--help`를 사용하여 사용 가능한 모든 명령줄 옵션을 볼 수 있어요:

```console
kali@kali:~/live-build-config$ ./build.sh --help
Usage: ./build.sh [<option>...]

  --distribution <arg>
  --proposed-updates
  --arch <arg>
  --verbose
  --debug
  --salt
  --installer
  --live
  --variant <arg>
  --version <arg>
  --subdir <arg>
  --get-image-path
  --no-clean
  --clean
  --help

More information: https://www.kali.org/docs/development/live-build-a-custom-kali-iso/
kali@bDesktop:~/live-build-config$
```

- - -

## 빌드된 이미지 테스트하기

이슈를 생성한 후에는 모든 칼리 기본 이미지처럼 취급할 수 있으므로 (베어메탈이나 [가상](/docs/virtualization/)으로) [설치](/docs/installation/)하거나 CD/DVD/[USB](/docs/usb/)에 복사할 수 있어요.

"프로덕션"에 투입하기 전에 이미지를 빠르게 테스트하고 싶다면 [qemu](https://packages.debian.org/sid/qemu)(그리고 UEFI용 [ovmf](https://packages.debian.org/sid/ovmf))를 사용할 수 있어요. 먼저 패키지를 설치해요:

```console
kali@kali:$ sudo apt update
kali@kali:$ sudo apt install -y qemu qemu-system-x86 ovmf
```

다음으로 사용할 하드 디스크를 생성해요:

```console
kali@kali:$ qemu-img create \
  -f qcow2 \
  /tmp/kali-test.hdd.img \
  20G
```

그 후에 생성된 이미지에서 부팅하려면 _(x64에서 라이브 이미지를 사용할 거예요)_:

```console
kali@kali:$ qemu-system-x86_64 \
  -enable-kvm \
  -drive if=virtio,aio=threads,cache=unsafe,format=qcow2,file=/tmp/kali-test.hdd.img \
  -cdrom /home/kali/live-build-config/images/kali-linux-rolling-live-amd64.iso \
  -boot once=d
```

위는 "BIOS" 부팅이에요. "UEFI" 부팅의 경우:

```console
kali@kali:$ qemu-system-x86_64 \
  -enable-kvm \
  -drive if=virtio,aio=threads,cache=unsafe,format=qcow2,file=/tmp/kali-test.hdd.img \
  -drive if=pflash,format=raw,readonly,file=/usr/share/OVMF/OVMF_CODE.fd \
  -drive if=pflash,format=raw,readonly,file=/usr/share/OVMF/OVMF_VARS.fd \
  -cdrom /home/kali/live-build-config/images/kali-linux-rolling-live-amd64.iso \
  -boot once=d
```

_참고: UEFI 구성 파일을 읽기 전용으로 설정했어요_
