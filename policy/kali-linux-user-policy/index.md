---
title: 칼리 리눅스 사용자 정책
description:
icon:
weight:
author: ["g0tmi1k",]
번역: ["xenix4845"]
---

{{% notice info %}}
이 문서는 칼리 리눅스에서 관리자 권한을 어떻게 사용하는지에 대한 정책을 설명해요.
{{% /notice %}}

칼리에서 권한이 필요한 명령을 실행하기 위해 두 가지 방법을 사용해요:

- pkexec (GUI 및 명령줄)
- sudo (명령줄)

또한 일부 도구는 관리자(super-user) 권한 없이 실행할 때 다르게 작동할 수 있다는 점을 알아두세요. [nmap](https://nmap.org/book/man-port-scanning-techniques.html)이 그 예시예요. 웹사이트에 나와있듯이:

> 기본적으로 Nmap은 SYN 스캔을 수행하지만, 사용자가 원시 패킷을 보낼 적절한 권한이 없는 경우(Unix에서는 루트 접근 필요) 연결 스캔으로 대체해요.

이건 이런 의미예요:

- SYN 스캔(`-sS`)은 루트 사용자의 기본값이에요. SYN 패킷만 전송하므로 더 빠르지만, 이걸 수행하려면 루트 권한이 필요한 특별한 기능이 필요해요.
- 연결 스캔(`-sT`)은 루트가 아닌 사용자의 기본값이에요. 3단계 핸드셰이크를 완료하기 때문에 SYN 스캔보다 시간이 더 오래 걸리고 더 많은 패킷을 사용해요.

칼리가 이전처럼 작동하도록 되돌리고 싶다면 다음 패키지를 설치할 수 있어요:

```console
kali@kali:~$ sudo apt update
kali@kali:~$
kali@kali:~$ sudo apt install -y kali-grant-root
kali@kali:~$
```

- - -

이 정책은 [칼리 리눅스 2020.1](https://kali.org/blog/kali-linux-2020-1-release/)부터 적용되었어요. [이전 루트 정책](/docs/policy/kali-linux-root-user-policy/)도 여기서 확인할 수 있어요.
