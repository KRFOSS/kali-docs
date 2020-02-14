---
title: Kali Branches
description:
icon:
date: 2020-01-13
type: post
weight:
author: ["gamb1t", "g0tmi1k",]
tags: ["",]
keywords: ["",]
og_description:
---

## What is a branch

Kali Linux has multiple branches which allows for users to decide how up to date their packages will be. Kali Linux is [similar to Debian](/docs/policy/kali-linux-relationship-with-debian/) in many regards, one of those is the use of branches.

You may have multiple branches enabled at once, however, switching branches may introduce problems, as packages may be at different versions, and on available in certain ones and may not be stable.

Please see the [network sources](/docs/general-use/kali-linux-sources-list-repositories/) page for how to switch branches, as well as an example of how to use multiple branches, please see our [NVIDIA GPU Drivers](/docs/general-use/install-nvidia-drivers-on-kali-linux/) guide.

## Kali branches

First are the main branches, which are the most frequently used, and the most stable. These are often seen as "safe".

- **kali-rolling** is the main default branch that most should be using. It is being continuously-updated, as it pulls from `kali-dev` after ensuring questionable packages are stable and combines them with packages from `kali-rolling-only`. From time to time, a package bug may slip into here, due to `debian-testing`.
- **kali-last-snapshot** is a branch of Kali that can be used if users want a more standard feeling of software control. Every release we freeze the code and merge `kali-rolling` into `kali-last-snapshot`, at which point users will then get all of the updates between [versioned releases]((https://www.kali.org/kali-linux-releases/)) (i.e. 2019.3 -> 2019.4). This often is more stable, as packages are not updated (until the next release as its a "Point Release") and go thought our release smoke testing. This is the "safest" option.

And those that you will likely not need except in very special cases:

- **kali-experimental** is a staging area for work-in-progress packages.
- **kali-bleeding-edge** contains package [automatically updated from the upstream](https://www.kali.org/news/bleeding-edge-kali-repositories/) git repositories. This branch has potential to be very unstable.

### Development

- **kali-dev** is the development version of Kali. It is created by combining three other branches (`kali-dev-only`, `kali-debian-picks` and `debian-testing`). Its mainly used for merging updates which come from Debian and the changes maintained by Kali.
- **kali-dev-only** is the development distribution with Kali-specific packages (which is auto-merged into `kali-dev`).
- **kali-rolling-only** is a repository for packages that need to quickly reach `kali-rolling`

### Branches used to assist with other branches

- **kali-debian-picks** contains packages cherry picked from `debian-experimental` and `debian-unstable`. It is auto-merged into `kali-dev`.
- **[debian-testing](https://wiki.debian.org/DebianTesting)** is a mirror of Debian testing (which is used to build `kali-dev`)
- **[debian-experimental]([https://wiki.debian.org/DebianExperimental])** and **[debian-unstable](https://wiki.debian.org/DebianUnstable)** are partial mirrors for specific packages that we want to cherry-pick.

## Mapping

Below is a diagram showing the relationship between the branches.

```
debian-experimental -> debian-unstable -> debian-testing -> kali-dev -> kali-rolling -> kali-last-snapshot
      |                      |                              ^   ^         ^       |
      v                      v                              |   |         |       v
      ---------------------------------> kali-debian-picks -|   |         |       --> kali-bleeding-edge
                                                                |         |                  ^
kali-experimental -> kali-dev-only -----------------------------|         |                  |
                                                                          |               Upstream
kali-rolling-only --------------------------------------------------------|
```

## Debian's Relation

[Debian](https://www.debian.org/releases/) has three main options:

- [Stable](https://www.debian.org/releases/stable/)
- [Testing](https://www.debian.org/releases/testing/)
- [Unable](https://www.debian.org/releases/unstable/)

**Stable** is a fix release, which comes out every x months. They often do a "[Point Release](https://wiki.debian.org/DebianReleases/PointReleases)", which often is security updates. Package don't often get a version upgrade during this time (as things may alter, which may break, thus isn't stable). In relation to Kali, this is **kali-last-snopshot**.

**Testing** is the closest offering of Debian "rolling" on offer but yet being stable. Rolling being, as soon as their is an package update available, its pushed out. However, this is what Kali is since January 2016, a rolling distribution with our **kali-rolling**.

**Unable** is just after Debian's package development happens. The package has been created, but not yet fully tested. Kali doesn't have this, as it rolling distribution.

For more information about how Kali relates to Debian, please see our [policy page](https://www.kali.org/docs/policy/kali-linux-relationship-with-debian/) on the matter.
