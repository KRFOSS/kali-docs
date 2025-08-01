---
title: 오드로이드-C0/C1/C1+
description:
icon:
weight:
author: ["steev",]
번역: ["xenix4845"]
---

[오드로이드-C1](https://www.hardkernel.com/shop/odroid-c1-2/)은 쿼드 코어 1.5GHz Cortex A5 프로세서와 1GB RAM을 탑재한 개발 보드입니다. 칼리 리눅스는 외장 microSD 카드 또는 eMMC 모듈에 설치할 수 있습니다.

오드로이드-C0와 오드로이드-C1+는 기본적으로 동일한 기본 하드웨어에 주변장치만 변경된 제품이므로, 세 가지 장치 모두 같은 이미지를 사용할 수 있습니다.

기본적으로 칼리 리눅스 오드로이드-C 이미지는 다른 대부분의 플랫폼과 유사하게 [**kali-linux-default** 메타패키지](/docs/general-use/metapackages/)를 포함합니다. 추가 도구를 설치하려면 [메타패키지 페이지](/docs/general-use/metapackages/)를 참조하세요.

{{% notice info %}}
오드로이드-C0/C1/C1+용 빌드 스크립트는 새로운 방식으로 변환되지 않았기 때문에 빌드가 실패할 수 있습니다. 이 보드용으로 빌드를 계획하고 있다면, 스크립트를 새로운 방식으로 업데이트하여 병합 요청으로 제출하는 것을 고려해 주세요.
{{% /notice %}}

## 오드로이드-C0/C1/C1+용 칼리 - 빌드 스크립트 지침

칼리는 사전 빌드된 이미지를 다운로드용으로 제공하지 않지만, GitLab에서 [칼리-ARM 빌드 스크립트](https://gitlab.com/kalilinux/build-scripts/kali-arm) 저장소를 클론하여 _README.md_ 파일의 지침에 따라 직접 이미지를 생성할 수 있습니다. 사용할 스크립트는 `odroid-c.sh`입니다.

빌드 스크립트 실행이 완료되면 스크립트를 실행한 디렉토리에 "img" 파일이 생성됩니다. 이 시점부터는 사전 빌드된 이미지를 다운로드한 경우와 동일한 방식으로 진행하면 됩니다.

이러한 이미지를 생성하는 가장 쉬운 방법은 **기존 칼리 리눅스 환경 내에서** 작업하는 것입니다.

## 오드로이드-C0/C1/C1+용 칼리 - 사용자 지침

오드로이드-C0/C1/C1+에 칼리를 설치하려면 다음 지침을 따르세요:

1. 최소 16GB 용량의 빠른 microSD 카드 또는 eMMC 모듈을 준비하세요. Class 10 카드를 강력히 권장합니다.
2. **[dd](https://manpages.debian.org/testing/coreutils/dd.1.en.html)** 유틸리티를 사용하여 이 파일을 microSD 카드에 이미징하세요 ([칼리 USB 만들기](/docs/usb/live-usb-install-with-windows/)와 동일한 과정).

아래 예시에서는 저장 장치가 `/dev/sdX`에 위치한다고 가정합니다. 이 값을 단순히 복사하지 마시고, **올바른 드라이브 경로로 변경하세요**.

{{% notice info %}}
이 과정은 microSD 카드 또는 eMMC의 모든 데이터를 지웁니다. 잘못된 저장 장치를 선택하면 컴퓨터의 하드 디스크가 지워질 수 있습니다.
{{% /notice %}}

```console
$ xzcat kali-linux-2025.2-odroid-c-armhf.img.xz | sudo dd of=/dev/sdX bs=4M status=progress
```

이 과정은 PC, microSD 카드 또는 eMMC의 속도, 그리고 칼리 리눅스 이미지 크기에 따라 시간이 소요될 수 있습니다.

_dd_ 작업이 완료되면, microSD 카드 또는 eMMC를 꽂은 상태로 오드로이드-C0/C1/C1+를 부팅하세요.

[칼리에 로그인](/docs/introduction/default-credentials/)할 수 있어야 합니다.
