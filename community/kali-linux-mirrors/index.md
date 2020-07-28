---
title: Official Kali Linux Mirrors
description:
icon:
date: 2020-05-11
type: post
weight: 100
author: ["g0tmi1k",]
tags: ["",]
keywords: ["",]
og_description:
---

## Using Official Repositories

The Kali Linux distribution has two [repositories](/docs/general-use/kali-linux-sources-list-repositories/), which are mirrored world-wide:

* [http.kali.org](http://http.kali.org) ([mirrorlist](http://http.kali.org/README.mirrorlist)): the main package repository;
* [cdimage.kali.org](http://cdimage.kali.org) ([mirrorlist](http://cdimage.kali.org/README.mirrorlist)): the repository of pre-built Kali ISO images.

When using the default hosts listed above, you'll automatically be redirected to a mirror site which is geographically close to you, and which is guaranteed to be up-to-date. If you prefer to manually select a mirror, click on the **_mirrorlist_** link near each hostname above and select a mirror that suits you. You will then need to edit your /etc/apt/sources.list file accordingly with the chosen values.

{{% notice info %}}
IMPORTANT! Do not add additional repositories to your <a href="/docs/general-use/kali-linux-sources-list-repositories"/>/etc/apt/sources.list</a> file.

Doing so will most likely break your Kali installation.
{{% /notice %}}
