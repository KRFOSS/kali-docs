---
title: Should I Use Kali Linux?
description:
icon:
date: 2019-11-25
type: post
weight: 20
author: ["g0tmi1k",]
keywords: [""]
og_description:
---

## What's Different About Kali Linux?

Kali Linux is specifically geared to meet the requirements of professional penetration testing and security auditing. To achieve this, several core changes have been implemented in Kali Linux which reflect these needs:

1. **Network services disabled by default:** Kali Linux contains systemd hooks that [disable network services](/docs/policy/kali-linux-network-service-policies/) by default. These hooks allow us to install various services on Kali Linux, while ensuring that our distribution remains secure by default, no matter what packages are installed. Additional services such as Bluetooth are also blacklisted by default.

2. **Custom Linux kernel:** Kali Linux uses an upstream kernel, patched for wireless injection.

3. **A _minimal_ and _trusted_ set of repositories:** given the aims and goals of Kali Linux, maintaining the integrity of the system as a whole is absolutely key. With that goal in mind, the set of upstream software sources which Kali uses is [kept to an absolute minimum](/docs/general-use/kali-linux-sources-list-repositories/). Many new Kali users are tempted to add additional repositories to their **sources.list**, but doing so runs a _very serious risk_ of breaking your Kali Linux installation.

## Is Kali Linux Right For You?

As the distribution's developers, you might expect us to recommend that everyone should be using Kali Linux. The fact of the matter is, however, that Kali is a Linux distribution specifically geared towards professional penetration testers and security specialists, and given its unique nature, it is **NOT** a recommended distribution if you're unfamiliar with Linux or are looking for a general-purpose Linux desktop distribution for development, web design, gaming, etc.

Even for experienced Linux users, Kali can pose some challenges. Although Kali is an open source project, it's not a _wide_-open source project, for reasons of security. The development team is small and trusted, packages in the repositories are signed both by the individual committer and the team, and — importantly — the set of upstream repositories from which updates and new packages are drawn is very small. Adding repositories to your software sources which have not been tested by the Kali Linux development team is a good way to cause problems on your system.

While Kali Linux is architected to be [highly customizable](/docs/development/live-build-a-custom-kali-iso/), don't expect to be able to add random unrelated packages and repositories that are "out of band" of the regular Kali software sources and have it Just Work. In particular, there is absolutely no support whatsoever for the apt-add-repository command, LaunchPad, or PPAs. Trying to install _**Steam**_ on your Kali Linux desktop is an experiment that will not end well. Even getting a package as mainstream as NodeJS onto a Kali Linux installation can take [a little extra effort and tinkering](http://www.acme-dot.com/stupid-problems-deserve-stupid-solutions/).

If you are unfamiliar with Linux generally, if you do not have at least a basic level of competence in administering a system, if you are looking for a Linux distribution to use as a learning tool to get to know your way around Linux, or if you want a distro that you can use as a general purpose desktop installation, _Kali Linux is probably not what you are looking for_.

In addition, misuse of security and penetration testing tools within a network, particularly without specific authorization, may cause irreparable damage and result in significant consequences, personal and/or legal. "Not understanding what you were doing" is not going to work as an excuse.

However, if you're a professional penetration tester or are studying penetration testing with a goal of becoming a certified professional, there's no better toolkit — at any price — than Kali Linux.

{{% notice info %}}
If you are looking for a Linux distribution to learn the basics of Linux and need a good starting point, Kali Linux is not the ideal distribution for you. You may want to begin with [Ubuntu](https://www.ubuntu.com), [Mint](https://www.linuxmint.com), or [Debian](https://www.debian.org/) instead. If you're interested in getting hands-on with the internals of Linux, take a look the ["Linux From Scratch"](http://www.linuxfromscratch.org/) project.
{{% /notice %}}
