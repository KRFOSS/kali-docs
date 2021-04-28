---
title: Get the latest unreleased features and bug fixes with Kali Bleeding Edge
description:
icon:
type: post
weight: 62
author: ["rhertzog",]
---

## Introduction

`kali-bleeding-edge` is the name of a [Kali
repository](/docs/general-use/kali-branches/) that you can enable in your
APT configuration to have access to packages built with the latest 
version of the source code found in the upstream git repository.

## Use cases

### Ensure you have the latest version of a specific tool

With over 300 tools in our repository, it's close to impossible for us to
keep every tool updated to their latest git version all the time — it's
already hard to do with official releases. However there are certainly
cases where you really want the latest version. Consider this:

You have an audit tomorrow, and you're getting your tools ready. You boot
Kali Linux up, run `sudo apt update && sudo apt full-upgrade` to make sure
that you are running the latest stable versions of *$your_favorite_tool*. After
the update, you notice that it was updated to last week's version.
However, in the past week, some pretty awesome updates were added to
the git repository of *$your_favorite_tool*, and you really need those new
features for the upcoming assessment. What do you do?

You can `git clone` the upstream repository in your home directory and
work from there but then you lose any integration with Kali. You also
have to take care of dependencies manually. No, the smartest solution
is to rely on the tireless work of our Kali Bot that constantly crunches
upstream git repository to populate our kali-bleeding-edge repository.

### Verify bug fixes committed by upstream authors

You're an avid user of *$your_favorite_tool* and you have found some
bug. As a good open source citizen, you checked the upstream project
on GitHub, saw that the bug was not yet reported and you sent a detailed
report on how to reproduce the issue.

Thanks to this, the upstream developer identified the problem, committed
a possible fix and asked you to verify whether the problem is fixed with
the latest git version.

You like the tool and the open-source philosophy, but you're not a
developer and don't really want to build a git version of the tool just
for this.

Thankfully, you remember the kali-bleeding-edge repository
and after a quick check on [pkg.kali.org](https://pkg.kali.org) you
notice that the Kali bot already prepared an updated package. You
quickly install it and reply to the upstream developer with the desired
confirmation (or further information).

## Caution: watch for the blood

Remember that it's called “Kali **bleeding edge**” for a reason. While
the poor Kali bot does its best, its AI is not always smart enough to detect
new dependencies, backward incompatible changes, and any other
instructions left by the upstream developer in the README. So while
it will usually manage to build some package, there's no guaranty
that the updated package will work.

Some upstream developers deliberately break their tool while they are
working on some refactoring, or other major changes. They recommend their
users to use only the official releases. In that case, the
kali-bleeding-edge package will be useless if not actively harmful (the
non-finished code could for example break some of the data managed by
the tool).

Thus it's a good idea to have a look at the changes that landed in the
upstream git repository before deciding to upgrade a package to its
kali-bleeding-edge version.

## How to enable the repository

Ok. You have been warned and you promised to use kali-bleeding-edge
in a reasonable way. How do you enable that repository? Easy:

```console
kali@kali:~$ sudo tee /etc/apt/sources.list.d/kali-bleeding-edge.list <<END
deb http://http.kali.org/kali kali-bleeding-edge main contrib non-free
END
kali@kali:~$ sudo apt update
[...]
```

If you later want to disable the repository, it's as easy as removing
the file that you just created:

```console
kali@kali:~$ sudo rm -f /etc/apt/sources.list.d/kali-bleeding-edge.list
```

## Install a package from kali-bleeding-edge

Once the repository has been enabled, installing a package from
kali-bleeding-edge is trivial with `apt install
<package>/kali-bleeding-edge`. Here's an example for gitleaks:

```console
kali@kali:~$ sudo apt install gitleaks/kali-bleeding-edge
Reading package lists... Done
Building dependency tree       
Reading state information... Done
Selected version '7.4.0+git20210412.1.6f5ad9d-0kali1~jan+nus1' (http.kali.org [amd64]) for 'gitleaks'
The following packages will be upgraded:
  gitleaks
1 upgraded, 0 newly installed, 0 to remove and 283 not upgraded.
Need to get 2504 kB of archives.
After this operation, 0 B of additional disk space will be used.
Get:1 http://kali.download/kali kali-bleeding-edge/main amd64 gitleaks amd64 7.4.0+git20210412.1.6f5ad9d-0kali1~jan+nus1 [2504 kB]
Fetched 2504 kB in 2s (1257 kB/s)   
(Reading database ... 106991 files and directories currently installed.)
Preparing to unpack .../gitleaks_7.4.0+git20210412.1.6f5ad9d-0kali1~jan+nus1_amd64.deb ...
Unpacking gitleaks (7.4.0+git20210412.1.6f5ad9d-0kali1~jan+nus1) over (7.4.0-0kali1) ...
Setting up gitleaks (7.4.0+git20210412.1.6f5ad9d-0kali1~jan+nus1) ...
Processing triggers for kali-menu (2021.1.4) ...
```

Notice that the versioning scheme is a bit special. It starts with the
version of the last upstream release and is followed by
`+git<date>.<number>.<commit>` (where `<date>` is the date of the last
upstream commit, `<number>` is a simple increment to cater for multiple
versions in the same day, and `<commit>` is a short commit identifier).
The Debian revision contains `~jan+nus<X>` indicating that this is
a "new uptsream snapshot" (`nus`) that was created by the [Janitor
Bot](https://salsa.debian.org/jelmer/debian-janitor) (which powers the
[Kali Bot](https://janitor.kali.org)). The tilde ensures that this
sorts lower than any subsequent manual release of the same version.

Be aware that once you have installed a package from kali-bleeding-edge,
`apt` will continue to fetch updates for that package from this repository
for as long as the kali-bleeding-edge version is newer than the
kali-rolling version.

## Switch back to the stable version

If you want to go back to the stable version of the package, it works
the same way, except that you set the target distribution to kali-rolling:

```console
kali@kali:~$ $ sudo apt install gitleaks/kali-rolling
Reading package lists... Done
Building dependency tree       
Reading state information... Done
Selected version '7.4.0-0kali1' (kali-rolling [amd64]) for 'gitleaks'
The following packages will be DOWNGRADED:
  gitleaks
0 upgraded, 0 newly installed, 1 downgraded, 0 to remove and 282 not upgraded.
Need to get 2504 kB of archives.
After this operation, 0 B of additional disk space will be used.
Do you want to continue? [Y/n] 
Get:1 http://kali.download/kali kali-rolling/main amd64 gitleaks amd64 7.4.0-0kali1 [2504 kB]
Fetched 2504 kB in 2s (1060 kB/s)   
dpkg: warning: downgrading gitleaks from 7.4.0+git20210412.1.6f5ad9d-0kali1~jan+nus1 to 7.4.0-0kali1
(Reading database ... 106747 files and directories currently installed.)
Preparing to unpack .../gitleaks_7.4.0-0kali1_amd64.deb ...
Unpacking gitleaks (7.4.0-0kali1) over (7.4.0+git20210412.1.6f5ad9d-0kali1~jan+nus1) ...
Setting up gitleaks (7.4.0-0kali1) ...
Processing triggers for kali-menu (2021.1.4) ...
```
