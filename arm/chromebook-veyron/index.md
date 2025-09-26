---
title: 에이수스 크롬북 플립 (Veyron)
description:
icon:
weight:
author: ["steev",]
번역: ["xenix4845"]
---

[에이수스 크롬북 플립](https://www.asus.com/us/Notebooks/ASUS_Chromebook_Flip_C100PA/)은 쿼드코어 1.8GHz 프로세서와 2GB 또는 4GB RAM을 탑재한 크롬북으로, 10.1인치 10포인트 멀티터치 터치스크린을 자랑합니다. 칼리 리눅스는 외장 마이크로SD 카드나 USB 드라이브에 완벽하게 설치할 수 있습니다.

기본적으로 칼리 리눅스 에이수스 크롬북 플립 이미지에는 다른 플랫폼과 마찬가지로 [**kali-linux-default** 메타패키지](/docs/general-use/metapackages/)가 포함되어 있습니다. 추가 도구를 설치하고 싶으시다면 [메타패키지 페이지](/docs/general-use/metapackages/)를 참고하세요.

{{% notice info %}}
Veyron 기반 크롬북용 빌드 스크립트는 아직 새로운 방식으로 변환되지 않아 빌드가 실패할 수 있습니다. 이 보드용으로 빌드를 계획 중이시라면, 스크립트를 새로운 방식으로 업데이트하여 병합 요청으로 제출해 주시면 대단히 감사하겠습니다.
{{% /notice %}}

## 에이수스 크롬북 플립용 칼리 - 빌드 스크립트 가이드

칼리에서는 미리 빌드된 이미지를 다운로드용으로 제공하지 않지만, [칼리-ARM 빌드 스크립트](https://gitlab.com/kalilinux/build-scripts/kali-arm) GitLab 저장소를 클론하고 _README.md_ 파일의 지침을 따라 직접 이미지를 생성할 수 있습니다. 사용해야 할 스크립트는 바로 `chromebook-veyron.sh`입니다.

빌드 스크립트 실행이 완료되면 스크립트를 실행한 디렉토리에 "img" 파일이 생성됩니다. 이 시점부터는 마치 미리 빌드된 이미지를 다운로드한 것처럼 동일한 방식으로 진행하시면 됩니다.

이러한 이미지를 생성하는 가장 효과적이고 빠른 방법은 **기존의 칼리 리눅스 환경 내에서** 작업하는 것입니다.

## 에이수스 크롬북 플립용 칼리 - 사용자 완벽 가이드

에이수스 크롬북 플립에 칼리를 설치하려면 다음 명쾌한 지침을 따라주세요:

1. 최소 16GB 용량의 초고속 마이크로SD 카드나 USB 드라이브를 준비하세요. Class 10 카드를 적극 추천합니다.
2. **[dd](https://manpages.debian.org/testing/coreutils/dd.1.en.html)** 유틸리티를 활용하여 이미지 파일을 마이크로SD 카드에 구워넣으세요([칼리 USB 제작하기](/docs/usb/live-usb-install-with-windows/)와 동일한 프로세스).

여기서는 저장 장치가 `/dev/sdX`에 위치한다고 가정합니다. 절대로 이 값을 그대로 복사하지 마시고, **반드시 실제 드라이브 경로로 변경하셔야 합니다**.

{{% notice info %}}
이 과정은 마이크로SD 카드나 USB 드라이브의 모든 데이터를 완전히 지웁니다. 잘못된 저장 장치를 선택하면 소중한 컴퓨터의 하드 디스크가 영원히 지워질 수 있으니 각별히 주의하세요.
{{% /notice %}}

```console
$ xzcat kali-linux-2025.3-chromebook-veyron-armhf.img.xz | sudo dd of=/dev/sdX bs=4M status=progress
```

이 과정은 여러분의 PC 성능, 마이크로SD 카드나 USB 드라이브의 속도, 그리고 칼리 리눅스 이미지 크기에 따라 다소 시간이 소요될 수 있습니다.

_dd_ 작업이 완벽하게 완료되면, 마이크로SD 카드나 USB 드라이브를 꽂은 상태로 에이수스 크롬북 플립을 부팅하고, 30초 타임아웃이 지나기 전에 재빨리 **CTRL+U**를 눌러주세요.

이제 여러분은 [칼리에 로그인](/docs/introduction/default-credentials/)하여 강력한 보안 도구의 세계로 뛰어들 준비가 되었습니다!

## 에이수스 크롬북 플립용 칼리 - 이미지 커스터마이징 완전정복

칼리 에이수스 크롬북 플립 이미지를 여러분만의 방식으로 맞춤 설정하고 싶으신가요? [패키지](/docs/general-use/metapackages/) 설치 변경, [데스크톱 환경](/docs/general-use/switching-desktop-environments/) 전환, 이미지 파일 크기 조정 또는 더 폭넓은 실험을 원하신다면, GitLab의 [칼리-ARM 빌드 스크립트](https://gitlab.com/kalilinux/build-scripts/kali-arm) 저장소를 확인하고 _README.md_ 파일의 명확한 지침을 따르세요. 사용해야 할 스크립트는 바로 `chromebook-veyron.sh`입니다.
