---
title: Kali Linux 사용자 정책
description:
icon:
weight:
author: ["g0tmi1k",]
---

Kali에서 권한이 필요한 명령을 실행하기 위해 두 가지 방법을 사용합니다:

- pkexec (GUI 및 명령줄)
- sudo (명령줄)

또한 일부 도구는 관리자(super-user) 권한 없이 실행할 때 다르게 작동할 수 있다는 점을 유의해야 합니다. [nmap](https://nmap.org/book/man-port-scanning-techniques.html)이 그 예입니다. 웹사이트에 명시된 바와 같이:

> 기본적으로 Nmap은 SYN 스캔을 수행하지만, 사용자가 원시 패킷을 보낼 적절한 권한이 없는 경우(Unix에서 루트 액세스 필요) 연결 스캔으로 대체합니다.

이는 다음을 의미합니다:

- SYN 스캔(`-sS`)은 루트 사용자의 기본값입니다. SYN 패킷만 전송하므로 더 빠르지만, 이를 수행하기 위해 루트 권한이 필요한 특별한 기능이 요구됩니다.
- 연결 스캔(`-sT`)은 루트가 아닌 사용자의 기본값입니다. 3단계 핸드셰이크를 완료하기 때문에 SYN 스캔보다 시간이 더 오래 걸리고 더 많은 패킷을 사용합니다.

Kali가 이전처럼 작동하도록 복원하고 싶다면 다음 패키지를 설치할 수 있습니다:

```console
kali@kali:~$ sudo apt update
kali@kali:~$
kali@kali:~$ sudo apt install -y kali-grant-root
kali@kali:~$
```

- - -

이 정책은 Kali Linux 2020.1부터 적용되었습니다. 여기 [이전 루트 정책](/docs/policy/kali-linux-root-user-policy/)을 확인하실 수 있습니다.
