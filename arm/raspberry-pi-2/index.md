---
title: 라즈베리 파이 2
description:
icon:
weight:
author: ["steev",]
번역: ["xenix4845"]
---

{{% notice info %}}
만약 라즈베리 파이 2의 CPU 위 PCB에 `Raspberry Pi 2 Model B V1.2`라고 인쇄되어 있다면, [라즈베리 파이 2 v1.2 문서](/docs/arm/raspberry-pi-64-bit/)를 참고하세요. 하지만 `Raspberry Pi 2 Model B V1.1`이라고 되어 있다면, 계속 읽어주세요.
{{% /notice %}}

기본적으로 칼리 리눅스 라즈베리 파이 2 이미지는 다른 플랫폼과 유사하게 [**kali-linux-default** 메타패키지](/docs/general-use/metapackages/)를 포함하고 있어요. 추가 도구를 설치하고 싶다면 [메타패키지 페이지](/docs/general-use/metapackages/)를 참조하세요.

## 라즈베리 파이 2에서 칼리 - 사용자 지침

[칼리 리눅스 이미지 다운로드 및 검증](/docs/introduction/download-official-kali-linux-images/) 방법이나 [해당 이미지로 부팅 가능한 장치 만들기](/docs/usb/live-usb-install-with-windows/)에 익숙하지 않다면, 이러한 주제에 대해 더 자세히 설명하는 특정 문서를 참조하는 것이 좋아요.

라즈베리 파이 2에 표준 칼리 리눅스의 사전 빌드된 이미지를 설치하려면 다음 지침을 따르세요:

1. 최소 16GB 용량의 빠른 마이크로 SD 카드를 준비하세요. Class 10 카드를 강력히 권장해요.
2. [다운로드](/get-kali/) 영역에서 `Kali RaspberryPi 2, 3, 4 및 400 (img.xz)` 이미지를 다운로드하고 _검증_하세요. 이미지 검증 과정은 [칼리 리눅스 다운로드](/docs/introduction/download-official-kali-linux-images/)에 더 자세히 설명되어 있어요.
3. **[dd](https://manpages.debian.org/testing/coreutils/dd.1.en.html)** 유틸리티를 사용하여 이 파일을 마이크로 SD 카드에 이미징하세요([칼리 USB 만들기](/docs/usb/live-usb-install-with-windows/)와 동일한 과정).

예시에서는 저장 장치가 `/dev/sdX`에 위치한다고 가정해요. 이 값을 그대로 복사하지 _말고_, **올바른 드라이브 경로로 변경**하세요.

{{% notice info %}}
이 과정은 마이크로 SD 카드의 모든 내용을 지워요. 잘못된 저장 장치를 선택하면 컴퓨터의 하드 디스크가 지워질 수 있어요.
{{% /notice %}}

```console
$ xzcat kali-linux-2025.3-raspberry-pi-armhf.img.xz | sudo dd of=/dev/sdX bs=4M status=progress
```

이 과정은 PC, 마이크로 SD 카드 속도, 칼리 리눅스 이미지 크기에 따라 시간이 걸릴 수 있어요.

_dd_ 작업이 완료되면 마이크로 SD를 꽂은 상태로 라즈베리 파이 2를 부팅하세요.

이제 [칼리에 로그인](/docs/introduction/default-credentials/)할 수 있어야 해요.

## 라즈베리 파이 2 - 팁

커널에 외부 모듈을 빌드하려면 대부분의 지침에서 `linux-headers-$(uname -r)`를 통해 헤더 패키지를 설치해야 한다고 말하지만, 라즈베리 파이 2 이미지에서는 **그렇지 않아요**. 헤더 패키지는 이미 포함되어 있으며 그런 명명 체계를 따르지 않고 `linux-headers-rpi-v7` 및 `linux-headers-rpi-v7l`이라는 이름을 가져요. 삭제한 경우 다음 명령어를 실행하여 다시 추가할 수 있어요:

```console
kali@kali:~$ sudo apt update
[...]
kali@kali:~$ sudo apt install -y linux-headers-rpi-v7 linux-headers-rpi-v7l

```

- - -

mt76 칩셋 USB Wi-Fi 장치를 사용할 수 있지만, 다음 내용이 포함된 구성 파일을 `/etc/modprobe.d`에 만들어야 해요:

```plaintext
# Load mt76usb without using scatter-gather which doesn't work on the RPi2 or RPi3 USB chipset
options mt76-usb disable_usb_sg=1
```

- - -

칼리는 기본적으로 데스크톱으로 Xorg에서 XFCE와 함께 LightDM을 사용해요. 테스트 중에 많은 HAT 시스템이 디스플레이 출력을 위한 구성 스니펫이 필요하다는 것을 발견했어요. 출력에 문제가 있다면 반대의 경우일 수 있으니, `/etc/X11/Xorg.conf.d/99-vc4.conf` 파일을 제거하고 Xorg가 기본값을 사용하도록 해보세요.

```console
kali@kali:~$ sudo mv /etc/X11/Xorg.conf.d/99-vc4.conf ~
```

다른 방법으로는 구성 스니펫을 수정해야 할 수도 있어요. LCD 설명서를 참조하는 것이 가장 좋아요.

- - -

## 라즈베리 파이 2 - 이미지 사용자 지정

설치되는 [패키지](/docs/general-use/metapackages/) 변경, [데스크톱 환경](/docs/general-use/switching-desktop-environments/) 변경, 이미지 파일 크기 증가 또는 감소 등 칼리 라즈베리 파이 2 이미지를 사용자 지정하고 싶다면, GitLab의 [칼리-ARM 빌드 스크립트](https://gitlab.com/kalilinux/build-scripts/kali-arm) 저장소를 확인하고 _README.md_ 파일의 지침을 따르세요. 사용할 스크립트는 `raspberry-pi.sh`예요.
