---
title: 라즈베리 파이 3
description:
icon:
weight:
author: ["steev",]
번역: ["xenix4845"]
---

[라즈베리 파이 3](https://www.raspberrypi.org/products/raspberry-pi-3-model-b/)은 쿼드 코어 1.2GHz 프로세서와 1GB RAM을 갖추고 있어요. Kali Linux는 외장 microSD 카드에 설치해 사용할 수 있어요.

기본적으로 Kali Linux 라즈베리 파이 3 이미지는 다른 플랫폼과 유사하게 [**kali-linux-default** 메타패키지](/docs/general-use/metapackages/)를 포함하고 있어요. 추가 도구를 설치하고 싶다면 [메타패키지 페이지](/docs/general-use/metapackages/)를 참조하세요.

{{% notice info %}}
라즈베리 파이 3은 64비트 프로세서를 탑재했기 때문에 64비트 이미지를 실행할 수 있어요.
64비트 이미지를 실행할 수 있기 때문에 `Kali Linux Raspberry Pi 2, 3, 4 및 400 (32-bit) (img.xz)` 또는 `Kali Linux Raspberry Pi 2 (v1.2), 3, 4 및 400 (64-bit) (img.xz)` 중에서 선택할 수 있으며, 후자가 64비트 이미지예요.<br />
<br />
라즈베리 파이 장치에는 32비트 이미지를 사용하는 것을 권장해요. 32비트가 더 많은 테스트를 거쳤고, 대부분의 문서가 32비트인 RaspberryPi OS를 기준으로 작성되어 있기 때문이에요.
{{% /notice %}}

## 라즈베리 파이 3에서 Kali - 사용자 지침

[Kali Linux 이미지 다운로드 및 검증](/docs/introduction/download-official-kali-linux-images/) 방법이나 [해당 이미지로 부팅 가능한 장치 만들기](/docs/usb/live-usb-install-with-windows/)에 익숙하지 않다면, 이러한 주제에 대해 더 자세히 설명하는 특정 문서를 참조하는 것이 좋아요.

라즈베리 파이 3에 표준 Kali Linux의 사전 빌드된 이미지를 설치하려면 다음 지침을 따르세요:

1. 최소 16GB 용량의 빠른 microSD 카드를 준비하세요. Class 10 카드를 강력히 권장해요.
2. [다운로드](/get-kali/) 영역에서 선호하는 `Kali 라즈베리 파이 3` 이미지를 다운로드하고 _검증_하세요. 이미지 검증 과정은 [Kali Linux 다운로드](/docs/introduction/download-official-kali-linux-images/)에 더 자세히 설명되어 있어요.
3. **[dd](https://manpages.debian.org/buster/coreutils/dd.1.en.html)** 유틸리티를 사용하여 이 파일을 microSD 카드에 이미징하세요([Kali USB 만들기](/docs/usb/live-usb-install-with-windows/)와 동일한 과정).

예시에서는 저장 장치가 `/dev/sdX`에 위치한다고 가정해요. 이 값을 그대로 복사하지 _말고_, **올바른 드라이브 경로로 변경**하세요.

{{% notice info %}}
이 과정은 microSD 카드의 모든 내용을 지워요. 잘못된 저장 장치를 선택하면 컴퓨터의 하드 디스크가 지워질 수 있어요.
{{% /notice %}}

```console
$ xzcat kali-linux-2024.4-raspberry-pi-armhf.img.xz | sudo dd of=/dev/sdX bs=4M status=progress
```

**또는**

```console
$ xzcat kali-linux-2024.4-raspberry-pi-arm64.img.xz | sudo dd of=/dev/sdX bs=4M status=progress
```

이 과정은 PC, microSD 속도, Kali Linux 이미지 크기에 따라 시간이 걸릴 수 있어요.

_dd_ 작업이 완료되면 microSD를 꽂은 상태로 라즈베리 파이 3을 부팅하세요.

이제 [Kali에 로그인](/docs/introduction/default-credentials/)할 수 있어요.

## 라즈베리 파이 3 - 팁과 요령

라즈베리 파이 3의 블루투스 서비스는 작동하기 전에 **uart 헬퍼 서비스**가 필요해요. 블루투스 서비스를 활성화하고 시작하려면 다음 명령어를 실행하세요:

```console
kali@kali:~$ sudo systemctl enable --now hciuart.service
kali@kali:~$ sudo systemctl enable --now bluetooth.service
```

- - -

mt76 칩셋 USB Wi-Fi 장치를 사용할 수 있지만, 다음 내용이 포함된 구성 파일을 `/etc/modprobe.d`에 만들어야 해요:

```plaintext
# Load mt76usb without using scatter-gather which doesn't work on the RPi2 or RPi3 USB chipset
options mt76-usb disable_usb_sg=1
```

- - -

Kali는 기본적으로 데스크톱으로 Xorg에서 XFCE와 함께 LightDM을 사용해요. 테스트 중에 많은 HAT 시스템이 디스플레이 출력을 위한 구성 스니펫이 필요하다는 것을 발견했어요. 출력에 문제가 있다면 반대의 경우일 수 있으니, `/etc/X11/Xorg.conf.d/99-vc4.conf` 파일을 제거하고 Xorg가 기본값을 사용하도록 해보세요.

```console
kali@kali:~$ sudo mv -v /etc/X11/Xorg.conf.d/99-vc4.conf ~
```

다른 방법으로는 구성 스니펫을 수정해야 할 수도 있어요. LCD 설명서를 참조하는 것이 가장 좋아요.

- - -

## 헤드리스(모니터 없이) 라즈베리 파이 3에서 Kali - 팁과 요령

무선 네트워크에 연결하기 위해 microSD 카드의 첫 번째 파티션에 `wpa_supplicant.conf` 파일을 추가할 수 있어요.

다른 Linux 시스템에서 `wpa_passphrase YOURNETWORK > wpa_supplicant.conf` 명령을 실행해 이 파일을 생성할 수 있어요. 무선 네트워크 비밀번호를 입력하라는 메시지가 표시돼요. 명령을 실행할 때 비밀번호를 추가할 수도 있지만, 그렇게 하면 Wi-Fi 네트워크 비밀번호가 사용자의 쉘 히스토리에 남게 된다는 점을 기억하세요.

## 라즈베리 파이 3 - 이미지 사용자 지정

설치되는 [패키지](/docs/general-use/metapackages/) 변경, [데스크톱 환경](/docs/general-use/switching-desktop-environments/) 변경, 이미지 파일 크기 증가 또는 감소 등 Kali 라즈베리 파이 3 이미지를 사용자 지정하고 싶다면, GitLab의 [Kali-ARM Build-Scripts](https://gitlab.com/kalilinux/build-scripts/kali-arm) 저장소를 확인하고 _README.md_ 파일의 지침을 따르세요. 사용할 스크립트는 `raspberry-pi.sh`(32비트) 또는 `raspberry-pi-64-bit.sh`(64비트)예요.
