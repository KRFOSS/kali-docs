---
title: 라즈베리 파이 1 (오리지널)
description:
icon:
weight:
author: ["steev",]
번역: ["xenix4845"]
---

{{% notice info %}}
초기 버전의 라즈베리 파이 1 (오리지널) 보드는 풀 사이즈 SD 카드 슬롯을 갖고 있지만, 이후 버전의 보드는 microSD 카드 슬롯으로 변경되었어요. 이 문서에서는 풀 사이즈 SD 카드를 사용하는 방법을 설명하지만, microSD 카드의 과정도 동일해요.
{{% /notice %}}

[라즈베리 파이 1](https://raspberrypi.org/)은 저렴한 가격에 신용카드 크기의 ARM 컴퓨터예요. "표준" 노트북이나 데스크톱 PC보다 성능이 낮지만, 그 경제적인 가격 덕분에 작은 Linux 시스템으로는 훌륭한 선택이에요. 라즈베리 파이 1은 대용량 저장을 위한 풀 사이즈 SD 카드 슬롯을 제공하며, 보드에 전원이 공급되면 해당 장치에서 부팅을 시도해요.

기본적으로 칼리 리눅스 라즈베리 파이 1 이미지는 다른 플랫폼과 마찬가지로 [**kali-linux-default** 메타패키지](/docs/general-use/metapackages/)를 포함하고 있어요. 추가 도구를 설치하고 싶다면 [메타패키지 페이지](/docs/general-use/metapackages/)를 참조하세요.

## 라즈베리 파이 1에서 칼리 - 사용자 지침

[칼리 리눅스 이미지 다운로드 및 검증](/docs/introduction/download-official-kali-linux-images/) 방법이나 [해당 이미지로 부팅 가능한 장치 만들기](/docs/usb/live-usb-install-with-windows/)에 익숙하지 않다면, 이러한 주제에 대해 더 자세히 설명하는 특정 문서를 참조하는 것이 좋아요.

라즈베리 파이에 표준 칼리 리눅스의 사전 빌드된 이미지를 설치하려면 다음 과정을 따르세요:

1. 최소 16GB 용량의 빠른 풀 사이즈 SD 카드를 준비하세요. Class 10 카드를 강력히 권장해요.
2. [다운로드](/get-kali/) 영역에서 칼리 리눅스 라즈베리 파이 1 이미지를 다운로드하고 _검증_하세요. 이미지 검증 과정은 [칼리 리눅스 다운로드](/docs/introduction/download-official-kali-linux-images/)에 더 자세히 설명되어 있어요.
3. **[dd](https://manpages.debian.org/testing/coreutils/dd.1.en.html)** 유틸리티를 사용하여 이 파일을 풀 사이즈 SD 카드에 이미징하세요([칼리 USB 만들기](/docs/usb/live-usb-install-with-windows/)와 동일한 과정).

예시에서는 저장 장치가 `/dev/sdX`에 위치한다고 가정해요. 이 값을 그대로 복사하지 _말고_, **올바른 드라이브 경로로 변경**하세요.

{{% notice info %}}
이 과정은 풀 사이즈 SD 카드의 모든 내용을 지워요. 잘못된 저장 장치를 선택하면 컴퓨터의 하드 디스크가 지워질 수 있어요.
{{% /notice %}}

```console
$ xzcat kali-linux-2025.3-raspberry-pi1-armel.img.xz | sudo dd of=/dev/sdX bs=4M status=progress
```

이 과정은 PC, 풀 사이즈 SD 카드 속도, 칼리 리눅스 이미지 크기에 따라 시간이 걸릴 수 있어요.

_dd_ 작업이 완료되면 풀 사이즈 SD 카드를 꽂은 상태로 라즈베리 파이 1을 부팅하세요.

이제 [Kali에 로그인](/docs/introduction/default-credentials/)할 수 있어야 해요.

## 라즈베리 파이 1 - 팁

라즈베리 파이에는 무선 기능이 없으므로 무선 연결을 위해 외부 장치가 필요해요.

- - -

커널에 외부 모듈을 빌드하려면 대부분의 지침에서 `linux-headers-$(uname -r)`를 통해 헤더 패키지를 설치해야 한다고 말하지만, 라즈베리 파이 이미지에서는 **그렇지 않아요**. 헤더 패키지는 이미 포함되어 있으며 그런 명명 체계를 따르지 않고 `linux-headers-rpi-v6`라는 이름을 가져요. 삭제한 경우 다음 명령어를 실행하여 다시 추가할 수 있어요:

```console
kali@kali:~$ sudo apt update
[...]
kali@kali:~$ sudo apt install -y linux-headers-rpi-v6
```

- - -

## 라즈베리 파이 1 - 이미지 사용자 지정

설치되는 [패키지](/docs/general-use/metapackages/) 변경, [데스크톱 환경](/docs/general-use/switching-desktop-environments/) 변경, 이미지 파일 크기 증가 또는 감소 등 칼리 라즈베리 파이 1 이미지를 사용자 지정하고 싶다면, GitLab의 [칼리-ARM 빌드 스크립트](https://gitlab.com/kalilinux/build-scripts/kali-arm) 저장소를 확인하고 _README.md_ 파일의 지침을 따르세요. 사용할 스크립트는 `raspberry-pi1.sh`예요.
