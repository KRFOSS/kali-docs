---
title: 칼리 리눅스 업데이트하기
description:
icon:
weight:
author: ["gamb1t",]
번역: ["xenix4845"]
---

## 언제 칼리 리눅스를 업데이트해야 할까요?

기본 설치된 칼리 리눅스를 사용하고 있다면, 몇 주마다 한 번씩은 업데이트를 확인해보세요. 새로운 도구 버전이 필요하거나 보안 업데이트 소식을 들으면 업데이트 주기를 앞당길 수도 있어요. 하지만 좋은 방법은 침투 테스트(engagement) 전에 모든 도구가 제대로 작동하는지 확인하고, 침투 테스트 진행 중에는 업데이트를 하지 않는 거예요. 칼리 리눅스는 롤링 릴리스(rolling release, 지속적 업데이트 방식)라서 가끔씩 문제가 생겨서 필요한 도구가 작동하지 않을 수 있거든요.

만약 [last-snapshot](/docs/general-use/kali-branches/)을 사용하고 있다면, 다음 칼리 리눅스 버전이 출시될 때까지는 업데이트를 받을 수 없어요. [블로그 게시물](/blog/)을 [뉴스레터](/newsletter/)나 [RSS](/rss.xml)로 구독하거나, [칼리 리눅스 소셜 미디어](/docs/community/list-of-official-kali-sites/#social-media-networks)를 팔로우하면 새 버전 출시 소식을 받아볼 수 있어요. 칼리 리눅스는 1년에 4번 출시되고, 대략적으로 분기별 일정을 따라요.

## 칼리 리눅스를 어떻게 업데이트하나요?

칼리 리눅스를 업데이트하려면, 먼저 `/etc/apt/sources.list` 파일이 [올바르게 설정](/docs/general-use/kali-linux-sources-list-repositories/)되어 있는지 확인해보세요:

```console
kali@kali:~$ cat /etc/apt/sources.list
# See https://www.kali.org/docs/general-use/kali-linux-sources-list-repositories/
deb http://http.kali.org/kali kali-rolling main contrib non-free non-free-firmware

# Additional line for source packages
# deb-src http://http.kali.org/kali kali-rolling main contrib non-free non-free-firmware
kali@kali:~$
```

그 다음 아래 명령어들을 실행하면 최신 칼리 리눅스 버전으로 안전하게 업그레이드할 수 있어요:

```console
kali@kali:~$ sudo apt update
[...]
kali@kali:~$
kali@kali:~$ sudo apt dist-upgrade -y
[...]
kali@kali:~$
```

위의 명령어가 작동하지 않았다면, 아래 명령어를 사용하여 최신 커널로 강제 업데이트할 수 있어요. (시스템이 손상될 수 있어 권장하지는 않아요):

```console
kali@kali:~$ sudo apt update && sudo apt full-upgrade -y
[...]
kali@kali:~$
```
