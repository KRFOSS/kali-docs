---
title: SS808/MK808
description:
icon:
archived: "true"
weight:
author: ["steev",]
---

{{% notice info %}}
Kali Linux는 더 이상 미리 빌드된 이미지나 자체 이미지를 생성하기 위한 빌드 스크립트를 제공하지 않습니다.
이 하드웨어는 더 이상 지원되지 않습니다.
이 페이지는 역사적 가치를 위해 남겨두었습니다.
{{% /notice %}}

- - -

<!-- @g0tmi1k: How does MK808 come into it -->
SainSmart SS808은 다양한 형태와 종류로 제공되는 **rockchip** 기반 ARM 장치입니다. 1GB RAM과 함께 듀얼 코어 1.6 GHz A9 프로세서를 탑재하여 Kali를 매우 잘 실행합니다.

기본적으로 Kali Linux SS808 이미지는 대부분의 Kali 플랫폼에서 볼 수 있는 [**kali-linux-default** 메타패키지](/docs/general-use/metapackages/)를 **포함하지 않습니다**. 추가 도구를 설치하려면 [메타패키지 페이지](/docs/general-use/metapackages/)를 참조하세요.

## SS808용 Kali - 사용자 지침

[Kali Linux 이미지 다운로드 및 검증](/docs/introduction/download-official-kali-linux-images/) 또는 [해당 이미지로 부팅 가능한 장치 생성](/docs/usb/live-usb-install-with-windows/)에 익숙하지 않다면, 이러한 주제에 대해 더 자세히 설명된 절차를 참조하는 것이 좋습니다.

SS808에 Kali만 설치하려면 아래 지침을 따르세요:

1. 최소 8GB 용량의 빠른 microSD 카드를 준비하세요. Class 10 카드를 적극 권장합니다.
2. [다운로드](/get-kali/) 영역에서 `Kali Raspberry SS808` 이미지를 다운로드 _및 검증_하세요. 이미지 검증 과정은 [Kali Linux 다운로드](/docs/introduction/download-official-kali-linux-images/)에서 더 자세히 설명합니다.
3. **[dd](https://manpages.debian.org/testing/coreutils/dd.1.en.html)** 유틸리티를 사용하여 이 파일을 microSD 카드에 쓰세요([Kali USB 만들기](/docs/usb/live-usb-install-with-windows/)와 동일한 과정).
4. [MK808-Finless-1-6-Custom-ROM](https://forum.freaktab.com/?3207-NEW-MK808-Finless-1-6-Custom-ROM)을 Windows 머신에 다운로드하고 zip 파일을 압축 해제하세요.
5. MK808 Finless ROM 도구의 README 파일을 읽고, 필요한 Windows 드라이버를 설치하세요.
6. Finless ROM 플래시 도구를 실행하고 하단에 **Found RKAndroid Loader Rock USB**가 표시되는지 확인하세요. 목록에서 `kernel.img`와 `recovery.img`를 선택 해제하고 장치를 플래시하세요.
7. 다음으로 FInless ROM 디렉터리의 `kernel.img`와 `recovery.img`를 모두 Kali의 `kernel.img`로 덮어쓰세요.
8. Finless ROM 도구에서 `kernel.img`와 `recovery.img`만 선택되었는지 확인한 다음 장치를 다시 플래시하세요.
9. microSD 카드를 SS808에 삽입하고 부팅하세요.

예시에서는 저장 장치가 `/dev/sdX`에 있다고 가정합니다. 이 값을 그대로 복사하지 **마시고**, **올바른 드라이브 경로로 변경**하세요.

{{% notice info %}}
이 과정은 microSD 카드의 모든 데이터를 지웁니다. 잘못된 저장 장치를 선택하면 컴퓨터의 하드 디스크가 지워질 수 있습니다.
{{% /notice %}}

```console
$ dd if=kali-linux-2025.1-SS808.img of=/dev/sdX conv=fsync bs=4M
```

이 과정은 PC 성능, microSD 카드 속도 및 Kali Linux 이미지 크기에 따라 시간이 걸릴 수 있습니다.

_dd_ 작업이 완료되면 microSD 카드를 삽입한 상태로 SS808을 부팅하세요.

이제 [Kali에 로그인](/docs/introduction/default-credentials/)하고 **startx**를 실행할 수 있습니다.
