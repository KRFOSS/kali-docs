---
title: OpenSSL Configuration
description:
icon:
weight:
author: ["arnaudr",]
---

Since [Kali Linux 2021.3](https://www.kali.org/blog/kali-linux-2021-3-release/), to allow Kali to talk to as many services as possible, OpenSSL has been configured for **wider compatibility**. This means that legacy protocols _(such as TLS 1.0 and TLS 1.1)_ and older ciphers are **enabled by default**. The result, tools used inside of Kali will be able to communication to these outdated methods. _This is done to help potentially increase the attack surface available. Older services using this may be at end of life, and increasing the chances of discovering vulnerabilities or other problems_.

However, if instead you would rather aim to keep your communication as secure as possible using today's modern standards, you can select **Strong Security** mode.

These settings can be changed easily using `kali-tweaks`. Simply:

- Open a terminal and run `kali-tweaks`. 
- From there, select Hardening -> OpenSSL Configuration
- Now you can choose between **Wide Compatibility** _(the default)_ and **Strong Security**.

_Note: This is achieved by tweaking the OpenSSL configuration through `/etc/ssl/openssl.cnf` and `/etc/ssl/kali.cnf`, so while it can restore access to some legacy services, it will not allow access to servers running protocols that are no longer compiled in `libssl` (for example SSLv3)._
