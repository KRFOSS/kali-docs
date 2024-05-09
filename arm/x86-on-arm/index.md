---
title: Running x86 code on ARM devices
description:
icon:
author: ["gamb1t",]
---

{{% notice info %}}

There is currently a potential issue with the current version of the qemu-user-static package which can cause some programs to not perform properly. Kali has released a patched version of this package, however this is being frequently modified at the time being. If this documentation does not perform properly, download the package listed below and install it as follow:

```console
kali@kali:~$ wget https://snapshot.debian.org/archive/debian/20240509T024809Z/pool/main/q/qemu/qemu-user-static_8.2.3%2Bds-2_arm64.deb
kali@kali:~$ 
kali@kali:~$ sudo apt install ./qemu-user-static_8.2.3+ds-2_arm64.deb
kali@kali:~$ 
```

{{% /notice %}}

To run x86 code we are going to utilize [qemu-user-static](https://wiki.debian.org/QemuUserEmulation).

#### Install necessary packages

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

Please keep in mind that more libraries may need to be installed depending on what package is being ran. In some cases these libraries will be installed automatically with the package install, however in others some research must be done to learn what is missing.

#### Running x86 code

```console
# Before qemu-user-static install
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
# After qemu-user-static install
kali@kali:~$ sudo apt install -y qemu-user-static binfmt-support
kali@kali:~$
kali@kali:~$ pwsh
PowerShell 7.1.3
Copyright (c) Microsoft Corporation.

https://aka.ms/powershell
Type 'help' to get help.


┌──(kali㉿kali)-[/home/kali]
└─PS>
```

If there is a downloaded binary that is x86 that is not automatically being ran under `qemu-user-static`, you can invoke it with the following command:

```console
kali@kali:~$ qemu-x86_x64-static my_x86_code
kali@kali:~$
```
