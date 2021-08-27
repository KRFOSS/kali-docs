---
title: Installing VirtualBox Guest Addition (Guest Tools)
description:
icon:
weight: 310
author: ["g0tmi1k",]
---

Installing "Guest Addition", gives a better user experience with VirtualBox VMs _(e.g. proper mouse and screen integration, as well as folder sharing)_. This is why since Kali Linux 2019.3, during the [setup process](https://gitlab.com/kalilinux/build-scripts/live-build-config/-/blob/master/simple-cdd/profiles/offline.downloads) it should **detect if Kali Linux is inside a VM**. If it is, then **automatically install any additional tools** (in VirtualBox's case, `virtualbox-guest-x11`).

You must use **VirtualBox 4.2.xx or higher**  in order to take advantage of the improvements, including compatibility updates, and enhanced stability of both the core application and the Guest Additions.

## virtualbox-guest-x11

Should you decide to [create your own VirtualBox installation of Kali Linux](/docs/virtualization/install-virtualbox-guest-vm/) _(rather than using our [pre-made VirtualBox images](https://www.kali.org/get-kali/#kali-virtual-machines))_, and you want to force a manual reinstall of `virtualbox-guest-x11` (as something has gone wrong), first make sure you are [fully updated](/docs/general-use/updating-kali/), then enter the following.

```console
kali@kali:~$ sudo apt update
...
kali@kali:~$
kali@kali:~$ sudo apt install -y --reinstall virtualbox-guest-x11
...
kali@kali:~$
kali@kali:~$ sudo reboot -f
kali@kali:~$
```

- - -

For older versions of Kali Linux, here is our [previous guide](/docs/virtualization/install-virtualbox-guest-additions-legacy/).
