---
title: OpenSSL Configuration
description:
icon:
weight:
author: ["arnaudr", "elwood", "g0tmi1k", "rhertzog"]
---

Since our release of [Kali Linux 2021.3](https://www.kali.org/blog/kali-linux-2021-3-release/) OpenSSL has been configured for **wider compatibility** to allow Kali to talk to as many services as possible. This means that legacy protocols _(such as TLS 1.0 and TLS 1.1)_ and older ciphers are **enabled by default**. This is a change from standard behavior, as these are disabled to enforce communication over more secure channels. As a result of this change tools used inside of Kali will be able to communicate using these outdated methods. _This is done to help increase Kali's ability to talk to older, obsolete systems and servers that are still using these older protocols. Older services using this may be at end of life, thus increasing the chances of discovering vulnerabilities or other problems_.

However, if you would rather keep your communication as secure as possible using today's modern standards, you can enable the **Strong Security** mode.

These settings can be changed easily using the `kali-tweaks` tool. Simply:

- Open a terminal and run `kali-tweaks`. 
- From there, select Hardening -> OpenSSL Configuration.
- Now you can choose between **Wide Compatibility** _(the default)_ and **Strong Security**.

_Note: This is achieved by changing the OpenSSL configuration through `/etc/ssl/openssl.cnf` and `/etc/ssl/kali.cnf`, so while it can restore access to some legacy services, it will not allow access to servers running protocols that are no longer compiled in the `libssl` library (for example SSLv3)._
