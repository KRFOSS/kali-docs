---
title: 유틸라이트 프로
description:
icon:
weight:
author: ["steev",]
번역: ["xenix4845"]
---

[유틸라이트 프로](http://www.compulab.co.il/utilite-computer/web/utilite-overview)는 쿼드코어 1.2GHz Cortex A9 프로세서와 2GB RAM을 탑재한 기기입니다. 칼리 리눅스는 외장 마이크로SD 카드에 설치됩니다.

기본적으로 칼리 리눅스 유틸라이트 프로 이미지는 다른 플랫폼과 마찬가지로 [**kali-linux-default** 메타패키지](/docs/general-use/metapackages/)를 포함합니다. 추가 도구를 설치하고 싶다면 [메타패키지 페이지](/docs/general-use/metapackages/)를 참고하세요.

{{% notice info %}}
유틸라이트 프로용 빌드 스크립트는 새 스타일로 변환되지 않아 빌드가 실패할 수 있습니다. 이 보드용으로 빌드를 계획 중이라면, 스크립트를 새 방식으로 업데이트해서 병합 요청으로 제출해 주세요.
{{% /notice %}}

## 유틸라이트 프로용 칼리 - 빌드 스크립트 가이드

칼리는 미리 빌드된 이미지를 제공하지 않지만, GitLab에서 [칼리-ARM 빌드 스크립트](https://gitlab.com/kalilinux/build-scripts/kali-arm) 저장소를 복제하고 _README.md_ 파일의 지침을 따라 직접 생성할 수 있습니다. 사용해야 할 스크립트는 `utilite-pro.sh`입니다.

빌드 스크립트 실행이 완료되면 스크립트를 실행한 디렉터리에 "img" 파일이 생성됩니다. 이후 과정은 미리 빌드된 이미지를 다운로드했을 때와 동일합니다.

이런 이미지를 생성하는 가장 쉬운 방법은 **기존 칼리 리눅스 환경 내에서** 작업하는 것입니다.

## 유틸라이트 프로용 칼리 - 사용자 가이드

유틸라이트 프로에 칼리를 설치하려면 다음 지침을 따르세요:

1. 최소 16GB 용량의 빠른 마이크로SD 카드를 준비하세요. Class 10 카드를 강력히 권장합니다.
2. **[dd](https://manpages.debian.org/testing/coreutils/dd.1.en.html)** 유틸리티를 사용해 이 파일을 마이크로SD 카드에 기록하세요([칼리 USB 만들기](/docs/usb/live-usb-install-with-windows/)와 동일한 과정).

예시에서는 저장 장치가 `/dev/sdX`에 있다고 가정합니다. 이 값을 그대로 복사하지 **말고**, **여러분 환경에 맞는 올바른 드라이브 경로로 변경**하세요.

{{% notice info %}}
이 과정은 마이크로SD 카드의 모든 데이터를 지웁니다. 잘못된 저장 장치를 선택하면 컴퓨터 하드 디스크가 지워질 수 있어요!
{{% /notice %}}

```console
$ xzcat kali-linux-2025.2-utilite-pro-armhf.img.xz | sudo dd of=/dev/sdX bs=4M status=progress
```

이 작업은 PC 성능, 마이크로SD 카드 속도, 칼리 리눅스 이미지 크기에 따라 시간이 걸릴 수 있습니다.

dd 작업이 완료되면 마이크로SD 카드를 연결한 상태로 유틸라이트 프로를 부팅하세요.

이제 [칼리에 로그인](/docs/introduction/default-credentials/)할 수 있습니다.
