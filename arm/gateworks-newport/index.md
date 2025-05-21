---
title: 게이트웍스 뉴포트
description:
icon:
weight:
author: ["steev",]
번역: ["xenix4845"]
---

[게이트웍스 뉴포트](https://www.gateworks.com/products/industrial-single-board-computers/octeon-tx-single-board-computers-gateworks-newport/)는 플래시 드라이브 크기의 컴퓨터를 구현한 제품입니다. 칼리 리눅스는 microSD 카드에 설치할 수 있습니다.

_이 이미지는 "Cavium OcteonTX" 기반 보드용입니다._

기본적으로 칼리 리눅스 게이트웍스 뉴포트 이미지는 다른 대부분의 플랫폼과 마찬가지로 [**kali-linux-default** 메타패키지](/docs/general-use/metapackages/)를 포함하고 있습니다. 추가 도구를 설치하고자 하신다면 [메타패키지 페이지](/docs/general-use/metapackages/)를 참조해 주시기 바랍니다.

## 게이트웍스 뉴포트용 칼리 - 사용자 안내서

[칼리 리눅스 이미지 다운로드 및 검증](/docs/introduction/download-official-kali-linux-images/)이나 [해당 이미지로 부팅 가능한 장치 생성](/docs/usb/live-usb-install-with-windows/)에 익숙하지 않으시다면, 이러한 주제에 대해 더 자세히 설명한 전문 문서를 참조하시길 강력히 권장합니다.

뉴포트에 표준 칼리 리눅스의 사전 빌드된 이미지를 설치하려면 다음 지침을 따르세요:

1. 최소 16GB 용량의 고속 microSD 카드를 준비하세요. Class 10 카드를 강력히 권장합니다.
2. [다운로드](/get-kali/) 영역에서 `칼리 뉴포트` 이미지를 다운로드하고 검증하세요. 이미지 검증 과정은 [칼리 리눅스 다운로드](/docs/introduction/download-official-kali-linux-images/)에 더 자세히 설명되어 있습니다.
3. **[dd](https://manpages.debian.org/testing/coreutils/dd.1.en.html)** 유틸리티를 사용하여 이 파일을 microSD 카드에 기록하세요. 이 예시에서는 `/dev/sdX`에 위치한 microSD를 사용합니다. **_필요에 따라 이 경로를 변경하세요._**

{{% notice info %}}
이 과정은 microSD 카드의 모든 데이터를 삭제합니다. 잘못된 저장 장치를 선택하면 컴퓨터의 하드 디스크가 완전히 지워질 수 있습니다.
{{% /notice %}}

```console
$ xzcat kali-linux-2025.1-gateworks-newport-arm64.img.xz | sudo dd of=/dev/sdX bs=4M status=progress
```

이 작업은 PC 성능, microSD 카드의 속도, 그리고 칼리 리눅스 이미지 크기에 따라 시간이 소요될 수 있습니다.

_dd_ 작업이 완료되면, 게이트웍스 뉴포트가 연결된 컴퓨터를 부팅하세요.

이제 [칼리에 로그인](/docs/introduction/default-credentials/)할 수 있습니다.

## 게이트웍스 뉴포트용 칼리 - 이미지 커스터마이징

칼리 게이트웍스 뉴포트 이미지를 커스터마이징하고 싶으시다면, [패키지](/docs/general-use/metapackages/) 설치 변경, [데스크톱 환경](/docs/general-use/switching-desktop-environments/) 전환, 이미지 파일 크기 조정 또는 더 폭넓은 실험을 원하신다면, GitLab의 [칼리-ARM 빌드 스크립트](https://gitlab.com/kalilinux/build-scripts/kali-arm) 저장소를 확인하고 _README.md_ 파일의 지침을 따르세요. 사용해야 할 스크립트는 `gateworks-newport.sh`입니다.
