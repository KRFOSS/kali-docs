---
title: Installing Tor Browser on Kali Linux
description:
icon:
weight:
author: ["gad3r",]
번역: ["ryuyijun",]
---

#### 설치 방법

터미널을 열고 다음 명령어를 입력하세요:

```console
kali@kali:~$ sudo apt update
kali@kali:~$
kali@kali:~$ sudo apt install -y tor torbrowser-launcher
kali@kali:~$
```

그다음 다음 명령어를 입력하세요:

```console
kali@kali:~$ torbrowser-launcher
```

처음 명령어를 실행하면 Tor 브라우저를 다운로드하고 설치하며, 서명 검증도 포함됩니다.

그 이후부터는 Tor 브라우저를 업데이트하고 실행하는데 사용됩니다

###### 참

- [Debian Wiki: TorBrowser](https://wiki.debian.org/TorBrowser)
