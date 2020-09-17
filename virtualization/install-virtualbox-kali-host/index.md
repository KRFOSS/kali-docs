---
title: Installing VirtualBox on Kali (Host)
description:
icon:
date: 2020-03-05
type: post
weight:
author: ["g0tmi1k",]
tags: ["",]
keywords: ["",]
og_description:
---

You can install VirtualBox on Kali Linux, allowing you to use virtual machines (VMs) inside of Kali Linux. However if you are wanting to install Kali Linux as a VM, you want our [Kali Linux Guest Vbox (coming soon)](*coming soon*) page.

The first thing we are going to do is import VirtualBox's repository key:

```
kali@kali:~$ wget -q https://www.virtualbox.org/download/oracle_vbox_2016.asc -O- \
  | sudo apt-key add -
kali@kali:~$
```

We then move onto adding VirtualBox's repository.
We add this to a separate file, so it does not interfere with [Kali Linux's main repository](https://www.kali.org/docs/general-use/kali-linux-sources-list-repositories/).

One thing to bare in mind, [Kali Linux is based on Debian](https://www.kali.org/docs/policy/kali-linux-relationship-with-debian/), so we need to use [Debian's current stable version](https://www.debian.org/releases/stable/):

```
kali@kali:~$ echo "deb [arch=amd64] http://download.virtualbox.org/virtualbox/debian $( lsb_release -cs ) contrib" \
  | sudo tee /etc/apt/sources.list.d/virtualbox.list
kali@kali:~$
```

As we have altered our network repository, we need to re-build the cache.

```
kali@kali:~$ sudo apt update
kali@kali:~$
```

As VirtualBox has various kernel modules (e.g. `vboxdrv`, `vboxnetflt` and `vboxnetadp`), we need to make sure they are kept up-to-date when Kali's kernel gets updated. This can be achieved using [dkms](https://packages.debian.org/testing/dkms).

```
kali@kali:~$ sudo apt install -y dkms
kali@kali:~$
```

Now its time to install VirtualBox itself (along with its Extension Pack to expand VirtualBox's advanced features):

```
kali@kali:~$ sudo apt install -y virtualbox virtualbox-ext-pack
kali@kali:~$
```

When prompted, read and accept the license.


You can now find VirtualBox in the menu or start it via the command line:

```
kali@kali:~$ virtualbox
kali@kali:~$
```
