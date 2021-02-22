---
title: Kali Branches
description:
icon:
type: post
weight: 60
author: ["gamb1t", "g0tmi1k", "LindirQuenya",]
---

## What is a branch?

A branch is an alternative version of some software, in this case of the Kali OS. Kali Linux has multiple branches which allows for users to decide how up-to-date their packages will be. Kali Linux is [similar to Debian](/docs/policy/kali-linux-relationship-with-debian/) in many regards, one of which is the use of branches.

You may have multiple branches enabled at once. However, switching branches may introduce problems, as packages may be at different versions, and unavailable or unstable in certain cases.

Please see the [network sources](/docs/general-use/kali-linux-sources-list-repositories/) page for how to switch branches. For an example of how to use multiple branches, please see our [NVIDIA GPU Drivers](/docs/general-use/install-nvidia-drivers-on-kali-linux/) guide.

## Kali branches

First are the main branches, which are the most frequently used, and the most stable. These are often seen as "safe".

- **kali-rolling** is the main default branch that most should be using. It is being continuously updated, as it pulls from `kali-dev` after ensuring questionable packages are stable and combining them with packages from `kali-rolling-only`. From time to time, a package bug may slip into here, due to bugs in `debian-testing`.
- **kali-last-snapshot** is a branch of Kali that can be used if users want a more standard feeling of software control. For every new release, we freeze the code and merge `kali-rolling` into `kali-last-snapshot`, at which point users will get all of the updates between [versioned releases](/releases/) (i.e. 2019.3 -> 2019.4). This often is more stable, as packages are not updated (until the next release as it's a "Point Release") and go thought our release testing. This is the "safest" option.

Next are those that you will likely not need except in very special cases:

- **kali-experimental** is a staging area for work-in-progress packages.
- **kali-bleeding-edge** contains packages that are [automatically updated from the upstream]((/blog/bleeding-edge-kali-repositories/) git repositories. This branch has the potential to be very unstable.

### Development

- **kali-dev** is the development version of Kali Linux. It is created by combining three other branches: `kali-dev-only`, `kali-debian-picks` and `debian-testing`. Its mainly used for merging Debian's updates with the changes maintained by Kali Linux.
- **kali-dev-only** is the development distribution with Kali-specific packages. This branch is auto-merged into `kali-dev`.
- **kali-rolling-only** is a repository for packages that need to quickly reach `kali-rolling`.

### Branches used to assist with other branches

- **kali-debian-picks** contains packages cherry-picked from `debian-experimental` and `debian-unstable`. It is auto-merged into `kali-dev`.
- **[debian-testing](https://wiki.debian.org/DebianTesting)** is a mirror of Debian's testing distribution. This is used to build `kali-dev`.
- **[debian-experimental](https://wiki.debian.org/DebianExperimental)** and **[debian-unstable](https://wiki.debian.org/DebianUnstable)** are partial mirrors for specific packages that we want to cherry-pick.

## Mapping

Below is a diagram showing the relationship between the branches.

```plaintext
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
- [Unstable](https://www.debian.org/releases/unstable/)

**Stable** is the "safe" Debian branch. Around every two months, it is updated with a "[Point Release](https://wiki.debian.org/DebianReleases/PointReleases)", which is often just security updates. Packages don't generally get a version upgrade during this time, due to potential incompatibility and thus instability. This is the Debian equivalent of **kali-last-snapshot**.

**Testing** is the closest thing there is to a Debian "rolling" distribution, where "rolling" means that as soon as a package update is available, it's pushed out. Kali has used this branch as a starter for **kali-rolling** since January 2016.

**Unstable** is just after Debian's package development happens. The packages have been created, but not fully tested. Kali doesn't have an equivalent, as it is a rolling distribution.

For more information about how Kali relates to Debian, please see our [policy page](/docs/policy/kali-linux-relationship-with-debian/) on the matter.

### The kali-rolling repository

Contrary to kali-dev, kali-rolling is expected to be of better quality because it's managed by a tool that ensures installability of all the package it contains. That tool picks updated packages from kali-dev and copies them to kali-rolling only when they have been verified to be installable. Note however that those checks do not include any functional testing. It might still contain broken software due to other problems that are not covered by the package dependencies. **Kali Rolling is the primary repository that most users should be using**. They can also report any issue they have with Kali specific packages on [bugs.kali.org](https://bugs.kali.org/). Make sure to select the "kali-dev" version in "Product version".

Kali Rolling users are expected to have the following entries in their sources.list:

```plaintext
deb http://http.kali.org/kali kali-rolling main non-free contrib
```

### The kali-dev repository

**WARNING: While kali-dev is publicly accessible to everybody on all Kali mirrors, this distribution should not be used by end-users as it will regularly break**.

This repository is actually Debian's Testing distribution with all the kali-specific packages (available in the kali-dev-only repository) force-injected. Kali packages take precedence over the Debian packages. Sometimes when Testing changes, some Kali packages must be updated and this will not happen immediately. During this time, kali-dev is likely to be broken. This repository is where Kali developers push updated packages and is the basis used to create kali-rolling.
