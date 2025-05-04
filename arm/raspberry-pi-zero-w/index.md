---
title: Raspberry Pi Zero W
description:
icon:
weight:
author: ["steev",]
번역: ["xenix4845"]
---

{{% notice info %}}
라즈베리 파이 Zero W는 PCB **하단**에 `Raspberry Pi Zero W V1.1`이라고 인쇄되어 있습니다.
{{% /notice %}}

[라즈베리 파이 Zero W](https://www.raspberrypi.org/products/raspberry-pi-zero-w-/)는 싱글 코어 1GHz 프로세서와 512MB RAM을 갖추고 있습니다. Kali Linux는 외장 microSD 카드에 설치됩니다. [라즈베리 파이 Zero](/docs/arm/raspberry-pi-zero/)와 달리, 라즈베리 파이 Zero W는 보드에 *무*선 네트워킹 기능이 **있습니다**.

기본적으로 Kali Linux 라즈베리 파이 Zero W 이미지는 다른 대부분의 플랫폼과 유사하게 [**kali-linux-default** 메타패키지](/docs/general-use/metapackages/)를 포함합니다. 추가 도구를 설치하려면 [메타패키지 페이지](/docs/general-use/metapackages/)를 참조하세요.

{{% notice info %}}
라즈베리 파이 이미지는 [Re4son](https://twitter.com/re4sonkernel)의 커널을 사용하며, 여기에는 외장 Wi-Fi 카드, TFT 디스플레이용 드라이버 및 [라즈베리 파이 3](/docs/arm/raspberry-pi-3/)과 [4](/docs/arm/raspberry-pi-4/)의 내장 무선 카드를 위한 [nexmon](https://github.com/seemoo-lab/nexmon) 펌웨어가 포함되어 있습니다. 별도로 다운로드하여 설치할 필요가 없으며, 그렇게 하면 현재 설치된 커널보다 다운그레이드될 가능성이 높습니다.
{{% /notice %}}

## 라즈베리 파이 Zero W용 Kali - 사용자 지침

[Kali Linux 이미지 다운로드 및 검증](/docs/introduction/download-official-kali-linux-images/) 또는 [해당 이미지를 사용하여 부팅 가능한 장치 생성](/docs/usb/live-usb-install-with-windows/)에 익숙하지 않은 경우, 해당 주제에 대해 더 자세히 설명된 절차를 참조하는 것이 좋습니다.

라즈베리 파이 Zero W에 Kali Linux의 사전 빌드된 표준 이미지를 설치하려면 다음 지침을 따르세요:

1. 최소 16GB 용량의 빠른 microSD 카드를 준비하세요. Class 10 카드를 강력히 권장합니다.
2. [다운로드](/get-kali/) 영역에서 `Kali 라즈베리 파이 Zero/Zero W` 이미지를 다운로드 _및 검증_ 하세요. 이미지 검증 과정은 [Kali Linux 다운로드](/docs/introduction/download-official-kali-linux-images/)에 자세히 설명되어 있습니다.
3. **[dd](https://manpages.debian.org/testing/coreutils/dd.1.en.html)** 유틸리티를 사용하여 이 파일을 microSD 카드에 이미징하세요 ([Kali USB 만들기](/docs/usb/live-usb-install-with-windows/)와 동일한 과정).

아래 예시에서는 저장 장치가 `/dev/sdX`에 위치한다고 가정합니다. 이 값을 단순히 복사하지 마시고, **올바른 드라이브 경로로 변경하세요**.

{{% notice info %}}
이 과정은 microSD 카드의 모든 데이터를 지웁니다. 잘못된 저장 장치를 선택하면 컴퓨터의 하드 디스크가 지워질 수 있습니다.
{{% /notice %}}

```console
$ xzcat kali-linux-2025.1-raspberry-pi-zero-w-armel.img.xz | sudo dd of=/dev/sdX bs=4M status=progress
```

이 과정은 PC, microSD 카드 속도 및 Kali Linux 이미지 크기에 따라 시간이 소요될 수 있습니다.

_dd_ 작업이 완료되면, microSD 카드를 꽂은 상태로 라즈베리 파이 Zero W를 부팅하세요.

[Kali에 로그인](/docs/introduction/default-credentials/)할 수 있어야 합니다.

# 라즈베리 파이 Zero W 헤드리스 설정 - 팁과 요령

microSD 카드의 첫 번째 파티션에 `wpa_supplicant.conf` 파일을 추가하여 무선 네트워크에 연결할 수 있습니다.

다른 Linux 시스템에서 `wpa_passphrase YOURNETWORK > wpa_supplicant.conf`를 실행하여 이 파일을 생성할 수 있습니다. 무선 네트워크 비밀번호를 입력하라는 메시지가 표시됩니다. 명령을 실행할 때 비밀번호를 추가할 수 있지만, 그렇게 하면 Wi-Fi 네트워크 비밀번호가 사용자의 쉘 히스토리에 남는다는 점을 명심하세요.

## 라즈베리 파이 Zero W용 Kali - 이미지 사용자 정의

Kali 라즈베리 파이 Zero W 이미지를 사용자 정의하려면, 설치되는 [패키지](/docs/general-use/metapackages/) 변경, [데스크톱 환경](/docs/general-use/switching-desktop-environments/) 변경, 이미지 파일 크기 증가 또는 감소 등의 작업을 하고 싶다면, GitLab의 [Kali-ARM 빌드-스크립트](https://gitlab.com/kalilinux/build-scripts/kali-arm) 저장소를 확인하고 _README.md_ 파일의 지침을 따르세요. 사용할 스크립트는 `raspberry-pi-zero-w.sh`입니다.
