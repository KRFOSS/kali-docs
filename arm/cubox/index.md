---
title: 큐박스
description:
icon:
weight:
author: ["steev",]
번역: ["xenix4845"]
---

[큐박스](https://www.solid-run.com/product/cubox-carrier-base/)는 저사양, 저비용 ARM 컴퓨터입니다. 그리 인상적이지 않은 스펙에도 불구하고, 그 저렴한 가격은 작은 Linux 시스템으로 사용하기에 탁월한 선택이며 단순히 미디어 PC 역할 이상의 많은 기능을 수행할 수 있습니다.

_이 이미지는 "Freescale" 기반이 아닌 원본 "Marvell" 기반용입니다._

기본적으로 칼리 리눅스 큐박스 이미지는 다른 칼리 플랫폼에서 흔히 볼 수 있는 [**kali-linux-default** 메타패키지](/docs/general-use/metapackages/)를 **포함하지 않습니다**. 추가 도구를 설치하려면 [메타패키지 페이지](/docs/general-use/metapackages/)를 참조하시기 바랍니다.

{{% notice info %}}
큐박스용 빌드 스크립트는 새로운 방식으로 변환되지 않았기 때문에 빌드가 실패할 수 있습니다. 이 보드용으로 빌드할 계획이 있다면, 스크립트를 새로운 방식으로 업데이트하여 머지 리퀘스트로 제출하는 것을 고려해 주세요.
{{% /notice %}}

## 큐박스용 칼리 - 빌드 스크립트 지침

칼리는 미리 빌드된 이미지를 제공하지 않지만, 깃랩에서 [칼리-ARM 빌드 스크립트](https://gitlab.com/kalilinux/build-scripts/kali-arm) 저장소를 복제하고 _README.md_ 파일의 지침을 따라 직접 생성할 수 있습니다. 사용할 스크립트는 `cubox.sh`입니다.

빌드 스크립트가 실행을 완료하면, 스크립트를 실행한 디렉토리에 "img" 파일이 생성됩니다. 이 시점부터는 미리 빌드된 이미지를 다운로드한 것과 동일한 방식으로 진행하면 됩니다.

이미지를 생성하는 가장 쉬운 방법은 **이미 설치된 칼리 리눅스 환경 내에서** 작업하는 것입니다.

## 큐박스용 칼리 - 사용자 지침

큐박스에 칼리를 설치하려면 다음 지침을 따르세요:

1. 최소 16GB 용량의 빠른 마이크로 SD 카드를 준비하세요. Class 10 카드를 강력히 권장합니다.
2. **[dd](https://manpages.debian.org/testing/coreutils/dd.1.en.html)** 유틸리티를 사용하여 이 파일을 마이크로 SD 카드에 이미징하세요 ([칼리 USB 만들기](/docs/usb/live-usb-install-with-windows/)와 동일한 과정).

아래 예시에서는 저장 장치가 `/dev/sdX`에 위치한다고 가정합니다. 이 값을 단순히 복사하지 마시고, **올바른 드라이브 경로로 변경하세요**.

{{% notice info %}}
이 과정은 마이크로 SD 카드의 모든 데이터를 지웁니다. 잘못된 저장 장치를 선택하면 컴퓨터의 하드 디스크가 지워질 수 있습니다.
{{% /notice %}}

```console
$ xzcat kali-linux-2025.4-cubox-armhf.img.xz | sudo dd of=/dev/sdX bs=4M status=progress
```

이 과정은 PC, 마이크로 SD 카드 속도 및 칼리 리눅스 이미지 크기에 따라 시간이 소요될 수 있습니다.

_dd_ 작업이 완료되면 마이크로 SD 카드를 삽입한 상태로 큐박스를 부팅하세요.

[칼리에 로그인](/docs/introduction/default-credentials/)할 수 있어야 합니다.
