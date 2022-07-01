---
title: No sound on Kali 2022.2
description:
icon:
weight: 5000
author: ["gamb1t",]
---

As of writing there are some issues with sound being disabled that can occur. To fix this is pretty easy. We first look to see if `pipewire-pulse` is installed.

```console
kali@kali:~$ apt list --installed | grep pipewire-pulse

WARNING: apt does not have a stable CLI interface. Use with caution in scripts.

pipewire-pulse/kali-rolling,now 0.3.51-1 amd64 [installed,automatic]
kali@kali:~$
```

If this package is installed, we will want to uninstall it and then reboot.

```console
kali@kali:~$ sudo apt purge --autoremove pipewire-pulse
...
kali@kali:~$
kali@kali:~$ sudo reboot
```

This should then get a working audio setup.
