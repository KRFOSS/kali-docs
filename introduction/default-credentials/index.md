---
title: Kali's Default Credentials
description: What is Kali's password
icon:
type: post
weight: 60
author: ["g0tmi1k",]
---

Kali changed to a [non-root user policy](/docs/policy/kali-linux-user-policy/) by default [since the release of **2020.1**](https://www.kali.org/news/kali-default-non-root-user/).

This means:

- During the installation of [**amd64** and **i386** images](/docs/installation/), it will prompt you for a standard user account to be created.

- Any default operating system credentials used during **Live Boot**, or **pre-created image** (like **[Virtual Machines](/docs/virtualization/)** & **[ARM](/docs/arm/)**) will be:
    - User: `kali`
    - Password: `kali`

- **Vagrant** image _(based on their [policy](https://www.vagrantup.com/docs/boxes/base.html))_:
    - Username: `vagrant`
    - Password: `vagrant`

- [Amazon **EC2**](/docs/cloud/aws/):
    - User: `kali`
    - Password: `<ssh key>`

## Default Tool Credentials

Some tools shipped with Kali, will use their own default hardcoded credentials (others will generate a new password the first time its used). The following tools have the default values:

- [BeEF-XSS](https://tools.kali.org/exploitation-tools/beef-xss)
    - Username: `beef`
    - Password: `beef`
    - Configuration File: `/etc/beef-xss/config.yaml`

- MySQL
    - User: `root`
    - Password: ` ` _(blank)_
    - Setup Program: `mysql_secure_installation`

- [OpenVAS](https://tools.kali.org/vulnerability-analysis/openvas)
    - Username: `admin`
    - Password: `<Generated during setup>`
    - Setup Program: `openvas-setup`

- [Metasploit-Framework](https://tools.kali.org/exploitation-tools/metasploit-framework)
    - Username: `postgres`
    - Password: `postgres`
    - Configuration File: `/usr/share/metasploit-framework/config/database.yml`

- PowerShell-Empire/Starkiller
    - Username: `empireadmin`
    - Password: `password123`

- - -

For versions of Kali Linux older than 2020.1, here is our [previous credential information](/docs/introduction/kali-linux-default-passwords/) and [root policy](/docs/policy/kali-linux-root-user-policy/) information.
