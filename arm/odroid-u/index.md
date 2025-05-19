---
title: ODROID-U2/U3
description:
icon:
weight:
author: ["steev",]
번역: ["xenix4845"]
---

[ODROID-U2](https://www.hardkernel.com/main/products/prdt_info.php?g_code=G135341370451)는 콘솔 출력이 기본적으로 제공되지 않기 때문에 다루기 까다로운 하드웨어입니다. ODROID-U2를 구매할 때는 부팅 과정의 직렬 디버깅에 사용되는 USB UART 케이블도 함께 구매하는 것이 이상적입니다.

{{% notice info %}}
ODROID-U2와 ODROID-U3 하드웨어는 동일한 기본 플랫폼을 기반으로 하므로, U2 이미지는 수정 없이 U3에서도 작동합니다.
{{% /notice %}}

기본적으로 칼리 리눅스 ODROID-U2/U3 이미지는 다른 Kali 플랫폼에서 흔히 볼 수 있는 [**kali-linux-default** 메타패키지](/docs/general-use/metapackages/)를 **포함하지 않습니다**. 추가 도구를 설치하려면 [메타패키지 페이지](/docs/general-use/metapackages/)를 참조하세요.

{{% notice info %}}
ODROID-U2/U3용 빌드 스크립트는 새로운 방식으로 변환되지 않았기 때문에 빌드가 실패할 수 있습니다. 이 보드용으로 빌드를 계획하고 있다면, 스크립트를 새로운 방식으로 업데이트하여 병합 요청으로 제출하는 것을 고려해 주세요.
{{% /notice %}}

## ODROID-U2/U3용 Kali - 빌드 스크립트 지침

Kali는 사전 빌드된 이미지를 다운로드용으로 제공하지 않지만, GitLab에서 [Kali-ARM 빌드 스크립트](https://gitlab.com/kalilinux/build-scripts/kali-arm) 저장소를 클론하여 _README.md_ 파일의 지침에 따라 직접 이미지를 생성할 수 있습니다. 사용할 스크립트는 `odroid-u.sh`입니다.

빌드 스크립트 실행이 완료되면 스크립트를 실행한 디렉토리에 "img" 파일이 생성됩니다. 이 시점부터는 사전 빌드된 이미지를 다운로드한 경우와 동일한 방식으로 진행하면 됩니다.

이러한 이미지를 생성하는 가장 쉬운 방법은 **기존 칼리 리눅스 환경 내에서** 작업하는 것입니다.

## ODROID-U2/U3용 Kali - 사용자 지침

ODROID-U2/U3에 Kali를 설치하려면 다음 지침을 따르세요:

1. 최소 16GB 용량의 빠른 microSD 카드를 준비하세요. Class 10 카드를 강력히 권장합니다.
2. **[dd](https://manpages.debian.org/testing/coreutils/dd.1.en.html)** 유틸리티를 사용하여 이 파일을 microSD 카드에 이미징하세요 ([Kali USB 만들기](/docs/usb/live-usb-install-with-windows/)와 동일한 과정).

아래 예시에서는 저장 장치가 `/dev/sdX`에 위치한다고 가정합니다. 이 값을 단순히 복사하지 마시고, **올바른 드라이브 경로로 변경하세요**.

{{% notice info %}}
이 과정은 microSD 카드의 모든 데이터를 지웁니다. 잘못된 저장 장치를 선택하면 컴퓨터의 하드 디스크가 지워질 수 있습니다.
{{% /notice %}}

```console
$ xzcat kali-linux-2025.1-odroid-u-armhf.img.xz | sudo dd of=/dev/sdX bs=4M status=progress
```

이 과정은 PC, microSD 카드 속도 및 칼리 리눅스 이미지 크기에 따라 시간이 소요될 수 있습니다.

_dd_ 작업이 완료되면, microSD 카드를 꽂은 상태로 ODROID-U2/U3를 부팅하세요.

[Kali에 로그인](/docs/introduction/default-credentials/)할 수 있어야 합니다.

### 문제 해결

ODROID 부팅 과정을 문제 해결하려면 UART 직렬 케이블을 ODROID에 연결해야 합니다. 케이블이 연결되면 다음 명령을 실행하여 콘솔에 연결할 수 있습니다:

```console
$ screen /dev/ttySAC1 115200
```
