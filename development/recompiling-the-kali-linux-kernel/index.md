---
title: 칼리 리눅스 커널 재컴파일하기
description:
icon:
weight: 61
author: ["g0tmi1k",]
번역: ["xenix4845"]
---

칼리 리눅스의 커스터마이징 가능성은 리눅스 커널까지 확장돼요.

요구사항에 따라 기본 칼리 리눅스 커널에 포함되지 않은 드라이버, 패치 또는 커널 기능을 추가하고 싶을 수도 있어요. 다음 가이드는 칼리 리눅스 커널을 필요에 맞게 빠르게 수정하고 재컴파일하는 방법을 설명할 거예요. 글로벌 무선 인젝션 패치는 이미 기본적으로 칼리 리눅스 커널에 포함되어 있다는 점을 참고하세요.

### 빌드 의존성 설치하기

커널을 재컴파일하기 위한 모든 빌드 의존성을 설치하는 것부터 시작하세요:

```console
kali@kali:~$ sudo apt install -y build-essential libncurses5-dev fakeroot xz-utils
```

### 칼리 리눅스 커널 소스 코드 다운로드하기

이 섹션의 나머지 부분은 리눅스 커널의 4.9 버전에 중점을 두고 있지만, 예시들은 물론 원하는 특정 커널 버전에 맞게 조정할 수 있어요. linux-source-4.9 바이너리 패키지가 설치되어 있다고 가정해요. 업스트림 소스를 포함하는 바이너리 패키지를 설치한다는 점을 주의하세요. linux라는 이름의 칼리 소스 패키지를 검색하지 않아요:

```console
kali@kali:~$ sudo apt install -y linux-source-4.9
Reading package lists... Done
Building dependency tree
Reading state information... Done
The following additional packages will be installed:
  bc libreadline7
Suggested packages:
  libncurses-dev | ncurses-dev libqt4-dev
The following NEW packages will be installed:
  bc libreadline7 linux-source-4.9
0 upgraded, 3 newly installed, 0 to remove and 0 not upgraded.
Need to get 95.4 MB of archives.
After this operation, 95.8 MB of additional disk space will be used.
[...]
kali@kali:~$ ls /usr/src
linux-config-4.9  linux-patch-4.9-rt.patch.xz  linux-source-4.9.tar.xz
```

패키지에 커널 소스의 압축된 아카이브인 _/usr/src/linux-source-4.9.tar.xz_가 포함되어 있다는 점을 주목하세요. 이 파일들을 새 디렉토리에 추출해야 해요(/usr/src/ 바로 아래가 아니라, 리눅스 커널을 컴파일하는 데 특별한 권한이 필요하지 않기 때문이에요). 대신 _~/kernel/_이 더 적절해요:

```console
kali@kali:~$ mkdir -p ~/kernel/
kali@kali:~$ cd ~/kernel/
kali@kali:~/kernel$ tar -xaf /usr/src/linux-source-4.9.tar.xz
```

### 커널 구성하기

더 최신 버전의 커널을 재컴파일할 때(추가 패치와 함께 할 수도 있음), 구성은 칼리 리눅스에서 제안하는 것과 최대한 가깝게 유지될 가능성이 높아요. 이 경우 처음부터 모든 것을 재구성하는 대신, _/boot/config-version_ 파일(버전은 현재 사용 중인 커널의 것으로, **uname -r** 명령어로 찾을 수 있어요)을 커널 소스가 포함된 디렉토리의 _.config_ 파일로 복사하는 것으로 충분해요:

```console
kali@kali:~/kernel$ cp /boot/config-4.9.0-kali1-amd64 ~/kernel/linux-source-4.9/.config
```

변경사항을 만들어야 하거나 처음부터 모든 것을 재구성하기로 결정했다면, 커널을 구성하는 데 시간을 들여야 해요. 이는 **make menuconfig** 명령어를 호출하여 할 수 있어요:

```console
kali@kali:~/kernel$ make menuconfig
```

커널 빌드를 설정하기 위한 **menuconfig** 사용의 세부사항은 이 가이드의 범위를 벗어나요. Linux.org에 [커널 빌드 구성에 대한 자세한 튜토리얼](https://www.linux.org/threads/the-linux-kernel-configuring-the-kernel-part-1.8745/)이 있어요.

### 커널 빌드하기

커널 구성이 준비되면, 간단한 **make deb-pkg**로 최대 5개의 데비안 패키지를 생성할 거예요: 커널 이미지와 관련 모듈을 포함하는 _linux-image-**version**_, 외부 모듈을 빌드하는 데 필요한 헤더 파일을 포함하는 _linux-headers-**version**_, 일부 드라이버에 필요한 펌웨어 파일을 포함하는 _linux-firmware-image-**version**_ (데비안이나 칼리에서 제공하는 커널 소스에서 빌드할 때 이 패키지가 없을 수도 있어요), 커널 이미지와 해당 모듈의 디버깅 심볼을 포함하는 _linux-image-**version**-dbg_, 그리고 GNU glibc 같은 일부 사용자 공간 라이브러리와 관련된 헤더를 포함하는 _linux-libc-dev_. 리눅스 커널 이미지는 큰 빌드이므로 완료하는 데 시간이 걸릴 것으로 예상하세요:

```console
kali@kali:~/kernel$ make clean
kali@kali:~/kernel$ make deb-pkg LOCALVERSION=-custom KDEB_PKGVERSION=$(make kernelversion)-1
[...]
kali@kali:~/kernel$ ls ../*.deb
../linux-headers-4.9.0-kali1-custom_4.9.2-1_amd64.deb
../linux-image-4.9.0-kali1-custom_4.9.2-1_amd64.deb
../linux-image-4.9.0-kali1-custom-dbg_4.9.2-1_amd64.deb
../linux-libc-dev_4.9.2-1_amd64.deb
```

### 수정된 커널 설치하기

빌드가 성공적으로 완료되면 새로운 커스텀 커널을 설치하고 시스템을 재부팅할 수 있어요. 특정 커널 버전 번호는 달라질 것이라는 점을 주의하세요 - 칼리 2016.2 시스템에서 수행한 우리 예시에서는 4.9.2였어요. 빌드하고 있는 현재 커널 버전에 따라 명령어를 적절히 조정해야 해요:

```console
kali@kali:~/kernel$ sudo dpkg -i ../linux-image-4.9.0-kali1-custom_4.9.2-1_amd64.deb
kali@kali:~/kernel$ reboot
```

시스템이 재부팅되면 새 커널이 실행되고 있을 거예요. 문제가 생기고 커널이 성공적으로 부팅되지 않으면, 여전히 Grub 메뉴를 사용하여 원래 기본 칼리 커널에서 부팅하고 문제를 해결할 수 있어요.
