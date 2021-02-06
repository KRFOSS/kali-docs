---
title: Installing snapd on Kali Linux
description:
icon:
type: post
weight:
author: ["gad3r",]
---

#### Install instructions


On Kali Linux, `snap` can be installed through:

```console
kali@kali:~$ sudo apt update
kali@kali:~$ sudo apt install snapd
```
Enabling and starting `snapd` and `snapd.apparmor` services:

```console
kali@kali:~$ sudo systemctl enable --now snapd apparmor
```

Log out and back in again, or restart your system, to ensure 
snap’s paths are updated correctly.

###### Reference

[Installing snap on Kali Linux](https://snapcraft.io/docs/installing-snap-on-kali)
