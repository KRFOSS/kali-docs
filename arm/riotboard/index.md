---
title: RIoTboard
description:
icon:
weight:
author: ["steev",]
번역: ["xenix4845"]
---

[RIoTboard](http://riotboard.org/)는 1GB RAM을 갖춘 Cortex A9 1GHz 프로세서를 탑재하고 있습니다. Kali Linux는 외장 microSD 카드에 설치됩니다.

기본적으로 Kali Linux RIoTboard 이미지는 다른 플랫폼과 유사하게 [**kali-linux-default** 메타패키지](/docs/general-use/metapackages/)를 포함하고 있습니다. 추가 도구를 설치하려면 [메타패키지 페이지](/docs/general-use/metapackages/)를 참조하세요.

## RIoTboard용 Kali - 빌드 스크립트 지침

Kali에서는 미리 빌드된 이미지를 제공하지 않지만, GitLab에서 [Kali-ARM 빌드 스크립트](https://gitlab.com/kalilinux/build-scripts/kali-arm) 저장소를 복제하고 _README.md_ 파일의 지침을 따라 이미지를 생성할 수 있습니다. 사용해야 할 스크립트는 `riotboard.sh`입니다.

빌드 스크립트 실행이 완료되면 스크립트를 실행한 디렉토리에 "img" 파일이 생성됩니다. 이제부터는 미리 빌드된 이미지를 다운로드한 것과 동일한 지침을 따르면 됩니다.

이러한 이미지를 생성하는 가장 쉬운 방법은 **기존 Kali Linux 환경 내에서** 작업하는 것입니다.

## RIoTboard용 Kali - 사용자 지침

RIoTboard에 Kali를 설치하려면 다음 지침을 따르세요:

1. 최소 16GB 용량의 빠른 microSD 카드를 준비하세요. Class 10 카드를 적극 권장합니다.
2. **[dd](https://manpages.debian.org/testing/coreutils/dd.1.en.html)** 유틸리티를 사용하여 이 파일을 microSD 카드에 쓰세요([Kali USB 만들기](/docs/usb/live-usb-install-with-windows/)와 동일한 과정).

예시에서는 저장 장치가 `/dev/sdX`에 있다고 가정합니다. 이 값을 그대로 복사하지 **마시고**, **올바른 드라이브 경로로 변경**하세요.

{{% notice info %}}
이 과정은 microSD 카드의 모든 데이터를 지웁니다. 잘못된 저장 장치를 선택하면 컴퓨터의 하드 디스크가 지워질 수 있습니다.
{{% /notice %}}

```console
$ xzcat kali-linux-2025.1-riotboard-armhf.img.xz | sudo dd of=/dev/sdX bs=4M status=progress
```

이 과정은 PC 성능, microSD 카드 속도 및 Kali Linux 이미지 크기에 따라 시간이 걸릴 수 있습니다.

_dd_ 작업이 완료되면 microSD 카드를 삽입한 상태로 RIoTboard를 부팅하세요.

이제 [Kali에 로그인](/docs/introduction/default-credentials/)할 수 있습니다.
