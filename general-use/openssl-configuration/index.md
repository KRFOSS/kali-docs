---
title: OpenSSl Configuration
description:
icon:
weight:
author: ["arnaudr",]
---

Since Kali Linux 2021.3.0, OpenSSl is configured for wide compatibility. It means that old protocols and ciphers are enabled. This is needed so that tools can access legacy services that use those old protocols and ciphers.

This setting can be changed easily though. Simply open a terminal and start `kali-tweaks`. From there, select « Hardening -> OpenSSL Configuration ». Now you can choose between « Wide Compatibility » (the default) and « Strong Security ».
