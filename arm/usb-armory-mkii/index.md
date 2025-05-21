---
title: USB 아머리 MKII
description:
icon:
weight:
author: ["steev",]
번역: ["xenix4845"]
---

Inverse Path의 [USB 아머리 MKII](https://inversepath.com/usbarmory)는 USB 플래시 드라이브 크기의 컴퓨터를 구현한 오픈 소스 하드웨어 디자인입니다. 칼리 리눅스는 microSD 카드에 설치할 수 있습니다.

기본적으로 칼리 리눅스 USB 아머리 MKII 이미지는 다른 Kali 플랫폼에서 흔히 볼 수 있는 [**kali-linux-default** 메타패키지](/docs/general-use/metapackages/)를 **포함하지 않습니다**. 추가 도구를 설치하려면 [메타패키지 페이지](/docs/general-use/metapackages/)를 참조하세요.

## USB 아머리 MKII용 칼리 - 사용자 지침

[칼리 리눅스 이미지 다운로드 및 검증](/docs/introduction/download-official-kali-linux-images/) 또는 [해당 이미지를 사용하여 부팅 가능한 장치 만들기](/docs/usb/live-usb-install-with-windows/)에 대한 자세한 내용에 익숙하지 않은 경우, 해당 주제에 대해 특정 문서에 설명된 더 자세한 절차를 참조하는 것이 좋습니다.

USB 아머리 MKII에 칼리 리눅스의 표준 빌드 사전 구축 이미지를 설치하려면 다음 지침을 따르세요:

1. 최소 16GB 용량의 빠른 microSD 카드를 준비하세요. Class 10 카드를 강력히 권장합니다.
2. [다운로드](/get-kali/) 영역에서 `칼리 USB 아머리 MKII` 이미지를 다운로드하고 _검증하세요_. 이미지 검증 과정은 [칼리 리눅스 다운로드](/docs/introduction/download-official-kali-linux-images/)에서 더 자세히 설명되어 있습니다.
3. **[dd](https://manpages.debian.org/testing/coreutils/dd.1.en.html)** 유틸리티를 사용하여 이 파일을 microSD 카드에 이미징하세요. 이 예시에서는 `/dev/sdX`에 위치한 microSD 카드를 사용합니다. **_필요에 따라 이 경로를 변경하세요._**

{{% notice info %}}
이 과정은 microSD 카드의 모든 데이터를 지웁니다. 잘못된 저장 장치를 선택하면 컴퓨터의 하드 디스크가 지워질 수 있습니다.
{{% /notice %}}

```console
$ xzcat kali-linux-2025.1-usb-armory-mkii-armhf.img.xz | sudo dd of=/dev/sdX bs=4M status=progress
```

이 과정은 PC, microSD 카드 속도 및 칼리 리눅스 이미지 크기에 따라 시간이 소요될 수 있습니다.

_dd_ 작업이 완료되면, USB 아머리를 꽂은 상태로 컴퓨터를 부팅하세요.

[칼리에 로그인](/docs/introduction/default-credentials/)할 수 있어야 합니다.

## USB 아머리 MKII용 칼리 - 이미지 사용자 정의

칼리 USB 아머리 MKII 이미지를 사용자 정의하려면, 설치되는 [패키지](/docs/general-use/metapackages/) 변경, [데스크톱 환경](/docs/general-use/switching-desktop-environments/) 변경, 이미지 파일 크기 증가 또는 감소 등의 작업을 하고 싶다면, GitLab의 [칼리-ARM 빌드 스크립트](https://gitlab.com/kalilinux/build-scripts/kali-arm) 저장소를 확인하고 _README.md_ 파일의 지침을 따르세요. 사용할 스크립트는 `usb-armory-mkii.sh`입니다.
