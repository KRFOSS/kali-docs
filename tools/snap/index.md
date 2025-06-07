---
title: 칼리 리눅스에 snap 설치하기
description:
icon:
weight:
author: ["gad3r",]
번역: ["xjnkr",]
---

#### 설치 과정
칼리 리눅스에선, `snap` 은 다음을 통해 설치할수 있습니다:

```console
kali@kali:~$ sudo apt update
kali@kali:~$
kali@kali:~$ sudo apt install -y snapd
kali@kali:~$
```

`snapd` 와 `snapd.apparmor` 서비스를 활성화하고 실행합니다:

```console
kali@kali:~$ sudo systemctl enable --now snapd apparmor
```

snap의 경로가 올바르게 업데이트되도록 하려면, 시스템에서 로그아웃후 다시 로그인하거나 시스템을 재시작 하세요.

###### 참고

- [Installing snap on Kali Linux](https://snapcraft.io/docs/installing-snap-on-kali)
