---
title: 파인북 프로
description:
icon:
weight:
author: ["steev",]
번역: ["xenix4845"]
---

[파인북 프로](https://www.pine64.org/pinebook-pro/)는 Mali T860 MP4 GPU와 4GB LPDDR4 RAM을 갖춘 Rockchip RK3399 SOC를 탑재하고 있습니다. 칼리 리눅스는 외장 마이크로 SD 카드나 내장 eMMC에서 실행할 수 있습니다.

기본적으로 칼리 리눅스 파인북 Pro 이미지는 다른 대부분의 플랫폼과 유사하게 [**kali-linux-default** 메타패키지](/docs/general-use/metapackages/)를 포함합니다. 추가 도구를 설치하려면 [메타패키지 페이지](/docs/general-use/metapackages/)를 참조하세요.

## 파인북 Pro 마이크로 SD 카드용 칼리 - 사용자 지침

[칼리 리눅스 이미지 다운로드 및 검증](/docs/introduction/download-official-kali-linux-images/) 또는 [해당 이미지를 사용하여 부팅 가능한 장치 생성](/docs/usb/live-usb-install-with-windows/)에 익숙하지 않은 경우, 해당 주제에 대해 더 자세히 설명된 절차를 참조하는 것이 좋습니다.

파인북 프로에 칼리 리눅스의 사전 빌드된 표준 이미지를 설치하려면 다음 지침을 따르세요:

1. 최소 16GB 용량의 빠른 마이크로 SD 카드 또는 eMMC 모듈을 준비하세요. Class 10 카드를 강력히 권장합니다.
2. [다운로드](/get-kali/) 영역에서 `칼리 파인북 프로` 이미지를 다운로드 _및 검증_ 하세요. 이미지 검증 과정은 [칼리 리눅스 다운로드](/docs/introduction/download-official-kali-linux-images/)에 자세히 설명되어 있습니다.
3. **[dd](https://manpages.debian.org/testing/coreutils/dd.1.en.html)** 유틸리티를 사용하여 이 파일을 마이크로 SD 카드에 이미징하세요 ([칼리 USB 만들기](/docs/usb/live-usb-install-with-windows/)와 동일한 과정).

아래 예시에서는 저장 장치가 `/dev/sdX`에 위치한다고 가정합니다. 이 값을 단순히 복사하지 마시고, **올바른 드라이브 경로로 변경하세요**.

{{% notice info %}}
이 과정은 마이크로 SD 카드의 모든 데이터를 지웁니다. 잘못된 저장 장치를 선택하면 컴퓨터의 하드 디스크나 eMMC 모듈이 지워질 수 있습니다.
{{% /notice %}}

```console
$ xzcat kali-linux-2025.4-pinebook-pro-arm64.img.xz | sudo dd of=/dev/sdX bs=4M status=progress
```

이 과정은 PC, 마이크로 SD 카드 속도 및 칼리 리눅스 이미지 크기에 따라 시간이 소요될 수 있습니다.

_dd_ 작업이 완료되면, 마이크로 SD 카드를 꽂은 상태로 파인북 프로를 부팅하세요.

[칼리에 로그인](/docs/introduction/default-credentials/)할 수 있어야 합니다.

## 파인북 프로 eMMC용 칼리 - 사용자 지침

파인북 프로의 eMMC 모듈에 칼리를 설치하려면 두 가지 방법이 있습니다.

[eMMC 모듈용 USB 어댑터](https://pine64.com/product/usb-adapter-for-emmc-module/?v=0446c16e2e66)가 있다면 마이크로 SD 카드와 동일한 단계를 따르면 됩니다.

eMMC 모듈용 USB 어댑터가 없다면, 부팅 가능한 마이크로 SD 카드를 사용하여 칼리 이미지를 eMMC에 기록할 수 있습니다. 지침은 마이크로 SD 카드와 유사하며, 위와 마찬가지로 올바른 장치를 사용하고 있는지 확인해야 합니다. 사용하려는 장치를 확인하는 가장 쉬운 방법은 /dev에서 `mmcblkX` 장치를 확인하는 것입니다. `boot0`와 `boot1`이 있는 장치가 eMMC입니다. 예를 들어, `/dev/mmcblk1boot0`가 존재한다면 `/dev/mmcblk1`을 장치로 사용해야 한다는 의미입니다. 한 가지 중요한 차이점은 위에서 `sdX`를 사용할 때와 달리 장치의 번호를 **반드시** 포함해야 한다는 것입니다.

{{% notice info %}}
이 과정은 eMMC 모듈의 모든 데이터를 지웁니다. 잘못된 저장 장치를 선택하면 마이크로 SD 카드가 지워질 수 있습니다.
{{% /notice %}}

```console
$ xzcat kali-linux-2025.4-pinebook-pro-arm64.img.xz | sudo dd of=/dev/mmcblk1 bs=4M status=progress
```

이 과정은 PC, eMMC 속도 및 칼리 리눅스 이미지 크기에 따라 시간이 소요될 수 있습니다.

_dd_ 작업이 완료되면, eMMC가 설치된 상태로 파인북 프로를 부팅하세요.

[칼리에 로그인](/docs/introduction/default-credentials/)할 수 있어야 합니다.

## 파인북 프로용 칼리 - 이미지 사용자 정의

칼리 파인북 프로 이미지를 사용자 정의하려면, 설치되는 [패키지](/docs/general-use/metapackages/) 변경, [데스크톱 환경](/docs/general-use/switching-desktop-environments/) 변경, 이미지 파일 크기 증가 또는 감소 등의 작업을 하고 싶다면, GitLab의 [칼리-ARM 빌드-스크립트](https://gitlab.com/kalilinux/build-scripts/kali-arm) 저장소를 확인하고 _README.md_ 파일의 지침을 따르세요. 사용할 스크립트는 `pinebook-pro.sh`입니다.
