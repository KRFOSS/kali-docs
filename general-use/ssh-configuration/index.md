---
title: SSH 설정
description:
icon:
weight:
author: ["arnaudr", "gamb1t",]
번역: ["xenix4845"]
---

## SSH 클라이언트: 넓은 호환성 vs 강력한 보안

[Kali Linux 2022.1](/blog/kali-linux-2022-1-release/) 출시 이후로, SSH 클라이언트를 **넓은 호환성**으로 쉽게 구성하여 Kali가 가능한 한 많은 SSH 서버와 통신할 수 있게 할 수 있어요. 넓은 호환성 모드에서는 레거시 키 교환 알고리즘 _(예: diffie-hellman-*-sha1)_ 및 오래된 암호화 방식 _(예: CBC)_ 이 **활성화**돼요. 결과적으로 Kali 내에서 사용되는 도구들은 이러한 오래된 방법을 사용하여 통신할 수 있어요. _이는 Kali가 이러한 오래된 프로토콜을 여전히 사용하고 있는 구형 SSH 서버와 통신할 수 있는 능력을 향상시키기 위해 수행돼요. 이를 사용하는 구형 서비스는 수명이 다했을 수 있으므로, 취약점이나 다른 문제를 발견할 가능성이 높아져요_.

이것이 _기본값이 아니라는_ 점에 유의하세요. 기본적으로 Kali Linux의 SSH 클라이언트는 더 안전한 채널을 통한 통신을 강제하기 위해 **강력한 보안**으로 구성되어 있어요.

이 설정은 `kali-tweaks` 도구를 사용하여 쉽게 변경할 수 있어요. 다음과 같이 하세요:

- 터미널을 열고 `kali-tweaks`를 실행하세요. 
- 그 다음, _Hardening_ 메뉴를 선택하세요.
- 이제 **강력한 보안**(Strong Security) _(기본값)_ 과 **넓은 호환성**(Wide Compatibility) 중에서 선택할 수 있어요.

_참고: 이는 구성 파일 `/etc/ssh/ssh_config.d/kali-wide-compat.conf`를 생성하거나 삭제함으로써 이루어져요._

## SSH 클라이언트: ssh1 패키지를 통한 제거된 기능 복원

소프트웨어와 보안이 발전함에 따라 OpenSSH는 더 이상 일반적으로 사용되지 않는 기능을 기본 패키지에서 제거해요. 위에서 언급한 넓은 호환성 모드는 _패키지에 존재하지만 기본적으로 비활성화된_ 기능을 활성화할 수 있어요. 그러나 정말로 너무 오래된 기능은 완전히 제거돼요. 이 시점에서는 다른 SSH 클라이언트인 `ssh1`을 사용해야 해요.

이 패키지는 실용적으로 버전 7.5(2017년 3월 출시)에서 동결된 OpenSSH예요. `ssh`, `scp`, `ssh-keygen`에 대한 바이너리를 각각 `ssh1`, `scp1`, `ssh-keygen1`이라는 이름으로 제공해요. 왜 버전 7.5인지 물을 수 있어요? 이는 **SSH v.1 프로토콜**을 지원하는 OpenSSH의 마지막 릴리스이기 때문이에요. 시간이 지남에 따라 이 오래된 버전은 버전 9.8에서 제거된 **DSA 키** 지원과 같은 다른 제거된 기능을 계속 사용하는 데 유용해졌어요.

[Kali Linux 2024.4](/blog/kali-linux-2024-4-release/) 출시 이후로, `openssh-client-ssh1` 패키지가 기본으로 설치돼요. 시스템을 업그레이드하고 이 기능이 필요한 다른 사용자의 경우 패키지를 설치하는 것이 쉬워요:

```console
kali@kali:~$ sudo apt update
[...]
kali@kali:~$
kali@kali:~$ sudo apt install -y openssh-client-ssh1
[...]
kali@kali:~$
kali@kali:~$ dpkg --listfiles openssh-client-ssh1 | grep bin/
/usr/bin/scp1
/usr/bin/ssh-keygen1
/usr/bin/ssh1
kali@kali:~$
kali@kali:~$ ssh1 -V
OpenSSH_7.5p1 Debian-17, OpenSSL 3.3.2 3 Sep 2024
kali@kali:~$
```

## SSH 클라이언트: GSS-API 지원

{{% notice info %}}
이것은 시스템을 업그레이드하여 이 기능을 잃은 Kali Linux 사용자를 위한 것이에요. 이 패키지는 2024.4부터 Kali Linux에 사전 설치되어 있어요.

2024년 9월 23일 현재, 이 패키지는 현재 변경 로그만 포함하고 있어요. 이 패키지는 OpenSSH 패키지에서 GSS-API 변경이 발생할 때를 위한 자리 표시자예요.
{{% /notice %}}

가까운 미래의 어느 시점에, GSS-API에 대한 지원은 표준 SSH 패키지의 공격 표면을 줄이기 위해 별도의 패키지로 분리될 거예요. 따라서 `openssh-client` 패키지는 GSS-API 지원 없이 제공되며, 필요한 사용자를 위해 별도의 패키지인 `openssh-client-gssapi`를 설치해야 할 거예요.

Kali rolling을 실행 중이고 GSS-API 지원이 미래에 제거되지 않도록 하고 싶은 사용자는 사전에 패키지를 설치할 수 있어요:

```console
kali@kali:~$ sudo apt update
[...]
kali@kali:~$
kali@kali:~$ sudo apt install -y openssh-client-gssapi
[...]
kali@kali:~$
```

## SSH 서버: 자동 호스트 키 생성

[Kali Linux 2022.1](/blog/kali-linux-2022-1-release/) 출시 이후로, SSH 호스트 키는 누락된 경우 자동으로 생성돼요. 이는 systemd 서비스인 `regenerate-ssh-host-keys`를 통해 이루어져요.

그렇다면 SSH 호스트 키는 정확히 무엇인가요? 이 키들은 SSH 서버가 기능을 하기 위해 필요해요. 각 기기마다 고유해야 해요. 이 키들은 `/etc/ssh`에서 찾을 수 있으며 `ssh_host_*_key`라는 이름이 붙어 있어요. 일반적으로 이렇게 보여요:

```console
kali@kali:~$ ls -l /etc/ssh/ssh_host_*
-rw------- 1 root root 1373 Feb  3 23:50 /etc/ssh/ssh_host_dsa_key
-rw-r--r-- 1 root root  599 Feb  3 23:50 /etc/ssh/ssh_host_dsa_key.pub
-rw------- 1 root root  505 Feb  3 23:50 /etc/ssh/ssh_host_ecdsa_key
-rw-r--r-- 1 root root  171 Feb  3 23:50 /etc/ssh/ssh_host_ecdsa_key.pub
-rw------- 1 root root  399 Feb  3 23:50 /etc/ssh/ssh_host_ed25519_key
-rw-r--r-- 1 root root   91 Feb  3 23:50 /etc/ssh/ssh_host_ed25519_key.pub
-rw------- 1 root root 2590 Feb  3 23:50 /etc/ssh/ssh_host_rsa_key
-rw-r--r-- 1 root root  563 Feb  3 23:50 /etc/ssh/ssh_host_rsa_key.pub
```

이 키들은 각 기기마다 고유해야 하기 때문에, Kali Linux [VM 이미지](/get-kali/#kali-virtual-machines)나 [ARM 이미지](/get-kali/#kali-arm)와 같은 사전 빌드된 Kali 이미지에 포함될 수 없어요. 일반적으로 SSH 서버를 처음 실행하기 전에 이러한 키를 생성하는 것은 사용자의 몫이에요. 그러나 SSH에 익숙하지 않은 대부분의 사용자에게는 이 기술적 세부 사항을 모르기 때문에 어려움이 있을 수 있어요.

이를 더 쉽게 하기 위해 Kali Linux는 이제 자동으로 처리하고 누락된 키를 생성하는 systemd 서비스와 함께 제공돼요. 이론적으로는 사전 빌드된 이미지의 첫 번째 부팅 중에만 서비스가 작동해요. 이후 부팅에서는 키가 이미 존재하기 때문에 아무 일도 일어나지 않아요. 이는 직접 이 키들을 제거한 사용자에게는 해당되지 않을 수 있어요.

이러한 자동 동작이 불편한 사용자의 경우, 비활성화하는 것은 매우 간단하고 명확해요:

```console
kali@kali:~$ sudo systemctl disable regenerate-ssh-host-keys.service
```
