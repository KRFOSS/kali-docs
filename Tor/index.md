---
title: Installing Tor Browser on Kali Linux
date: 2021-02-03
type: post
weight: 110
author: ["gad3r",]
---


Kali Linux provide the `torbrowser-launcher` tool to install Tor Browser.

#### Install instructions

Open the terminal then run the following commands:

```markdown
sudo apt update
sudo apt install tor torbrowser-launcher
```

As user run the following command:

```markdown
torbrowser-launcher
```

First time it will download install Tor Browser including the signature verification.

Next time it will be used to update an launch Tor Browser.

###### Reference 

[Debian Wiki: TorBrowser](https://wiki.debian.org/TorBrowser)

