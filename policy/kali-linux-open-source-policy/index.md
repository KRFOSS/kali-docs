---
title: Kali Linux Open Source Policy
description:
icon:
type: post
weight:
author: ["g0tmi1k",]
---

Kali Linux is a Linux distribution that aggregates thousands of free software [packages](https://pkg.kali.org/) in its main section. As a Debian derivative, all of the core software in Kali Linux complies with the [Debian Free Software Guidelines](https://www.debian.org/social_contract#guidelines).

As the specific exception to the above, Kali Linux's non-free section contains several tools which are not open source, but which have been made available for redistribution by [Offensive Security](https://www.offensive-security.com/) through default or specific licensing agreements with the vendors of those tools.

If you want to build a Kali derivative, you should _review the license_ of each Kali-specific non-free package before including it in your distribution - but note that non-free packages which are imported from Debian are safe to redistribute.

More importantly, all of the specific developments in Kali Linux's infrastructure or its integration with the included software have been put under the [GNU GPL](http://www.gnu.org/licenses/gpl.html).

If you want more information about the license of any given piece of software, you can either check `debian/copyright` in the source package or `/usr/share/doc/_package_/copyright` for a package that you have already installed.
