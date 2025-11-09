---
title: 큐비보드2
description:
icon:
weight:
author: ["steev",]
번역: ["xenix4845"]
---

[큐비보드2](http://cubieboard.org/model/cb2/)는 듀얼 코어 1.4GHz 프로세서와 1GB RAM을 탑재한 보드입니다. 칼리 리눅스는 외장 마이크로SD 카드에 설치하여 사용할 수 있습니다.

기본적으로 칼리 리눅스 큐비보드2 이미지에는 다른 플랫폼과 마찬가지로 [**kali-linux-default** 메타패키지](/docs/general-use/metapackages/)가 포함되어 있습니다. 추가 도구를 설치하고자 하신다면 [메타패키지 페이지](/docs/general-use/metapackages/)를 참조해 주시기 바랍니다.

## 큐비보드2용 칼리 - 빌드 스크립트 안내

칼리에서는 미리 빌드된 이미지를 다운로드용으로 제공하지 않지만, 깃랩의 [칼리-ARM 빌드 스크립트](https://gitlab.com/kalilinux/build-scripts/kali-arm) 저장소를 클론하여 _README.md_ 파일의 지침에 따라 직접 이미지를 생성하실 수 있습니다. 사용하실 스크립트는 `cubieboard2.sh`입니다.

빌드 스크립트 실행이 완료되면 스크립트를 실행한 디렉토리에 "img" 파일이 생성됩니다. 이후 과정은 미리 빌드된 이미지를 다운로드한 경우와 동일하게 진행하시면 됩니다.

이러한 이미지를 가장 효과적으로 생성하는 방법은 **기존의 칼리 리눅스 환경 내에서** 작업하는 것입니다.

## 큐비보드2용 칼리 - 사용자 안내서

큐비보드2에 칼리를 설치하시려면 다음 지침을 따라주세요:

1. 최소 16GB 용량의 고속 마이크로SD 카드를 준비하세요. Class 10 카드를 적극 권장합니다.
2. **[dd](https://manpages.debian.org/testing/coreutils/dd.1.en.html)** 유틸리티를 활용하여 이미지 파일을 마이크로SD 카드에 기록하세요([칼리 USB 만들기](/docs/usb/live-usb-install-with-windows/)와 동일한 과정입니다).

아래 예시에서는 저장 장치가 `/dev/sdX`에 위치한다고 가정합니다. 이 값을 그대로 복사하지 마시고, **반드시 실제 드라이브 경로로 변경하셔야 합니다**.

{{% notice info %}}
이 과정은 마이크로SD 카드의 모든 데이터를 삭제합니다. 잘못된 저장 장치를 선택하면 컴퓨터의 하드 디스크가 완전히 지워질 수 있으니 각별히 주의하세요.
{{% /notice %}}

```console
$ xzcat kali-linux-2025.3-cubieboard2-armhf.img.xz | sudo dd of=/dev/sdX bs=4M status=progress
```

이 작업은 여러분의 PC 성능, 마이크로SD 카드 속도, 그리고 칼리 리눅스 이미지 크기에 따라 다소 시간이 소요될 수 있습니다.

_dd_ 작업이 완료되면, 마이크로SD 카드를 삽입한 상태로 큐비보드2를 부팅하세요.

이제 [칼리에 로그인](/docs/introduction/default-credentials/)하여 강력한 보안 도구 환경을 경험하실 수 있습니다.
