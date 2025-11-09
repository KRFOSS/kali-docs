---
title: 나노PC-T3
description:
icon:
weight:
author: ["steev",]
번역: ["xenix4845"]
---

[나노PC-T3](http://wiki.friendlyarm.com/wiki/index.php/NanoPC-T3)는 삼성 S5P6818, 옥타 코어 Cortex™-A53(ARMv8 64비트) 프로세서와 1GB 또는 2GB DDR3 RAM을 탑재하고 있습니다. 나노PC-T3에는 8GB eMMC가 있지만 기본 칼리 설치에는 너무 작기 때문에, 외장 마이크로 SD 카드에서 실행합니다.

기본적으로 칼리 리눅스 나노PC-T3 이미지는 다른 대부분의 플랫폼과 유사하게 [**kali-linux-default** 메타패키지](/docs/general-use/metapackages/)를 포함합니다. 추가 도구를 설치하려면 [메타패키지 페이지](/docs/general-use/metapackages/)를 참조하세요.

## 나노PC-T3 마이크로 SD 카드용 칼리 - 사용자 지침

[칼리 리눅스 이미지 다운로드 및 검증](/docs/introduction/download-official-kali-linux-images/) 또는 [해당 이미지를 사용하여 부팅 가능한 장치 생성](/docs/usb/live-usb-install-with-windows/)에 익숙하지 않은 경우, 해당 주제에 대해 더 자세히 설명된 절차를 참조하는 것이 좋습니다.

NanoPC-T3에 칼리 리눅스의 사전 빌드된 표준 이미지를 설치하려면 다음 지침을 따르세요:

1. 최소 16GB 용량의 빠른 마이크로 SD 카드를 준비하세요. Class 10 카드를 강력히 권장합니다.
2. [다운로드](/get-kali/) 영역에서 `칼리 나노PC-T3` 이미지를 다운로드 _및 검증_ 하세요. 이미지 검증 과정은 [칼리 리눅스 다운로드](/docs/introduction/download-official-kali-linux-images/)에 자세히 설명되어 있습니다.
3. **[dd](https://manpages.debian.org/testing/coreutils/dd.1.en.html)** 유틸리티를 사용하여 이 파일을 마이크로 SD 카드에 이미징하세요 ([칼리 USB 만들기](/docs/usb/live-usb-install-with-windows/)와 동일한 과정).

아래 예시에서는 저장 장치가 `/dev/sdX`에 위치한다고 가정합니다. 이 값을 단순히 복사하지 마시고, **올바른 드라이브 경로로 변경하세요**.

{{% notice info %}}
이 과정은 마이크로 SD 카드의 모든 데이터를 지웁니다. 잘못된 저장 장치를 선택하면 컴퓨터의 하드 디스크가 지워질 수 있습니다.
{{% /notice %}}

```console
$ xzcat kali-linux-2025.3-nanopc-t-arm64.img.xz | sudo dd of=/dev/sdX bs=4M status=progress
```

이 과정은 PC, 마이크로 SD 카드 속도 및 칼리 리눅스 이미지 크기에 따라 시간이 소요될 수 있습니다.

_dd_ 작업이 완료되면, 마이크로 SD 카드를 꽂은 상태로 NanoPC-T3를 부팅하세요.

[칼리에 로그인](/docs/introduction/default-credentials/)할 수 있어야 합니다.

## 나노PC-T3용 칼리 - 팁

나노PC-T3는 마이크로 SD 카드가 꽂혀 있다면 먼저 이 카드에서 부팅을 시도합니다.

무선 칩셋은 Ampak AP6212로, 이는 Cypress(이전 Broadcom) 무선 카드의 리브랜딩 제품입니다. 따라서 노력을 들인다면 진취적인 사용자들은 [nexmon](https://github.com/seemoo-lab/nexmon)을 작동시킬 수 있을 것입니다.

부팅 인수/커널 명령줄을 변경하고 싶다면, `libubootenv-tool` 패키지를 설치한 다음 사용하려는 부팅 인수로 `env.conf` 파일을 생성해야 합니다. 기본값은 `console=ttySAC0,115200n8 root=/dev/mmcblk0p2 rootfstype=ext3 rootwait rw consoleblank=0 net.ifnames=0\nbootdelay 1`이며, 이후 `fw_setenv /dev/mmcblk0 -s env.conf`로 SD 카드에 기록하세요.

## 나노PC-T3용 칼리 - 이미지 사용자 정의

칼리 나노PC-T3 이미지를 사용자 정의하려면, 설치되는 [패키지](/docs/general-use/metapackages/) 변경, [데스크톱 환경](/docs/general-use/switching-desktop-environments/) 변경, 이미지 파일 크기 증가 또는 감소 등의 작업을 하고 싶다면, GitLab의 [Kali-ARM 빌드-스크립트](https://gitlab.com/kalilinux/build-scripts/kali-arm) 저장소를 확인하고 _README.md_ 파일의 지침을 따르세요. 사용할 스크립트는 `nanopc-t.sh`입니다.
