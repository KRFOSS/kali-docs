---
title: Kali's Relationship With Debian
description:
icon:
type: post
weight:
author: ["g0tmi1k",]
---

The Kali Linux distribution is based on [Debian Testing](https://www.debian.org/releases/testing/). Therefore, most of the Kali packages are imported, as-is, from the Debian repositories. In some cases, newer packages may be imported from Debian Unstable or Debian Experimental, either to improve user experience, or to incorporate needed bug fixes.

### Forked Packages

In order to implement some of Kali's unique features, we had to fork some packages. The Kali development team strives to keep such packages to a minimum by improving the upstream packages whenever possible, either by integrating the feature directly, or by adding the required hooks so that it's straightforward to enable the desired features without further modifying the upstream packages themselves.

Each package forked by Kali is maintained in a [Git repository](https://gitlab.com/kalilinux) with a "debian" branch so that updating a forked package can be easily done with a simple _git merge debian_ in its master branch.

### Additional Packages

Beyond this, Kali incorporates many additional [packages](https://pkg.kali.org/) which are specific to the penetration testing and security auditing field. The majority of these packages constitute "free software" according to [Debian's Free Software Guidelines](http://www.debian.org/social_contract#guidelines). Kali intends to contribute those packages back to Debian and to maintain them directly within Debian.

To facilitate this, Kali packaging strives to comply with the [Debian Policy](http://www.debian.org/doc/debian-policy/) and follow the best practices in use in Debian.
