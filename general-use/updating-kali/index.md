---
title: Kali 업데이트하기
description:
icon:
weight:
author: ["gamb1t",]
번역: ["xenix4845"]
---

# Kali를 언제 업데이트해야 하나요?

Kali를 기본 설치한 경우, 몇 주마다 업데이트를 확인해야 해요. 도구의 새 버전이 필요하거나 보안 업데이트에 대해 들었다면, 일정을 앞당길 수 있어요. 그러나 좋은 방법은 작업 전에 모든 도구가 작동하는지 확인하고, 그 작업 중에는 업데이트하지 않는 것이에요. Kali는 롤링 릴리스이기 때문에, 가끔 문제가 롤링에 숨어들어 필요한 도구가 작동하지 않을 수 있어요.

[last-snapshot](/docs/general-use/kali-branches/)을 사용하는 경우, 해당 연도의 다음 Kali 버전을 출시할 때까지 업데이트를 받지 않아요. 이러한 이유로 [Kali 트위터](https://twitter.com/kalilinux)를 팔로우하거나 몇 달마다 [Kali 웹사이트](/)를 확인하는 것이 좋아요. Kali는 일 년에 네 번 출시되며, 대략적인 분기별 일정을 따라요.

# Kali를 어떻게 업데이트하나요?

Kali를 업데이트하려면, 먼저 `/etc/apt/sources.list`가 올바르게 설정되어 있는지 확인하세요:

```console
kali@kali:~$ cat /etc/apt/sources.list
# See https://www.kali.org/docs/general-use/kali-linux-sources-list-repositories/
deb http://http.kali.org/kali kali-rolling main contrib non-free non-free-firmware

# Additional line for source packages
# deb-src http://http.kali.org/kali kali-rolling main contrib non-free non-free-firmware
kali@kali:~$
```

그 후 다음 명령어를 실행하여 [최신 Kali 버전으로 업그레이드](/docs/general-use/updating-kali/)할 수 있어요:

```console
kali@kali:~$ sudo apt update
kali@kali:~$
kali@kali:~$ sudo apt full-upgrade -y
kali@kali:~$
```
