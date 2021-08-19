---
title: OpenSSL Configuration
description:
icon:
weight:
author: ["arnaudr",]
---

Since Kali Linux 2021.3.0, OpenSSL is configured for wide compatibility. It means that old protocols (such as TLS 1.0 and TLS 1.1) and ciphers are enabled. This is needed so that tools can access legacy services that use those old protocols and ciphers.

This setting can be changed easily though. Simply open a terminal and start `kali-tweaks`. From there, select « Hardening -> OpenSSL Configuration ». Now you can choose between « Wide Compatibility » (the default) and « Strong Security ».

Note that this is achieved by tweaking the OpenSSL configuration through `/etc/ssl/openssl.cnf` and `/etc/ssl/kali.cnf`, so while it can restore access to some legacy services, it will not allow access to servers running protocols that are no longer compiled in libssl (for example SSLv3).
