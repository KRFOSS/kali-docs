---
title: SSH Configuration
description:
icon:
weight:
author: ["arnaudr"]
---

Since our release of [Kali Linux 2022.1](/blog/kali-linux-2022-1-release/) it is possible to easily configure the SSH client for **wider compatibility** to allow Kali to talk to as many SSH servers as possible. In wide compatibility mode, legacy key exchange algorithms _(such as diffie-hellman-*-sha1)_ and old ciphers _(such as CBC)_ are **enabled**. As a result, tools used inside of Kali are able to communicate using these outdated methods. _This is done to help increase Kali's ability to talk to older, obsolete SSH servers that are still using these older protocols. Older services using this may be at end of life, thus increasing the chances of discovering vulnerabilities or other problems_.

Note that this is _not the default_. Out of the box, the SSH client in Kali Linux is configured for **Strong Security** to enforce communication over more secure channels.

This setting can be changed easily using the `kali-tweaks` tool. Simply:

- Open a terminal and run `kali-tweaks`. 
- From there, select the _Hardening_ menu.
- Now you can choose between **Strong Security** _(the default)_ and **Wide Compatibility**.

_Note: This is achieved by creating or deleting the configuration file `/etc/ssh/ssh_config.d/kali-wide-compat.conf`._
