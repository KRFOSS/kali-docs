---
title: What is Kali Linux?
description:
icon:
weight: 10
author: ["g0tmi1k"]
---

### About Kali Linux

[Kali Linux](/) _(formerly known as [BackTrack Linux](https://www.backtrack-linux.org/))_ is an [open-source](/docs/policy/kali-linux-open-source-policy/), [Debian-based Linux](/docs/policy/kali-linux-relationship-with-debian/) distribution which allows users to perform advanced penetration testing and security auditing. It runs on multiple platforms and is freely available and accessible to both information security professionals and hobbyists. 

This distribution has [several hundred tools, configurations, and scripts](/features/) with [industry-specific modifications](/docs/policy/penetration-testing-tools-policy/) that allow users to focus on tasks such as computer forensics, reverse engineering, and vulnerability detection, instead of dealing with unrelated activities.

This distribution is specifically tailored to the needs of experienced penetration testers, so therefore all documentation on this site assumes prior knowledge of, and familiarity with, the Linux operating system in general. Please see [Should I Use Kali Linux?](/docs/introduction/should-i-use-kali-linux/) for more details on what makes Kali unique.

- _If you wish to review the features of Kali Linux, please see the following page: [Kali Linux Overview](/features/)._


### Kali Linux Features

<!--
Tool count:
- https://pkg.kali.org/derivative/kali-roll/
- https://pkg.kali.org/teams/kali-developers/
- https://gitlab.com/kalilinux/packages/ + archived
-->

- **It is free (as in beer) and always will be:** Kali Linux is [completely free](/docs/policy/kali-linux-open-source-policy/) of charge and always will be. You will never, ever have to pay for Kali Linux.
- **Features an open source Git tree:** The Kali Linux team is committed to the open source development model and our [development tree](https://gitlab.com/kalilinux) is available for all to see. All of the source code which goes into Kali Linux is available for anyone who wants to tweak or rebuild [packages](https://pkg.kali.org/) to suit their specific needs.
- **Compliant with the Filesystem Hierarchy Standard:** Since this distribution adheres to the [Filesystem Hierarchy Standard](https://www.pathname.com/fhs/), Linux users can easily locate binaries, support files, libraries, and so on.
- **Has wide-ranging support for devices:** Kali supports a wide variety of hardware and as many wireless devices as possible, including USB-based devices.
- **Includes a custom kernel that is patched for injection:** As penetration testers, the development team often needs to do wireless assessments, so our kernel has the latest injection patches included.
- **Developed in a secure environment:** The [Kali Linux team](/about-us/) is a small group of individuals and they are the only ones trusted to commit packages and interact with the repositories. All changes to the distribution are done with multiple secure protocols.
- **Has GPG signed packages and repositories:** Every package in Kali Linux is signed by each individual developer who built and committed it, and the repositories subsequently sign the packages as well.
- **Features multi-language support:** Although penetration tools tend to be written in English, we have ensured that Kali includes true multilingual support. This allows more users to operate in their native language and locate the tools that they need for the job.
- **Completely customizable:** We thoroughly understand that not everyone will agree with our design decisions, so we have made it as easy as possible for our more adventurous users to [customize Kali Linux](/docs/development/live-build-a-custom-kali-iso/) to their liking, all the way down to the kernel.
- **ARMEL and ARMHF support:** Since ARM-based single-board systems like the [Raspberry Pi](/docs/arm/raspberry-pi/) and [BeagleBone Black](/docs/arm/beaglebone-black/) are becoming increasingly more inexpensive and popular with penetration testers, our team realized that [Kali's ARM support](/docs/introduction/kali-on-arm-a-bit-of-history/) would need to be as robust as we could manage. This distribution supports fully working installations for both [ARMEL and ARMHF](https://en.wikipedia.org/wiki/ARM_architecture) systems, and it is available on [a wide range of ARM devices](/docs/arm/). The ARM repositories are integrated into the mainline distribution so tools for ARM are updated in conjunction with the rest of the distribution.

