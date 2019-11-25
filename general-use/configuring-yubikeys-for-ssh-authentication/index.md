---
title: Configuring Yubikeys for SSH Authentication
description:
icon:
date: 2019-10-26
type: post
weight: 100
author: ["g0tmi1k",]
tags: ["",]
keywords: ["",]
og_description:
---

This document explains how to configure a Yubikey for SSH authentication

#### Prerequisites

Install Yubikey Personalization Tool and Smart Card Daemon

```markdown
apt install -y yubikey-personalization scdaemon
```

#### Detect Yubikey

First, you'll need to ensure that your system is fully upgraded.

```html
root@kali:~# pcsc_scan
Scanning present readers...
Reader 0: Yubico Yubikey 4 OTP+U2F+CCID 00 00
  Card state: Card inserted,
Possibly identified card (using /usr/share/pcsc/smartcard_list.txt):
    Yubico Yubikey 4 OTP+CCID
```

#### Configuration<

In order for our Yubikey to be detected as a smart card, we'll need to set our Yubikey to CCID mode.

```markdown
root@kali:~# ykpersonalize -m 86
The USB mode will be set to: 0x86

Commit? (y/n) [n]: y
```

After this modification, GPG should now be able to recognize our Yubikey as a smart card.

```markdown
root@kali:~# gpg --card-status
Reader ...........: Yubico Yubikey 4 OTP U2F CCID 00 00
Version ..........: 2.1
Manufacturer .....: Yubico
Key attributes ...: rsa2048 rsa2048 rsa2048
Max. PIN lengths .: 127 127 127
PIN retry counter : 3 0 3
```

Now we will need to change the default PIN that is configured.

{{% notice info %}}
The default PIN is 123456 and default admin PIN is 12345678.
{{% /notice %}}

```markdown
root@kali:~# gpg --change-pin
gpg: OpenPGP card no. F8482212202010006041587850000 detected

1 - change PIN
2 - unblock PIN
3 - change Admin PIN
4 - set the Reset Code
Q - quit

Your selection? 1 # Enter a new PIN
PIN changed.

1 - change PIN

Your selection? 3 # Enter a new admin PIN
PIN changed.

Your selection? q
```
