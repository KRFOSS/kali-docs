---
title: 트림슬라이스
description:
icon:
weight:
author: ["steev",]
번역: ["xenix4845"]
---

[트림슬라이스](http://www.compulab.co.il/utilite-computer/web/trim-slice)는 듀얼 코어 1GHz 프로세서와 1GB RAM을 갖춘 디바이스입니다. 칼리 리눅스는 외장 microSD 카드에 설치됩니다.

기본적으로 칼리 리눅스 Trimslice 이미지는 다른 플랫폼과 유사하게 [**kali-linux-default** 메타패키지](/docs/general-use/metapackages/)를 포함하고 있습니다. 추가 도구를 설치하려면 [메타패키지 페이지](/docs/general-use/metapackages/)를 참조하세요.

{{% notice info %}}
트림슬라이스용 빌드 스크립트는 새로운 스타일로 변환되지 않았기 때문에 빌드가 실패할 수 있습니다. 이 보드용 빌드를 계획하고 있다면, 스크립트를 새 방식으로 업데이트하여 병합 요청으로 제출해주세요.
{{% /notice %}}

## 트림슬라이스용 칼리 - 빌드 스크립트 지침

칼리에서는 미리 빌드된 이미지를 제공하지 않지만, GitLab에서 [칼리-ARM 빌드 스크립트](https://gitlab.com/kalilinux/build-scripts/kali-arm) 저장소를 복제하고 _README.md_ 파일의 지침을 따라 이미지를 생성할 수 있습니다. 사용해야 할 스크립트는 `trimslice.sh`입니다.

빌드 스크립트 실행이 완료되면 스크립트를 실행한 디렉토리의 `images` 폴더에 "img.xz" 파일이 생성됩니다. 이제부터는 미리 빌드된 이미지를 다운로드한 것과 동일한 지침을 따르면 됩니다.

이러한 이미지를 생성하는 가장 쉬운 방법은 **기존 칼리 리눅스 환경 내에서** 작업하는 것입니다.

## 트림슬라이스용 칼리 - 사용자 지침

트림슬라이스에 칼리를 설치하려면 다음 지침을 따르세요:

1. 최소 16GB 용량의 빠른 microSD 카드를 준비하세요. Class 10 카드를 적극 권장합니다.
2. **[dd](https://manpages.debian.org/testing/coreutils/dd.1.en.html)** 유틸리티를 사용하여 이 파일을 microSD 카드에 쓰세요([칼리 USB 만들기](/docs/usb/live-usb-install-with-windows/)와 동일한 과정).

예시에서는 저장 장치가 `/dev/sdX`에 있다고 가정합니다. 이 값을 그대로 복사하지 **마시고**, **올바른 드라이브 경로로 변경**하세요.

{{% notice info %}}
이 과정은 microSD 카드의 모든 데이터를 지웁니다. 잘못된 저장 장치를 선택하면 컴퓨터의 하드 디스크가 지워질 수 있습니다.
{{% /notice %}}

```console
$ xzcat images/kali-linux-2025.3-trimslice-armhf.img.xz | sudo dd of=/dev/sdX bs=4M status=progress
```

이 과정은 PC 성능, microSD 카드 속도 및 칼리 리눅스 이미지 크기에 따라 시간이 걸릴 수 있습니다.

_dd_ 작업이 완료되면 microSD 카드를 삽입한 상태로 Banana Pro를 부팅하세요.

이제 [칼리에 로그인](/docs/introduction/default-credentials/)할 수 있습니다.
