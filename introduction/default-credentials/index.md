---
title: Default Credentials
description:
icon:
date: 2020-01-10
type: post
weight: 40
author: ["g0tmi1k",]
tags: ["",]
keywords: [""]
og_description:
---

## Kali Linux Default Operating System

Kali changed to a [non-root user policy](/docs/policy/kali-linux-user-policy/) by default [since the release of 2020.1](https://www.kali.org/news/kali-default-non-root-user/).

This means:
- During the installation of i386 and amd64 images, it will prompt you for a standard user account to be created.
- Any default operating system credentials used during Live Boot, or pre-created VMware and ARM images will be:
    - User: `kali`
    - Password: `kali`
- Vagrant image:
    - Username: `vagrant`
    - Password: `vagrant`

## Default Tool Credentials

Some tools shipped with Kali, will use their own default hardcoded credentials (others will generate a new password the first time its used). The following tools have the default values:

- BeEF-XSS
    - Username: `beef`
    - Password: `beef`
    - Configuration File: `/etc/beef-xss/config.yaml`

- MySQL
    - User: `root`
    - Password: ` ` _(blank)_
    - Setup Program: `mysql_secure_installation`

- OpenVAS
    - Username: `admin`
    - Password: `<Generated during setup>`
    - Setup Program: `openvas-setup`

- Metasploit-Framework
    - Username: `postgres`
    - Password: `postgres`
    - Configuration File: `/usr/share/metasploit-framework/config/database.yml`

- - -

For versions of Kali Linux older than 2020.1, here is its [previous credential information](/docs/introduction/kali-linux-default-passwords/) and [root policy](](/docs/policy/kali-linux-root-user-policy/)) information.
