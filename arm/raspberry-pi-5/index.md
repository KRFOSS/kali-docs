---
title: Raspberry Pi 5
description:
icon:
weight:
author: ["steev",]
번역: ["xenix4845"]
---

[라즈베리 파이 5](https://www.raspberrypi.org/products/raspberry-pi-5/)는 쿼드 코어 2.4GHz 프로세서와 모델에 따라 4GB 또는 8GB RAM을 갖추고 있습니다. Kali Linux는 microSD 카드에서 실행됩니다.

기본적으로 Kali Linux 라즈베리 파이 5 이미지는 다른 대부분의 플랫폼과 유사하게 [**kali-linux-default** 메타패키지](/docs/general-use/metapackages/)를 포함합니다. 추가 도구를 설치하려면 [메타패키지 페이지](/docs/general-use/metapackages/)를 참조하세요.

{{% notice info %}}
라즈베리 파이 5 지원은 아직 초기 단계이며 현재 하드웨어 없이 이미지를 개발 중입니다.
<br />
{{% /notice %}}

{{% notice info %}}
라즈베리 파이 5 이미지는 아직 내장 Wi-Fi 카드를 위한 [Nexmon](https://github.com/seemoo-lab/nexmon) 지원을 포함하지 않습니다. 무선 테스트를 계획하고 있다면 USB Wi-Fi 어댑터를 사용해야 합니다.
{{% /notice %}}

## 라즈베리 파이 5용 Kali - 사용자 지침

[Kali Linux 이미지 다운로드 및 검증](/docs/introduction/download-official-kali-linux-images/) 또는 [해당 이미지를 사용하여 부팅 가능한 장치 생성](/docs/usb/live-usb-install-with-windows/)에 익숙하지 않은 경우, 해당 주제에 대해 더 자세히 설명된 절차를 참조하는 것이 좋습니다.

라즈베리 파이 5에 Kali Linux의 사전 빌드된 표준 이미지를 설치하려면 다음 지침을 따르세요:

1. 최소 16GB 용량의 빠른 microSD 카드를 준비하세요. Class 10 카드를 강력히 권장합니다.
2. [다운로드](/get-kali/) 영역에서 선호하는 `Kali 라즈베리 파이 5` 이미지를 다운로드 _및 검증_ 하세요. 이미지 검증 과정은 [Kali Linux 다운로드](/docs/introduction/download-official-kali-linux-images/)에 자세히 설명되어 있습니다.
3. **[dd](https://manpages.debian.org/testing/coreutils/dd.1.en.html)** 유틸리티를 사용하여 이 파일을 microSD 카드에 이미징하세요 ([Kali USB 만들기](/docs/usb/live-usb-install-with-windows/)와 동일한 과정).

아래 예시에서는 저장 장치가 `/dev/sdb`에 위치한다고 가정합니다. 이 값을 단순히 복사하지 마시고, **올바른 드라이브 경로로 변경하세요**.

{{% notice info %}}
이 과정은 microSD 카드의 모든 데이터를 지웁니다. 잘못된 저장 장치를 선택하면 컴퓨터의 하드 디스크가 지워질 수 있습니다.
{{% /notice %}}

```console
$ xzcat kali-linux-2025.1-raspberry-pi-arm64.img.xz | sudo dd of=/dev/sdb bs=4M status=progress
```

이 과정은 PC, microSD 카드 속도 및 Kali Linux 이미지 크기에 따라 시간이 소요될 수 있습니다.

_dd_ 작업이 완료되면 microSD 카드를 삽입한 상태로 라즈베리 파이 5를 부팅하세요.

[Kali에 로그인](/docs/introduction/default-credentials/)할 수 있어야 합니다.

## 라즈베리 파이 5용 Kali - 팁과 트릭

<!-- Need to verify this is true with the 5 before adding it.
By default, audio is routed via HDMI, so you won't hear audio via the 3.5mm audio jack. You can run the following command in order to redirect the output:

```console
kali@kali:~$ sudo amixer -c 0 set numid=3 1
```
-->

대부분의 지침에서는 외부 모듈을 커널에 대해 빌드하려면 헤더 패키지를 설치해야 한다고 명시하고 있습니다. 그러나 이는 라즈베리 파이 5 이미지에서는 **해당되지 않습니다**. 헤더는 이미 포함되어 있으며 별도의 패키지의 **일부가 아닙니다**.
하지만 헤더를 사용 가능하게 하려면 다음 명령어를 실행해야 합니다.

```console
kali@kali:~$ cd /usr/src/kernel
kali@kali:/usr/src/kernel/$ sudo git clean -fdx && sudo make bcm2711_defconfig && sudo make modules_prepare
```

Kali 패키지 저장소의 외부 모듈을 빌드하려면, 예를 들어 [realtek-rtl88xxau-dkms](https://pkg.kali.org/pkgs/realtek-rtl88xxau-dkms)의 경우 권장 패키지를 함께 설치하지 **않고** 설치하도록 지정해야 합니다.

```console
kali@kali:~$ sudo apt install --no-install-recommends realtek-rtl88xxau-dkms
```

- - -

# 헤드리스 라즈베리 파이 5용 Kali - 팁과 트릭

무선 네트워크에 연결하기 위해 microSD 카드의 첫 번째 파티션에 `wpa_supplicant.conf` 파일을 추가할 수 있습니다.

다른 Linux 시스템에서 `wpa_passphrase 네트워크이름 > wpa_supplicant.conf` 명령어를 실행하여 이 파일을 생성할 수 있습니다. 무선 네트워크의 비밀번호를 입력하라는 메시지가 표시됩니다. 명령어를 실행할 때 비밀번호를 추가할 수도 있지만, 그렇게 하면 Wi-Fi 네트워크 비밀번호가 사용자의 쉘 히스토리에 남게 된다는 점을 유의하세요.

- - -

## 라즈베리 파이 5용 Kali - 이미지 사용자 정의

Kali 라즈베리 파이 5 이미지를 사용자 정의하려면, 설치되는 [패키지](/docs/general-use/metapackages/) 변경, [데스크톱 환경](/docs/general-use/switching-desktop-environments/) 변경, 이미지 파일 크기 증가 또는 감소 등의 작업을 하고 싶다면, GitLab의 [Kali-ARM 빌드-스크립트](https://gitlab.com/kalilinux/build-scripts/kali-arm) 저장소를 확인하고 _README.md_ 파일의 지침을 따르세요. 사용할 스크립트는 `raspberry-pi5.sh`(64비트)입니다.
