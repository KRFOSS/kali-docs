---
title: Installing Tor Browser on Kali Linux
description:
icon:
type: post
weight:
author: ["gad3r",]
---

Kali Linux provide the `torbrowser-launcher` tool to install Tor Browser.

#### Install instructions

Open the terminal then run the following commands:

```markdown
kali@kali:~$ sudo apt update
kali@kali:~$ sudo apt install tor torbrowser-launcher
```

As user run the following command:

```markdown
kali@kali:~$ torbrowser-launcher
```

First time it will download and install Tor Browser including the signature verification.

Next time it will be used to update and launch Tor Browser.

###### Reference

[Debian Wiki: TorBrowser](https://wiki.debian.org/TorBrowser)
