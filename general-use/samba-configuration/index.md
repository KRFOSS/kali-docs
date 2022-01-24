---
title: Samba Configuration
description:
icon:
weight:
author: ["arnaudr"]
---

Since our release of [Kali Linux 2021.4](/blog/kali-linux-2021-4-release/), the Samba client has been configured for **wider compatibility** to allow Kali to talk to as many Samba servers as possible. This means that legacy protocols _(such as LANMAN1, LANMAN2 and NT1)_ are **enabled by default**. This is a change from standard behavior, as these are disabled to enforce communication over more secure channels. As a result of this change tools used inside of Kali will be able to communicate using these outdated methods. _This is done to help increase Kali's ability to talk to older, obsolete Samba servers that are still using these older protocols. Older services using this may be at end of life, thus increasing the chances of discovering vulnerabilities or other problems_.

However, if you would rather keep Samba as secure as possible using today's modern standards, you can enable the **Strong Security** mode.

These settings can be changed easily using the `kali-tweaks` tool. Simply:

- Open a terminal and run `kali-tweaks`. 
- From there, select the _Hardening_ menu.
- Now you can choose between **Wide Compatibility** _(the default)_ and **Strong Security**.

_Note: This is achieved by changing the setting `client min protocol` in the Samba configuration file `/etc/samba/smb.conf`._
