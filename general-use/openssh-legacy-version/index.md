---
title: OpenSSH legacy packages
description:
icon:
weight:
author: ["gamb1t",]
---

It is common for software to advance and older software to become legacy. This is common for OpenSSH to keep up with security concerns, however if a machine you are attempting to connect to has a legacy key or authentication type then this can cause problems. Thankfully, there are packages which allow for these no longer supported items to be used.

## DSA (removed 9.8p1), SSH-1 (removed 7.6)

The first package that we can use will, `openssh-client-ssh1` provides support for DSA keys and protocol 1. This package is, in practicality, OpenSSH frozen at version 7.5. It provides binaries for ssh, scp, and ssh-keygen.

```console
kali@kali:~$ ssh -V
OpenSSH_9.7p1 Debian-7, OpenSSL 3.2.2 4 Jun 2024
kali@kali:~$
kali@kali:~$ sudo apt update
...
kali@kali:~$
kali@kali:~$ sudo apt install -y openssh-client-ssh1
Installing:
  openssh-client-ssh1

Summary:
  Upgrading: 0, Installing: 1, Removing: 0, Not Upgrading: 1293
  Download size: 478 kB
  Space needed: 1,425 kB / 1,245 GB available

...
kali@kali:~$
kali@kali:~$ sudo apt-file list openssh-client-ssh1
openssh-client-ssh1: /usr/bin/scp1
openssh-client-ssh1: /usr/bin/ssh-keygen1
openssh-client-ssh1: /usr/bin/ssh1
...
kali@kali:~$
```

<!-- @gamb1t: commented out until package is in kali-rolling
## GSS-API (removed 9.8p1)

For those who need GSS-API authentication, we need to install a separate package, `openssh-client-gssapi`.

```console
kali@kali:~$ ssh -V
OpenSSH_9.7p1 Debian-7, OpenSSL 3.2.2 4 Jun 2024
kali@kali:~$
kali@kali:~$ sudo apt update
...
kali@kali:~$
kali@kali:~$ sudo apt install -y openssh-client-gssapi
...
kali@kali:~$
kali@kali:~$ sudo apt-file list openssh-client-gssapi
...
kali@kali:~$
```
-->
