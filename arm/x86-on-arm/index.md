---
title: Running x86 code on ARM devices
description:
icon:
author: ["gamb1t",]
---

To run x86 code we are going to utilize [qemu-user-static](https://wiki.debian.org/QemuUserEmulation).

#### Install necessary packages

```
┌──(kali㉿kali)-[~]
└─$ sudo apt update

┌──(kali㉿kali)-[~]
└─$

┌──(kali㉿kali)-[~]
└─$ sudo apt install -y qemu-user-static binfmt-support

┌──(kali㉿kali)-[~]
└─$

┌──(kali㉿kali)-[~]
└─$ sudo dpkg --add-architecture amd64

┌──(kali㉿kali)-[~]
└─$

┌──(kali㉿kali)-[~]
└─$ sudo apt update

┌──(kali㉿kali)-[~]
└─$

┌──(kali㉿kali)-[~]
└─$ sudo apt install libc6:amd64

┌──(kali㉿kali)-[~]
└─$

```

Please keep in mind that more libraries may need to be installed depending on what package is being ran. In some cases these libraries will be installed automatically with the package install, however in others some research must be done to learn what is missing.

#### Running x86 code

```
# Before qemu-user-static install

┌──(kali㉿kali)-[~]
└─$ nmap
zsh: exec format error: nmap

┌──(kali㉿kali)-[~]
└─$

# After qemu-user-static install

┌──(kali㉿kali)-[~]
└─$ nmap
Nmap 7.92 ( https://nmap.org )
Usage: nmap [Scan Type(s)] [Options] {target specification}
TARGET SPECIFICATION:
...

┌──(kali㉿kali)-[~]
└─$
```

If there is a downloaded binary that is x86 that is not automatically being ran under `qemu-user-static`, you can invoke it with the following command:

```
┌──(kali㉿kali)-[~]
└─$ qemu-x86_x64-static my_x86_code

┌──(kali㉿kali)-[~]
└─$
```