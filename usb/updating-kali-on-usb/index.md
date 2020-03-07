---
title: Updating Kali Linux on USB
description:
icon:
date: 2020-03-07
type: post
weight:
author: ["gamb1t",]
tags: ["",]
keywords: ["",]
og_description:
---

### Requirements

In order to properly update Kali on a USB, [persistence](../kali-linux-live-usb-persistence) must be setup. If persistence is not setup, re-imaging the USB with an ISO from the [weekly](https://cdimage.kali.org/kali-images/kali-weekly/) build will be a suitable update.

### Process

The best way to update Kali on a USB is to follow the same way you would on a full install.

First ensure that `etc/apt/sources.list` is properly populated.

```markdown
kali@kali:~$ cat /etc/apt/sources.list
deb http://http.kali.org/kali kali-rolling main contrib non-free
deb-src http://http.kali.org/kali kali-rolling main contrib non-free
```

After that we can run the following commands which will upgrade us to the latest Kali versions.

```markdown
kali@kali:~$ sudo apt update && sudo apt full-upgrade -y
```

Using this, the kernel will not be updated however. If the kernel is needed to be upgraded, perhaps there is a security vulnerability patched, a re-write would be needed with the latest ISO. If there is data that is needed to be saved, an rsync to a separate storage device would be a good way to backup that data.