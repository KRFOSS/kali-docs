---
title: EfikaMX
description:
icon:
archived: "true"
weight:
author: ["steev",]
번역: ["xenix4845"]
---

{{% notice info %}}
칼리 리눅스는 더 이상 EfikaMX용 사전 빌드된 이미지나 자체 이미지를 생성하기 위한 빌드 스크립트를 제공하지 않습니다.
이 하드웨어는 더 이상 지원되지 않습니다.
이 페이지는 역사적 가치를 위해 남겨두었습니다.
{{% /notice %}}

- - -

EfikaMX는 저사양, 저비용 ARM 컴퓨터입니다. 다소 빛나지 않는 사양에도 불구하고, 그 경제성으로 인해 소형 리눅스 시스템으로는 탁월한 선택입니다.

기본적으로 칼리 리눅스 EfikaMX 이미지는 다른 칼리 플랫폼에서 흔히 볼 수 있는 [**kali-linux-default** 메타패키지](/docs/general-use/metapackages/)를 **포함하지 않습니다**. 추가 도구를 설치하고자 하신다면 [메타패키지 페이지](/docs/general-use/metapackages/)를 참조해 주시기 바랍니다.

## EfikaMX용 칼리 - 사용자 안내서

[칼리 리눅스 이미지 다운로드 및 검증](/docs/introduction/download-official-kali-linux-images/)이나 [해당 이미지로 부팅 가능한 장치 생성](/docs/usb/live-usb-install-with-windows/)에 익숙하지 않으시다면, 이러한 주제에 대해 더 자세히 설명한 전문 문서를 참조하시길 강력히 권장합니다.

EfikaMX에 표준 칼리 리눅스의 사전 빌드된 이미지를 설치하려면 다음 지침을 따르세요:

1. 최소 16GB 용량의 고속 마이크로SD 카드를 준비하세요. Class 10 카드를 적극 권장합니다.
2. [다운로드](/get-kali/) 영역에서 `칼리 EfikaMX` 이미지를 다운로드하고 검증하세요. 이미지 검증 과정은 [칼리 리눅스 다운로드](/docs/introduction/download-official-kali-linux-images/)에 더 자세히 설명되어 있습니다.
3. **[dd](https://manpages.debian.org/testing/coreutils/dd.1.en.html)** 유틸리티를 사용하여 이 파일을 마이크로SD 카드에 기록하세요([칼리 USB 만들기](/docs/usb/live-usb-install-with-windows/)와 동일한 과정).

아래 예시에서는 저장 장치가 `/dev/sdX`에 위치한다고 가정합니다. 이 값을 그대로 복사하지 마시고, **반드시 실제 드라이브 경로로 변경하셔야 합니다**.

{{% notice info %}}
이 과정은 풀사이즈 SD 카드의 모든 데이터를 삭제합니다. 잘못된 저장 장치를 선택하면 컴퓨터의 하드 디스크가 완전히 지워질 수 있으니 각별히 주의하세요.
{{% /notice %}}

```console
$ dd if=kali-linux-2025.1-efikamx.img of=/dev/sdX conv=fsync bs=4M
```

이 작업은 여러분의 PC 성능, 마이크로SD 카드 속도, 그리고 칼리 리눅스 이미지 크기에 따라 다소 시간이 소요될 수 있습니다.

_dd_ 작업이 완료되면, 마이크로SD 카드를 EfikaMX에 삽입하고 전원을 켜세요.

이제 [칼리에 로그인](/docs/introduction/default-credentials/)한 후 **startx**를 실행할 수 있습니다.

이것으로 모든 과정이 완료되었습니다!

## EfikaMX용 칼리 - 이미지 커스터마이징

칼리 BeagleBone Black 이미지를 커스터마이징하고 싶으시다면, [패키지](/docs/general-use/metapackages/) 설치 변경, [데스크톱 환경](/docs/general-use/switching-desktop-environments/) 전환, 이미지 파일 크기 조정 또는 더 폭넓은 실험을 원하신다면, GitLab의 [칼리-ARM 빌드 스크립트](https://gitlab.com/kalilinux/build-scripts/kali-arm) 저장소를 확인하고 _README.md_ 파일의 지침을 따르세요. 사용해야 할 스크립트는 `efikamx.sh`입니다.
