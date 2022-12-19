---
title: Kernel Configuration
description:
icon:
weight:
author: ["arnaudr", "gamb1t",]
---

## Kernel Configuration

The Kali Linux kernel differs slightly from the "usual" kernel. For the purpose of penetration testing, we chose to use some defaults that differ from general-purpose Linux distributions, and we also patch ther kernel here and there. This page is an attempt to document those changes.

Generally speaking: default values can be modified via the tool `kali-tweaks`, but patches can't be undone.

### WiFi injection patch

Raphael: FWIW, it's due to wifi injection patch that we apply. I think it would be nice to document them somewhere.

### Dmesg unrestricted

The kernel logs are unrestricted by default, meaning that any unprivileged user can run the command `dmesg` and inspect the kernel logs (also called the "kernel ring buffer").

### Privileged ports

Traditionally, IPv4 ports below 1024 are called "privileged ports", and can only be used by the privileged user. In Kali Linux, this is disabled by default, meaning that all ports are "unprivileged", ie. any user can run a program that binds to any port, even below 1024.

The intention is to make it easier to run tools that require this capability, even though they run unprivileged. If this is not desitable, 

<!-- Is it really the intention? Do we have example of such tools? Why don't they run as root to start with? -->
