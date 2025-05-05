---
title: ARM 크로스 컴파일 환경 만들기
description:
icon:
weight: 71
author: ["steev",]
번역: ["xenix4845"]
---

{{% notice info %}}
이 작업은 **root 권한**이 필요해요. `sudo su`(루트로 전환) 같은 방법으로 권한을 올려 두세요.
{{% /notice %}}

Kali Linux에서 **ARM 크로스 컴파일(cross‑compile) 환경**을 준비하는 가이드를 정리했어요. 이 과정은 여러 *Custom ARM Image* 문서의 출발점이 되니, 차근차근 따라와 주세요.

---

### 내 개발 PC 준비하기

커널을 빌드하고 이미지를 만들려면 **저장 공간**이 꽤 필요해요. 최소 50 GB 여유 공간과 넉넉한 **RAM·CPU 자원**을 준비해 두면 좋아요.

---

### 필요한 패키지 설치하기

먼저 의존 패키지를 설치해요:

```console
kali@kali:~$ sudo apt install -y git-core gnupg flex bison gperf libesd0-dev build-essential zip curl \
                       libncurses5-dev zlib1g-dev gcc-multilib g++-multilib
```

시스템이 **64비트 Kali Linux**라면 i386(32비트) 지원을 추가로 켜 주세요:

```console
kali@kali:~$ sudo dpkg --add-architecture i386
kali@kali:~$ sudo apt update
kali@kali:~$ sudo apt install -y ia32-libs
```

---

### 리나로(Linaro) 툴체인 내려받기

Linaro(리나로) 크로스 컴파일러를 깃 저장소에서 클론해요:

```console
kali@kali:~$ cd ~/
kali@kali:~$ mkdir -p arm-stuff/kernel/toolchains/
kali@kali:~$ cd arm-stuff/kernel/toolchains/
kali@kali:~$ git clone git://gitlab.com/kalilinux/packages/gcc-arm-eabi-linaro-4-6-2.git
```

---

### 환경 변수 설정하기

툴체인을 쓰려면 환경 변수를 이렇게 잡아 주세요:

```console
kali@kali:~$ export ARCH=arm
kali@kali:~$ export CROSS_COMPILE=~/arm-stuff/kernel/toolchains/gcc-arm-eabi-linaro-4.6.2/bin/arm-eabi-
```

이제 ARM 크로스 컴파일 환경이 완성됐어요. 다음 단계로, **자신만의 ARM 커널**을 빌드해 보세요. 방법은 [/docs/development/kali-linux-arm-chroot/](/docs/development/kali-linux-arm-chroot/) 문서에서 이어집니다.
