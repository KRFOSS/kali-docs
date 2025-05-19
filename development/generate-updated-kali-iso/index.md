---
title: 최신 칼리 ISO 생성하기
description:
icon:
weight: 50
author: ["g0tmi1k",]
번역: ["xenix4845"]
---

칼리 리눅스에서는 데비안의 [live-build](http://live.debian.net/devel/live-build/) 스크립트를 사용해 바로 최신 ISO를 만들 수 있어요. 이런 이미지를 만드는 가장 쉬운 방법은 **이미 설치된 칼리 리눅스 환경 내에서** 작업하는 거예요.
먼저 `live-build`와 `cdebootstrap` 패키지를 설치해야 해요:

```console
kali@kali:~$ sudo apt update
kali@kali:~$ sudo apt install -y git live-build cdebootstrap
```

다음으로, 칼리의 `cdimage` Git 저장소를 다음과 같이 클론(복제)해요:

```console
kali@kali:~$ git clone git://gitlab.com/kalilinux/build-scripts/live-build-config.git
```

이제 `cdimage.kali.org` 아래의 `live` 디렉토리로 이동해서 ISO를 빌드할 수 있어요:

```console
kali@kali:~$ cd live-build-config/
kali@kali:~$ lb clean --purge
kali@kali:~$ lb config
kali@kali:~$ lb build
```

{{% notice info %}}
live build 스크립트를 사용하면 칼리 리눅스 이미지를 완전히 맞춤 설정할 수 있어요. 칼리 live build 스크립트에 대한 자세한 내용은 <a href=/docs/development/live-build-a-custom-kali-iso/>칼리 맞춤화 페이지</a>를 확인해 보세요.
{{% /notice %}}
