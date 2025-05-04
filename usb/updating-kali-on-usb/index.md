---
title: USB에서 칼리 리눅스 업데이트하기
description:
icon:
weight: 75
author: ["gamb1t",]
번역: ["xenix4845"]
---

### 요구 사항

USB에서 칼리 리눅스를 적절하게 [업데이트](/docs/general-use/updating-kali/)하려면 [영구 저장소](/docs/usb/usb-persistence/)가 설정되어 있어야 해요. 영구 저장소가 설정되어 있지 않다면, [주간](https://cdimage.kali.org/kali-images/kali-weekly/) 빌드의 ISO로 USB를 다시 이미징하는 것이 적절한 업데이트가 될 거예요.

### 과정

USB에서 칼리를 업데이트하는 가장 좋은 방법은 전체 설치에서와 동일한 방식을 따르는 거예요.

먼저 `etc/apt/sources.list`가 올바르게 채워져 있는지 확인하세요:

```console
kali@kali:~$ cat /etc/apt/sources.list
deb http://http.kali.org/kali kali-rolling main contrib non-free non-free-firmware
kali@kali:~$
```

그런 다음 다음 명령을 실행하면 [최신 칼리 버전](/docs/general-use/updating-kali/)으로 업그레이드됩니다:

```console
kali@kali:~$ sudo apt update
kali@kali:~$
kali@kali:~$ sudo apt full-upgrade -y
kali@kali:~$
```

하지만 이 방법으로는 커널이 업데이트되지 않아요. 만약 커널을 업그레이드해야 한다면, 보안 취약점이 패치되었을 수도 있으니 최신 ISO로 다시 쓰기가 필요해요. 저장해야 할 데이터가 있다면, 별도의 저장 장치에 rsync로 백업하는 것이 좋은 방법이에요.
