---
title: SSH Configuration
description:
icon:
weight:
author: ["arnaudr",]
---

## SSH client: Wide Compatibility vs Strong Security

Since our release of [Kali Linux 2022.1](/blog/kali-linux-2022-1-release/) it is possible to easily configure the SSH client for **wider compatibility** to allow Kali to talk to as many SSH servers as possible. In wide compatibility mode, legacy key exchange algorithms _(such as diffie-hellman-*-sha1)_ and old ciphers _(such as CBC)_ are **enabled**. As a result, tools used inside of Kali are able to communicate using these outdated methods. _This is done to help increase Kali's ability to talk to older, obsolete SSH servers that are still using these older protocols. Older services using this may be at end of life, thus increasing the chances of discovering vulnerabilities or other problems_.

Note that this is _not the default_. Out of the box, the SSH client in Kali Linux is configured for **Strong Security** to enforce communication over more secure channels.

This setting can be changed easily using the `kali-tweaks` tool. Simply:

- Open a terminal and run `kali-tweaks`. 
- From there, select the _Hardening_ menu.
- Now you can choose between **Strong Security** _(the default)_ and **Wide Compatibility**.

_Note: This is achieved by creating or deleting the configuration file `/etc/ssh/ssh_config.d/kali-wide-compat.conf`._

## SSH server: automatic host keys generation

Since the release of [Kali Linux 2022.1](/blog/kali-linux-2022-1-release/), the SSH host keys are automatically generated if missing. This is achieved via the systemd service `regenerate-ssh-host-keys`.

So what are SSH host keys exactly? Those keys are required for the SSH server to be functional. They are supposed to be unique for each machine. Those keys can be found under `/etc/ssh` and are named `ssh_host_*_key`. This is how it usually looks like:

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

Since these keys must be unique for each machine, they can't be embedded in pre-built Kali images such as the Kali Linux [VM images](/get-kali/#kali-virtual-machines) or the [ARM images](/get-kali/#kali-arm). It's usually up to the user to create those keys before running the SSH server for the first time. However, for most users who are not familiar with SSH, this is a hurdle, as they are not aware of this technical detail.

To make it easier, Kali Linux now comes with a systemd service that takes care of that automatically, and generate those keys if they are missing. In theory, it's only during a first boot of a pre-built image that the service kicks in. On subsequent boots, the keys already exist and therefore nothing happens. This may not be the case for users who may remove these keys themselves.

For those who are not comfortable with this automatic behavior, it's very simple and straightforward to disable it:

```console
kali@kali:~$ sudo systemctl disable regenerate-ssh-host-keys.service
```

## OpenSSH: Restoring removed functionality via packages

As software and security advance, OpenSSH will remove features that are no longer commonly used from the default package. To keep the functionality of these removed features, there are alternative packages that can be installed.

### openssh-client-ssh1: DSA keys (removed OpenSSH 9.8p1), SSH1 protocol (removed OpenSSH 7.6)

The first package that we can use, `openssh-client-ssh1`, provides support for DSA keys and SSH1 protocol. This package is, in practicality, OpenSSH frozen at version 7.5. It provides binaries for `ssh`, `scp`, and `ssh-keygen`.

```console
kali@kali:~$ ssh -V
OpenSSH_9.8p1 Debian-8, OpenSSL 3.3.2 3 Sep 2024
kali@kali:~$
kali@kali:~$ sudo apt update
[...]
kali@kali:~$
kali@kali:~$ sudo apt install -y openssh-client-ssh1
Installing:
  openssh-client-ssh1

Summary:
  Upgrading: 0, Installing: 1, Removing: 0, Not Upgrading: 1293
  Download size: 478 kB
  Space needed: 1,425 kB / 1,245 GB available

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

### openssh-client-gssapi: GSS-API (removed OpenSSH 9.8p1, pre-installed in Kali Linux 2024.4 and on)

{{% notice info %}}
This is for users of Kali Linux who have upgraded their system and lost this functionality. The package will be pre-installed in Kali Linux starting in 2024.4.

As of September 23, 2024, this package currently only contains a changelog. This package is a placeholder for when the GSS-API changes occur in the OpenSSH package.
{{% /notice %}}

For those who need GSS-API authentication, we need to install the package `openssh-client-gssapi`.

```console
kali@kali:~$ ssh -V
OpenSSH_9.8p1 Debian-8, OpenSSL 3.3.2 3 Sep 2024
kali@kali:~$
kali@kali:~$ sudo apt update
[...]
kali@kali:~$
kali@kali:~$ sudo apt install -y openssh-client-gssapi
[...]
kali@kali:~$
```
