---
title: sudo에 관한 모든 것
description:
icon:
weight: 6
author: ["gamb1t",]
번역: ["xenix4845"]
---

# 비-루트 사용자

2020.1부터 Kali는 기본적으로 특권이 있는 비-루트 사용자로 전환했어요. 이는 루트에 설정된 비밀번호가 없으며, 설치 중에 생성된 계정이 사용해야 할 계정이라는 것을 의미해요. 루트 사용자에 대한 액세스를 다시 활성화하는 것이 가능하지만, 이는 권장되지 않아요.

# Sudo?

`sudo`는 관리자 권한이 필요한 도구, 포트 또는 서비스에 액세스할 수 있는 방법이에요. 그러나 sudo는 강력하며 시스템에 대한 전체 액세스를 허용할 수 있으므로, 모든 명령어에 `sudo`를 사용하는 것은 권장되지 않아요.

### Kali에서의 Sudo

Kali는 기본적으로 관리자 권한이 있는 사용자를 생성하기 때문에, 사용자는 바로 `sudo`를 사용하고 인증을 위해 비밀번호를 제공할 수 있어요. 사용자가 비밀번호 없는 `sudo`를 활성화하고 싶다면(누군가 사용자 계정에 접근하면 보안 위험이 있음), 다음과 같은 옵션이 있어요:

```console
kali@kali:~$ sudo apt install -y kali-grant-root && sudo dpkg-reconfigure kali-grant-root
```

이전 명령어는 사용자가 `sudo`를 사용할 때 비밀번호를 제공할 필요가 없는 신뢰할 수 있는 그룹에 추가될 수 있게 하는 패키지를 설치해요. 그러나 이것이 루트가 복원된다는 의미는 아니에요.

### 사용 방법

한국어로 설치한 경우 뜨는 메시지가 달라요.

```console
kali@kali:~$ ls /root
ls: cannot open directory '/root': Permission denied
kali@kali:~$
kali@kali:~$ sudo ls /root
[sudo] password for kali:
hello
kali@kali:~$ sudo apt install -y kali-grant-root
[...]
kali@kali:~$ sudo dpkg-reconfigure kali-grant-root
[...]
kali@kali:~$ sudo ls /root
hello
kali@kali:~$
```
