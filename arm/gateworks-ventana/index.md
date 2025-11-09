---
title: 게이트웍스 벤타나
description:
icon:
weight:
author: ["steev",]
번역: ["xenix4845"]
---

[게이트웍스 벤타나](https://www.gateworks.com/products/industrial-single-board-computers/imx6-single-board-computer-gateworks-ventana-family/)는 플래시 드라이브 크기의 컴퓨터를 구현했습니다. 칼리 리눅스는 마이크로 SD 카드에 설치할 수 있습니다.

_이 이미지는 "NXP (이전 Freescale) i.MX6" 기반 보드용입니다._

기본적으로 칼리 리눅스 게이트웍스 벤타나 이미지는 다른 대부분의 플랫폼과 유사하게 [**kali-linux-default** 메타패키지](/docs/general-use/metapackages/)를 포함합니다. 추가 도구를 설치하려면 [메타패키지 페이지](/docs/general-use/metapackages/)를 참조하세요.

 <!-- @steev: TODO: This is a community contributed image, so find out which specific ventana this is for as they have a number of them. -->

## 게이트웍스 벤타나용 칼리 - 사용자 지침

[칼리 리눅스 이미지 다운로드 및 검증](/docs/introduction/download-official-kali-linux-images/) 또는 [해당 이미지를 사용하여 부팅 가능한 장치 생성](/docs/usb/live-usb-install-with-windows/)에 익숙하지 않은 경우, 해당 주제에 대해 더 자세히 설명된 절차를 참조하는 것이 좋습니다.

벤타나에 칼리 리눅스의 사전 빌드된 표준 이미지를 설치하려면 다음 지침을 따르세요:

1. 최소 16GB 용량의 빠른 마이크로 SD 카드를 준비하세요. Class 10 카드를 강력히 권장합니다.
2. [다운로드](/get-kali/) 영역에서 `칼리 벤타나` 이미지를 다운로드 _및 검증_ 하세요. 이미지 검증 과정은 [칼리 리눅스 다운로드](/docs/introduction/download-official-kali-linux-images/)에 자세히 설명되어 있습니다.
3. **[dd](https://manpages.debian.org/testing/coreutils/dd.1.en.html)** 유틸리티를 사용하여 이 파일을 마이크로 SD 카드에 이미징하세요. 아래 예시에서는 마이크로 SD 카드가 `/dev/sdX`에 위치한다고 가정합니다. **_필요에 따라 이 경로를 변경하세요._**

{{% notice info %}}
이 과정은 마이크로 SD 카드의 모든 데이터를 지웁니다. 잘못된 저장 장치를 선택하면 컴퓨터의 하드 디스크가 지워질 수 있습니다.
{{% /notice %}}

```console
$ xzcat kali-linux-2025.3-gateworks-ventana-armhf.img.xz | sudo dd of=/dev/sdX bs=4M status=progress
```

이 과정은 PC, 마이크로 SD 카드 속도 및 칼리 리눅스 이미지 크기에 따라 시간이 소요될 수 있습니다.

_dd_ 작업이 완료되면, 게이트웍스 벤타나가 연결된 상태로 컴퓨터를 부팅하세요.

[칼리에 로그인](/docs/introduction/default-credentials/)할 수 있어야 합니다.

## 게이트웍스 벤타나용 칼리 - 이미지 사용자 정의

칼리 게이트웍스 벤타나 이미지를 사용자 정의하려면, 설치되는 [패키지](/docs/general-use/metapackages/) 변경, [데스크톱 환경](/docs/general-use/switching-desktop-environments/) 변경, 이미지 파일 크기 증가 또는 감소 등의 작업을 하고 싶다면, 깃랩의 [칼리-ARM 빌드 스크립트](https://gitlab.com/kalilinux/build-scripts/kali-arm) 저장소를 확인하고 _README.md_ 파일의 지침을 따르세요. 사용할 스크립트는 `gateworks-ventana.sh`입니다.
