---
title: Acer Tegra Chromebook 13" (Nyan)
description:
icon:
weight:
author: ["steev",]
번역: ["xenix4845"]
---

에이서 테그라 크롬북은 초경량 노트북입니다. 테그라 K1 2.1GHz 쿼드 코어 프로세서와 4GB RAM을 탑재하여 빠른 성능을 자랑하는 ARM 노트북입니다. 칼리 리눅스는 이 기기의 외장 풀사이즈 SD 카드에 설치할 수 있어 내장 디스크를 건드리지 않고도 사용 가능합니다.

기본적으로 칼리 리눅스 에이서 테그라 크롬북 13" 이미지에는 다른 플랫폼과 마찬가지로 [**kali-linux-default** 메타패키지](/docs/general-use/metapackages/)가 포함되어 있습니다. 추가 도구를 설치하고 싶으시다면 [메타패키지 페이지](/docs/general-use/metapackages/)를 참고하세요.

{{% notice info %}}
Nyan 기반 크롬북용 빌드 스크립트는 아직 새로운 방식으로 변환되지 않아 빌드가 실패할 수 있습니다. 이 보드용으로 빌드를 계획하고 계시다면, 스크립트를 새로운 방식으로 업데이트하여 병합 요청으로 제출해 주시면 감사하겠습니다.
{{% /notice %}}

## 에이서 테그라 크롬북용 칼리 - 빌드 스크립트 안내

칼리에서는 미리 빌드된 이미지를 다운로드 형태로 제공하지 않지만, [칼리-ARM 빌드 스크립트](https://gitlab.com/kalilinux/build-scripts/kali-arm) GitLab 저장소를 클론하여 _README.md_ 파일의 지침에 따라 직접 이미지를 생성할 수 있습니다. 사용해야 할 스크립트는 `chromebook-nyan.sh`입니다.

빌드 스크립트 실행이 완료되면 스크립트를 실행한 디렉토리에 "img" 파일이 생성됩니다. 이 시점부터는 미리 빌드된 이미지를 다운로드한 경우와 동일한 방식으로 진행하면 됩니다.

이러한 이미지를 생성하는 가장 효과적인 방법은 **기존의 칼리 리눅스 환경 내에서** 작업하는 것입니다.

## 에이서 테그라 크롬북용 칼리 - 사용자 가이드

에이서 테그라 크롬북 13"에 칼리를 설치하려면 다음 지침을 따르세요:

1. 최소 16GB 용량의 고속 풀사이즈 SD 카드나 USB 드라이브를 준비하세요. Class 10 카드를 강력히 추천합니다.
2. [크롬북을 개발자 모드로 전환](http://www.chromium.org/chromium-os/developer-information-for-chrome-os-devices/acer-c720-chromebook)하고, USB 부팅을 활성화하세요.
3. **[dd](https://manpages.debian.org/testing/coreutils/dd.1.en.html)** 유틸리티를 사용하여 이 파일을 풀사이즈 SD 카드에 기록하세요([칼리 USB 만들기](/docs/usb/live-usb-install-with-windows/)와 동일한 과정).

예시에서는 저장 장치가 `/dev/sdX`에 위치한다고 가정합니다. 이 값을 그대로 복사하지 말고, **실제 드라이브 경로로 변경하세요**.

{{% notice info %}}
이 과정은 풀사이즈 SD 카드나 USB 드라이브의 모든 내용을 지웁니다. 잘못된 저장 장치를 선택하면 컴퓨터의 하드 디스크가 완전히 지워질 수 있습니다.
{{% /notice %}}

```console
$ xzcat kali-linux-2025.1-chromebook-nyan-armhf.img.xz | sudo dd of=/dev/sdX bs=4M status=progress
```

이 과정은 PC 성능, 풀사이즈 SD 카드나 USB 드라이브의 속도, 칼리 리눅스 이미지 크기에 따라 시간이 소요될 수 있습니다.

_dd_ 작업이 완료되면, 풀사이즈 SD 카드나 USB 드라이브를 꽂은 상태로 에이서 테그라 크롬북을 부팅하고, 30초 타임아웃 전에 **CTRL+U**를 누르세요.

이제 [칼리에 로그인](/docs/introduction/default-credentials/)할 수 있을 것입니다.
