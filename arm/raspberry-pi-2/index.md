---
title: Raspberry Pi 2
description:
icon:
weight:
author: ["steev",]
번역: ["xenix4845"]
---

{{% notice info %}}
라즈베리 파이 2의 CPU 위 PCB에 `Raspberry Pi 2 Model B V1.2`가 인쇄되어 있다면, [라즈베리 파이 2 v1.2 문서](/docs/arm/raspberry-pi-64-bit/)를 참조하는 것이 좋습니다. 하지만 `Raspberry Pi 2 Model B V1.1`로 표시되어 있다면, 계속 이 문서를 읽으세요.
{{% /notice %}}

기본적으로 Kali Linux 라즈베리 파이 2 이미지는 다른 대부분의 플랫폼과 유사하게 [**kali-linux-default** 메타패키지](/docs/general-use/metapackages/)를 포함합니다. 추가 도구를 설치하려면 [메타패키지 페이지](/docs/general-use/metapackages/)를 참조하세요.

{{% notice info %}}
라즈베리 파이 이미지는 [Re4son](https://twitter.com/re4sonkernel)의 커널을 사용하며, 여기에는 외장 Wi-Fi 카드, TFT 디스플레이용 드라이버 및 [라즈베리 파이 3](/docs/arm/raspberry-pi-3/)과 [4](/docs/arm/raspberry-pi-4/)의 내장 무선 카드를 위한 [nexmon](https://github.com/seemoo-lab/nexmon) 펌웨어가 포함되어 있습니다. 별도로 다운로드하여 설치할 필요가 없으며, 그렇게 하면 현재 설치된 커널보다 다운그레이드될 가능성이 높습니다.
{{% /notice %}}

## 라즈베리 파이 2용 Kali - 사용자 지침

[Kali Linux 이미지 다운로드 및 검증](/docs/introduction/download-official-kali-linux-images/) 또는 [해당 이미지를 사용하여 부팅 가능한 장치 생성](/docs/usb/live-usb-install-with-windows/)에 익숙하지 않은 경우, 해당 주제에 대해 더 자세히 설명된 절차를 참조하는 것이 좋습니다.

라즈베리 파이 2에 Kali Linux의 사전 빌드된 표준 이미지를 설치하려면 다음 지침을 따르세요:

1. 최소 16GB 용량의 빠른 microSD 카드를 준비하세요. Class 10 카드를 강력히 권장합니다.
2. [다운로드](/get-kali/) 영역에서 `Kali RaspberryPi 2, 3, 4 and 400 (img.xz)` 이미지를 다운로드 _및 검증_ 하세요. 이미지 검증 과정은 [Kali Linux 다운로드](/docs/introduction/download-official-kali-linux-images/)에 더 자세히 설명되어 있습니다.
3. **[dd](https://manpages.debian.org/testing/coreutils/dd.1.en.html)** 유틸리티를 사용하여 이 파일을 microSD 카드에 이미징하세요 ([Kali USB 만들기](/docs/usb/live-usb-install-with-windows/)와 동일한 과정).

아래 예시에서는 저장 장치가 `/dev/sdX`에 위치한다고 가정합니다. 이 값을 단순히 복사하지 마시고, **올바른 드라이브 경로로 변경하세요**.

{{% notice info %}}
이 과정은 microSD 카드의 모든 데이터를 지웁니다. 잘못된 저장 장치를 선택하면 컴퓨터의 하드 디스크가 지워질 수 있습니다.
{{% /notice %}}

```console
$ xzcat kali-linux-2025.1-raspberry-pi-armhf.img.xz | sudo dd of=/dev/sdX bs=4M status=progress
```

이 과정은 PC, microSD 카드 속도 및 Kali Linux 이미지 크기에 따라 시간이 소요될 수 있습니다.

_dd_ 작업이 완료되면, microSD 카드를 꽂은 상태로 라즈베리 파이 2를 부팅하세요.

[Kali에 로그인](/docs/introduction/default-credentials/)할 수 있어야 합니다.

## 라즈베리 파이 2용 Kali - 팁

5.10 커널을 사용하는 경우, mt76 칩셋 USB Wi-Fi 장치를 사용할 수 있지만, 다음 내용이 포함된 `/etc/modprobe.d` 디렉토리에 구성 파일을 생성해야 합니다:

```plaintext
# RPi2 또는 RPi3 USB 칩셋에서 작동하지 않는 스캐터-개더 기능을 사용하지 않고 mt76usb를 로드
options mt76-usb disable_usb_sg=1
```

## 라즈베리 파이 2용 Kali - 이미지 사용자 정의

Kali 라즈베리 파이 2 이미지를 사용자 정의하려면, 설치되는 [패키지](/docs/general-use/metapackages/) 변경, [데스크톱 환경](/docs/general-use/switching-desktop-environments/) 변경, 이미지 파일 크기 증가 또는 감소 등의 작업을 하고 싶다면, GitLab의 [Kali-ARM 빌드-스크립트](https://gitlab.com/kalilinux/build-scripts/kali-arm) 저장소를 확인하고 _README.md_ 파일의 지침을 따르세요. 사용할 스크립트는 `raspberry-pi.sh`입니다.
