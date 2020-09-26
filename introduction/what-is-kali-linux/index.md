---
title: What is Kali Linux?
description:
icon:
type: post
weight: 10
author: ["g0tmi1k",]
---

[Kali Linux](https://www.kali.org/) is a [Debian-based Linux](/docs/policy/kali-linux-relationship-with-debian/) distribution aimed at advanced Penetration Testing and Security Auditing. Kali Linux contains [several hundred tools](/docs/policy/penetration-testing-tools-policy/) which are geared towards various information security tasks, such as Penetration Testing, Security research, Computer Forensics and Reverse Engineering. Kali Linux is developed, funded and maintained by [Offensive Security](https://www.offensive-security.com/), a leading information security training company.

Kali Linux was released on the 13th March 2013 as a complete, top-to-bottom rebuild of [BackTrack Linux](https://www.backtrack-linux.org/), adhering completely to [Debian](http://www.debian.org/) development standards.

- **More than 600 penetration testing tools included:** After reviewing every tool that was included in BackTrack, we eliminated a great number of tools that either simply did not work or which duplicated other tools that provided the same or similar functionality. Details on what's included are on the [Kali Tools](https://tools.kali.org/) site.
- **Free (as in beer) and always will be:** Kali Linux, like BackTrack, is [completely free](/docs/policy/kali-linux-open-source-policy/) of charge and always will be. You will never, ever have to pay for Kali Linux.
- **Open source Git tree:** We are committed to the open source development model and our [development tree](https://gitlab.com/kalilinux/) is available for all to see. All of the source code which goes into Kali Linux is available for anyone who wants to tweak or rebuild [packages](http://pkg.kali.org/) to suit their specific needs.
- **FHS compliant:** Kali adheres to the [Filesystem Hierarchy Standard](http://www.pathname.com/fhs/), allowing Linux users to easily locate binaries, support files, libraries, etc.
- **Wide-ranging wireless device support:** A regular sticking point with Linux distributions has been supported for wireless interfaces. We have built Kali Linux to support as many wireless devices as we possibly can, allowing it to run properly on a wide variety of hardware and making it compatible with numerous USB and other wireless devices.
- **Custom kernel, patched for injection:** As penetration testers, the development team often needs to do wireless assessments, so our kernel has the latest injection patches included.
- **Developed in a secure environment:** The [Kali Linux team](https://www.kali.org/about-us/) is made up of a small group of individuals who are the only ones trusted to commit packages and interact with the repositories, all of which is done using multiple secure protocols.
- **GPG signed packages and repositories:** Every package in Kali Linux is signed by each individual developer who built and committed it, and the repositories subsequently sign the packages as well.
- **Multi-language support:** Although penetration tools tend to be written in English, we have ensured that Kali includes true multilingual support, allowing more users to operate in their native language and locate the tools they need for the job.
- **Completely customizable:** We thoroughly understand that not everyone will agree with our design decisions, so we have made it as easy as possible for our more adventurous users to [customize Kali Linux](/docs/development/live-build-a-custom-kali-iso/) to their liking, all the way down to the kernel.
- **ARMEL and ARMHF support:** Since ARM-based single-board systems like the Raspberry Pi and BeagleBone Black, among others, are becoming more and more prevalent and inexpensive, we knew that [Kali's ARM support](/docs/introduction/kali-on-arm-a-bit-of-history/) would need to be as robust as we could manage, with fully working installations for both [ARMEL and ARMHF](http://en.wikipedia.org/wiki/ARM_architecture) systems. Kali Linux is available on [a wide range of ARM devices](/docs/arm/) and has ARM repositories integrated with the mainline distribution so tools for ARM are updated in conjunction with the rest of the distribution.

Kali Linux is specifically tailored to the needs of penetration testing professionals, and therefore all documentation on this site assumes prior knowledge of, and familiarity with, the Linux operating system in general. Please see [Should I Use Kali Linux?](/docs/introduction/should-i-use-kali-linux/) for more details on what makes Kali unique.
