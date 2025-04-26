---
title: Raspberry Pi 3
description:
icon:
weight:
author: ["steev",]
---

[라즈베리 파이 3](https://www.raspberrypi.org/products/raspberry-pi-3-model-b/)는 쿼드 코어 1.2GHz 프로세서와 1GB RAM을 갖추고 있습니다. Kali Linux는 외장 microSD 카드에 설치됩니다.

기본적으로 Kali Linux 라즈베리 파이 3 이미지는 다른 대부분의 플랫폼과 유사하게 [**kali-linux-default** 메타패키지](/docs/general-use/metapackages/)를 포함합니다. 추가 도구를 설치하려면 [메타패키지 페이지](/docs/general-use/metapackages/)를 참조하세요.

{{% notice info %}}
라즈베리 파이 3는 64비트 프로세서를 탑재하고 있어 64비트 이미지를 실행할 수 있습니다.
64비트 이미지를 실행할 수 있기 때문에 `Kali Linux Raspberry Pi 2, 3, 4 및 400(32비트)(img.xz)` 또는 `Kali Linux Raspberry Pi 2(v1.2), 3, 4 및 400(64비트)(img.xz)`를 실행할 이미지로 선택할 수 있으며, 후자가 64비트입니다.<br />
<br />
라즈베리 파이 장치에서는 32비트 이미지를 사용하는 것이 좋습니다. 32비트 이미지가 훨씬 더 많은 테스트를 거쳤으며, 대부분의 문서는 32비트인 라즈베리파이 OS를 실행한다고 가정하기 때문입니다.
{{% /notice %}}

{{% notice info %}}
라즈베리 파이 이미지는 [Re4son](https://twitter.com/re4sonkernel)의 커널을 사용하며, 여기에는 외장 Wi-Fi 카드, TFT 디스플레이용 드라이버 및 [라즈베리 파이 3](/docs/arm/raspberry-pi-3/)과 [4](/docs/arm/raspberry-pi-4/)의 내장 무선 카드를 위한 [nexmon](https://github.com/seemoo-lab/nexmon) 펌웨어가 포함되어 있습니다. 별도로 다운로드하여 설치할 필요가 없으며, 그렇게 하면 현재 설치된 커널보다 다운그레이드될 가능성이 높습니다.
{{% /notice %}}

## 라즈베리 파이 3용 Kali - 사용자 지침

[Kali Linux 이미지 다운로드 및 검증](/docs/introduction/download-official-kali-linux-images/) 또는 [해당 이미지를 사용하여 부팅 가능한 장치 생성](/docs/usb/live-usb-install-with-windows/)에 익숙하지 않은 경우, 해당 주제에 대해 더 자세히 설명된 절차를 참조하는 것이 좋습니다.

라즈베리 파이 3에 Kali Linux의 사전 빌드된 표준 이미지를 설치하려면 다음 지침을 따르세요:

1. 최소 16GB 용량의 빠른 microSD 카드를 준비하세요. Class 10 카드를 강력히 권장합니다.
2. [다운로드](/get-kali/) 영역에서 선호하는 `Kali 라즈베리 파이 3` 이미지를 다운로드 _및 검증_ 하세요. 이미지 검증 과정은 [Kali Linux 다운로드](/docs/introduction/download-official-kali-linux-images/)에 자세히 설명되어 있습니다.
3. **[dd](https://manpages.debian.org/buster/coreutils/dd.1.en.html)** 유틸리티를 사용하여 이 파일을 microSD 카드에 이미징하세요 ([Kali USB 만들기](/docs/usb/live-usb-install-with-windows/)와 동일한 과정).

아래 예시에서는 저장 장치가 `/dev/sdX`에 위치한다고 가정합니다. 이 값을 단순히 복사하지 마시고, **올바른 드라이브 경로로 변경하세요**.

{{% notice info %}}
이 과정은 microSD 카드의 모든 데이터를 지웁니다. 잘못된 저장 장치를 선택하면 컴퓨터의 하드 디스크가 지워질 수 있습니다.
{{% /notice %}}

```console
$ xzcat kali-linux-2025.1-raspberry-pi-armhf.img.xz | sudo dd of=/dev/sdX bs=4M status=progress
```

**또는**

```console
$ xzcat kali-linux-2025.1-raspberry-pi-arm64.img.xz | sudo dd of=/dev/sdX bs=4M status=progress
```

이 과정은 PC, microSD 카드 속도 및 Kali Linux 이미지 크기에 따라 시간이 소요될 수 있습니다.

_dd_ 작업이 완료되면 microSD 카드를 삽입한 상태로 라즈베리 파이 3을 부팅하세요.

[Kali에 로그인](/docs/introduction/default-credentials/)할 수 있어야 합니다.

## 라즈베리 파이 3용 Kali - 팁과 트릭

라즈베리 파이 3의 블루투스 서비스는 작동하기 전에 **uart helper 서비스**가 필요합니다. 블루투스 서비스를 활성화하고 시작하려면 다음 명령어를 실행하세요:

```console
kali@kali:~$ sudo systemctl enable --now hciuart.service
kali@kali:~$ sudo systemctl enable --now bluetooth.service
```

5.10 이상의 커널을 사용 중이라면, mt76 칩셋 USB Wi-Fi 장치를 사용할 수 있지만, `/etc/modprobe.d`에 다음 내용의 설정 파일을 생성해야 합니다:

```plaintext
# RPi2 또는 RPi3 USB 칩셋에서 작동하지 않는 scatter-gather 기능 없이 mt76usb를 로드
options mt76-usb disable_usb_sg=1
```

## 헤드리스 라즈베리 파이 3용 Kali - 팁과 트릭

무선 네트워크에 연결하기 위해 microSD 카드의 첫 번째 파티션에 `wpa_supplicant.conf` 파일을 추가할 수 있습니다.

다른 Linux 시스템에서 `wpa_passphrase 네트워크이름 > wpa_supplicant.conf` 명령어를 실행하여 이 파일을 생성할 수 있습니다. 무선 네트워크의 비밀번호를 입력하라는 메시지가 표시됩니다. 명령어를 실행할 때 비밀번호를 추가할 수도 있지만, 그렇게 하면 Wi-Fi 네트워크 비밀번호가 사용자의 쉘 히스토리에 남게 된다는 점을 유의하세요.

## 라즈베리 파이 3용 Kali - 이미지 사용자 정의

Kali 라즈베리 파이 3 이미지를 사용자 정의하려면, 설치되는 [패키지](/docs/general-use/metapackages/) 변경, [데스크톱 환경](/docs/general-use/switching-desktop-environments/) 변경, 이미지 파일 크기 증가 또는 감소 등의 작업을 하고 싶다면, GitLab의 [Kali-ARM 빌드-스크립트](https://gitlab.com/kalilinux/build-scripts/kali-arm) 저장소를 확인하고 _README.md_ 파일의 지침을 따르세요. 사용할 스크립트는 `raspberry-pi.sh`(32비트) 또는 `raspberry-pi-64-bit.sh`(64비트)입니다.
