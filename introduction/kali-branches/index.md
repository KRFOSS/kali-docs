--- 
title: Kali Branches 
description: 
icon: 
date: 2020-01-13
type: post 
weight: 
author: ["gamb1t",] 
tags: ["",] 
keywords: ["",] 
og_description: 
---

# What is a branch of Kali

Kali Linux is similar to Debian in many regards. One of those is the use of branches. We have multiple branches which users can use to decide how up to date their packages will be. 

## Kali branches available to users

- **kali-rolling**: kali-rolling is the constantly updated branch for users. This branch pulls from `kali-dev` after ensuring questionable packages are stable and combines them with packages from `kali-rolling-only`.

- **kali-dev**: kali-dev is the development version of Kali. It is created by combining three other branches: `kali-dev-only`, `debian-testing`, and `kali-debian-picks`. 

- **kali-last-snapshot**: kali-last-snapshot is a branch of Kali that can be used if users want a more standard feeling of software control. Every release we freeze the code and merge `kali-rolling` into kali-last-snapshot, at which point users will then get all of the updates between releases (i.e. 2019.3-2019.4).

- **kali-experimental**: kali-experimental is a staging area for work-in-progress packages. 

- **kali-bleeding-edge**: kali-bleeding-edge contains package automatically updated from the upstream git repositories. This branch has potential to be very unstable.

### Branches used to assist with other branches

- **kali-dev-only**: kali-dev-only is the development distribution with Kali-specific packages (which is auto-merged into kali-dev).

- **kali-rolling-only**: kali-rolling-only is a repository for packages that need to quickly reach `kali-rolling`

- **kali-debian-picks**: kali-debian-picks contains packages cherry picked from Debian-Unstable and Debian-Experimental. It is auto-merged into `kali-dev`.

- **debian-testing**: debian-testing is a mirror of Debian testing (which is used to build `kali-dev`)

- **debian-unstable** and **debian-experimental**: debian-unstable and debian-experimental are partial mirrors for specific packages that we want to cherry-pick.