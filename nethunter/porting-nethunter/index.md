---
title: 새 기기에 넷헌터 수동 포팅하기
description:
icon:
weight: 15
author: ["g0tmi1k", "yesimxev",]
번역: ["xenix4845"]
---

넷헌터를 새로운 기기에 포팅하려면 넷헌터가 어떻게 분리되어 있는지 이해하는 것이 중요해요. 넷헌터는 rootfs(chroot라고도 하지만 여기서는 rootfs라고 부를게요)와 커널로 나뉘어요. 대부분의 경우 rootfs는 칼리 리눅스만 포함하고 있기 때문에 안드로이드 기기에 중요하지 않아요. 커널은 블루투스, 무선 USB, HID 키보드(등) 작동에 필수적이에요.

또한 커널을 플래시하기 위해 잠금 해제된 부트로더가 있는 기기가 필요하고, 기기에서 루트를 얻을 수 있어야 해요. 루트는 busybox와 bootkali 같은 애플리케이션을 시스템에 쓰고, 칼리가 실행되도록 하는 명령어를 실행하기 위해 필요해요.

**기기를 포팅하려고 한다면 모든 것은 커널에 달려 있어요. 기기는 잠금 해제/루팅이 가능해야 해요**.

## 시작하기

[메인 문서 페이지](/docs/nethunter/building-nethunter/)의 지침을 이미 따랐다고 가정할게요. 모든 의존성을 충족했고 준비가 되었어요. 가장 먼저 하고 싶은 일은 테스트 커널을 빌드하는 것이에요.

## 커널 버전

기기가 오래된 경우 커널 버전이 3.4+ 이상인지 확인해주세요. 칼리 롤링으로 전환하면서 커널이 칼리 로드를 지원할 수 없는 chroot 내부에서 오류가 보이기 시작하고 있어요.

## 커널 소스 찾기

Nexus가 선택된 이유 중 하나는 모든 커널 소스가 [구글의 자체 웹사이트](https://android.googlesource.com/)를 통해 제공되기 때문이에요. 소스를 찾는 것은 제조업체에 따라 쉬울 수도 어려울 수도 있어요. 좋은 자료는 보통 [XDA 포럼](https://forum.xda-developers.com/)이에요. 다른 누군가가 이미 작동하는 커널을 빌드했을 가능성이 높고 GPL 하에서 소스를 제공해야 하거든요. XDA의 대부분의 커널 개발 페이지는 소스에 대한 링크를 제공해야 해요.

## 테스트 커널 만들기

툴체인을 아직 다운로드하지 않았다고 가정하고, 다음 명령어를 실행하여 다운로드할 수 있어요:

64비트가 아닌 오래된 기기의 경우:

```console
kali@kali:~$ git clone https://android.googlesource.com/platform/prebuilts/gcc/linux-x86/arm/arm-eabi-4.7 toolchain
kali@kali:~$ export ARCH=arm
kali@kali:~$ export SUBARCH=arm
kali@kali:~$ export CROSS_COMPILE=`pwd`/toolchain/bin/arm-eabi-
kali@kali:~$ make your_device_codename
kali@kali:~$ make -j$(nproc)
```

64비트 기기의 경우 적절한 툴체인을 사용하세요:

```console
kali@kali:~$ git clone https://android.googlesource.com/platform/prebuilts/gcc/linux-x86/aarch64/aarch64-linux-android-4.9 -b android10-release toolchain64
kali@kali:~$ export ARCH=arm64
kali@kali:~$ export SUBARCH=arm64
kali@kali:~$ export CROSS_COMPILE=`pwd`/toolchain64/bin/aarch64-linux-android-
kali@kali:~$ make your_device_codename                         
kali@kali:~$ make -j$(nproc)
```

준비가 되면 테스트 커널 설치 프로그램을 빌드할 수 있어요. 지침에 따라 복제된 커널 저장소에 커널을 추가한 다음 zip을 빌드하세요:
커널 파일이 어떻게 추가되는지 볼 수 있어요. 일부는 모듈과 추가 패치가 있고, 일부는 없어요. 주로 [이 커밋](https://gitlab.com/kalilinux/nethunter/build-scripts/kali-nethunter-kernels/-/commit/3665052d125e09e8652144a03056d9c396c3fc9e)처럼 모듈이 없다면 부트 이미지만 있으면 괜찮아요.

```console
kali@kali:~$ cd kali-nethunter-installer/
kali@kali:~$ ./build.py -i -k your_device_codename --your_android_version
```
