---
title: ARM 기기에서 x86 코드 실행하기
description:
icon:
author: ["gamb1t",]
번역: ["xenix4845"]
---

{{% notice info %}}

현재 qemu-user-static 패키지의 최신 버전에서 일부 프로그램이 제대로 작동하지 않는 문제가 있습니다. Kali에서 패치된 버전을 배포했지만, 이 패키지는 현재 자주 수정되고 있습니다. 이 문서의 방법이 제대로 작동하지 않는다면, 아래 목록된 패키지를 다운로드하여 다음과 같이 설치하세요:

```console
kali@kali:~$ wget https://snapshot.debian.org/archive/debian/20240509T024809Z/pool/main/q/qemu/qemu-user-static_8.2.3%2Bds-2_arm64.deb
kali@kali:~$ 
kali@kali:~$ sudo apt install ./qemu-user-static_8.2.3+ds-2_arm64.deb
kali@kali:~$ 
```

{{% /notice %}}

x86 코드를 실행하기 위해 [qemu-user-static](https://wiki.debian.org/QemuUserEmulation)을 활용할 거예요.

#### 필요한 패키지 설치하기

```console
kali@kali:~$ sudo apt update
kali@kali:~$
kali@kali:~$ sudo apt install -y qemu-user-static binfmt-support
kali@kali:~$
kali@kali:~$ sudo dpkg --add-architecture amd64
kali@kali:~$
kali@kali:~$ sudo apt update
kali@kali:~$
kali@kali:~$ sudo apt install libc6:amd64
kali@kali:~$
```

실행하려는 패키지에 따라 더 많은 라이브러리를 설치해야 할 수도 있습니다. 일부는 패키지 설치 시 자동으로 설치되지만, 다른 경우에는 무엇이 부족한지 찾아봐야 할 수도 있어요.

#### x86 코드 실행하기

```console
# qemu-user-static 설치 전
kali@kali:~$ sudo dpkg --add-architecture amd64
kali@kali:~$
kali@kali:~$ sudo apt install -y powershell
kali@kali:~$
kali@kali:~$ file /opt/microsoft/powershell/7/pwsh
/opt/microsoft/powershell/7/pwsh: ELF 64-bit LSB pie executable, x86-64, version 1 (GNU/Linux), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, for GNU/Linux 2.6.32, BuildID[sha1]=9c3feab2531f770c71d023f031faf37758181701, stripped
kali@kali:~$
kali@kali:~$ pwsh
zsh: exec format error: pwsh
kali@kali:~$
# qemu-user-static 설치 후
kali@kali:~$ sudo apt install -y qemu-user-static binfmt-support
kali@kali:~$
kali@kali:~$ pwsh
PowerShell 7.1.3
Copyright (c) Microsoft Corporation.

https://aka.ms/powershell
도움말이 필요하면 'help'를 입력하세요.


┌──(kali㉿kali)-[/home/kali]
└─PS>
```

다운로드한 x86 바이너리가 `qemu-user-static` 아래서 자동으로 실행되지 않는다면, 다음 명령으로 실행할 수 있습니다:

```console
kali@kali:~$ qemu-x86_64-static 내_x86_코드
kali@kali:~$
```
