---
title: Kali Network Repositories (apt sources.list)
description:
icon:
date: 2019-10-26
type: post
weight: 1
author: ["g0tmi1k",]
tags: ["",]
keywords: ["",]
og_description:
---

The single most common causes of a broken Kali Linux installation are following unofficial advice, and particularly arbitrarily populating the system's **sources.list** file with unofficial repositories. The following post aims to clarify what repositories should exist in **sources.list**, and when they should be used.
**Any additional repositories added to the Kali sources.list file will most likely _BREAK YOUR KALI LINUX INSTALL_.**

### Regular repositories
On a standard, clean install of Kali Linux, you should have the following entry present in **/etc/apt/sources.list**:

```markdown
deb http://http.kali.org/kali kali-rolling main non-free contrib
```

_You can find a list of official Kali Linux mirrors [here](/docs/community/kali-linux-mirrors/)._

### Source repositories
In case you require source packages, you might also want to add the following repositories as well:

```markdown
deb-src http://http.kali.org/kali kali-rolling main non-free contrib
```

### The kali-dev repository

**WARNING: While kali-dev is publicly accessible to everybody on all Kali mirrors, this distribution should not be used by end-users as it will regularly break.**

This repository is actually Debian's Testing distribution with all the kali-specific packages (available in the kali-dev-only repository) force-injected. Kali packages take precedence over the Debian packages. Sometimes when Testing changes, some Kali packages must be updated and this will not happen immediately. During this time, kali-dev is likely to be broken. This repository is where Kali developers push updated packages and is the basis used to create kali-rolling.

### The kali-rolling repository

Contrary to kali-dev, kali-rolling is expected to be of better quality because it's managed by a tool that ensures installability of all the package it contains. That tool picks updated packages from kali-dev and copies them to kali-rolling only when they have been verified to be installable. Note however that those checks do not include any functional testing. It might still contain broken software due to other problems that are not covered by the package dependencies. **Kali Rolling is the primary repository that most users should be using.** They can also report any issue they have with Kali specific packages on [bugs.kali.org](https://bugs.kali.org). Make sure to select the "kali-dev" version in "Product version".

Kali Rolling users are expected to have the following entries in their sources.list:

```markdown
deb http://http.kali.org/kali kali-rolling main non-free contrib
```
