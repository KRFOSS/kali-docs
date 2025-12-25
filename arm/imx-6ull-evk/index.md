---
title: i.MX 6ULL EVK
description:
icon:
archived: "true"
weight:
author: ["steev",]
번역: ["xenix4845"]
---

<!-- @steev: TODO: This is a community contributed image, so we don't know that much about it, nor do we test it. -->

[i.MX 6ULL EVK](https://www.nxp.com/design/development-boards/i-mx-evaluation-and-development-boards/evaluation-kit-for-the-i-mx-6ull-and-6ulz-applications-processor:MCIMX6ULL-EVK)는 i.MX 6ULL 애플리케이션 프로세서 기반의 시장 중심 개발 도구입니다. 칼리 리눅스는 마이크로 SD 카드에 설치할 수 있습니다.

기본적으로 칼리 리눅스 i.MX 6ULL EVK 이미지는 다른 대부분의 플랫폼과 유사하게 [**kali-linux-default** 메타패키지](/docs/general-use/metapackages/)를 포함합니다. 추가 도구를 설치하려면 [메타패키지 페이지](/docs/general-use/metapackages/)를 참조하세요.

## i.MX 6ULL EVK용 칼리 - 빌드 스크립트 지침

칼리는 사전 빌드된 이미지를 다운로드용으로 제공하지 않지만, 깃랩에서 [칼리-ARM 빌드 스크립트](https://gitlab.com/kalilinux/build-scripts/kali-arm) 저장소를 클론하여 _README.md_ 파일의 지침에 따라 직접 이미지를 생성할 수 있습니다. 사용할 스크립트는 `imx-6ull-evk.sh`입니다.

빌드 스크립트 실행이 완료되면 스크립트를 실행한 디렉토리에 "img" 파일이 생성됩니다. 이후 과정은 사전 빌드된 이미지를 다운로드한 경우와 동일합니다.

이러한 이미지를 생성하는 가장 쉬운 방법은 **기존 칼리 리눅스 환경 내에서** 작업하는 것입니다.

1. 최소 16GB 용량의 빠른 마이크로 SD 카드를 준비하세요. Class 10 카드를 강력히 권장합니다.
2. **[dd](https://manpages.debian.org/testing/coreutils/dd.1.en.html)** 유틸리티를 사용하여 이 파일을 마이크로 SD 카드에 이미징하세요 ([Kali USB 만들기](/docs/usb/live-usb-install-with-windows/)와 동일한 과정).

아래 예시에서는 저장 장치가 `/dev/sdX`에 위치한다고 가정합니다. 이 값을 단순히 복사하지 마시고, **올바른 드라이브 경로로 변경하세요**.

{{% notice info %}}
이 과정은 마이크로 SD 카드의 모든 데이터를 지웁니다. 잘못된 저장 장치를 선택하면 컴퓨터의 하드 디스크가 지워질 수 있습니다.
{{% /notice %}}

```console
$ xzcat kali-linux-2025.4-imx-6ull-evk-armhf.img.xz | sudo dd of=/dev/sdX bs=4M status=progress
```

이 과정은 PC, 마이크로 SD 카드 및 칼리 리눅스 이미지 크기에 따라 시간이 소요될 수 있습니다.

<!-- @steev: TODO: mention the jumper settings according to the documentation once @1y gets back to me about the questions I have. -->

_dd_ 작업이 완료되면 마이크로 SD 카드를 꽂은 상태로 부팅하세요.

[칼리에 로그인](/docs/introduction/default-credentials/)할 수 있어야 합니다.
