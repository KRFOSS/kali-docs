---
title: Installing Tor Browser on Kali Linux
description:
icon:
type: post
weight:
author: ["gad3r",]
---

#### Install Instructions

Open the terminal then run the following commands:

```console
kali@kali:~$ sudo apt update
kali@kali:~$
kali@kali:~$ sudo apt install -y tor torbrowser-launcher
kali@kali:~$
```

As user run the following command:

```console
kali@kali:~$ torbrowser-launcher
```

First time it will download and install Tor Browser including the signature verification.

Next time it will be used to update and launch Tor Browser.

###### Reference

- [Debian Wiki: TorBrowser](https://wiki.debian.org/TorBrowser)
