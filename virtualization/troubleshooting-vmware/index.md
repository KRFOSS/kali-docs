---
title: Troubleshooting Kali VMware VM
description:
icon:
weight: 299
author: ["arnaudr",]
---

# Fix copy/paste, drag and drop with KDE desktop

This is for users of the Kali KDE desktop, within a VMware virtual machine. It is a known issue that copy/paste and drag and drop won't work out of the box. As a workaround, it's possible to disable the systemd user instance, and use KDE's legacy boot mechanism instead. To do so, open a terminal and type the following command:

```console
kali@kali:~$ kwriteconfig5 --file startkderc --group General --key systemdBoot false
```

As a verification, you can check that this command created the file `~/.config/startkderc` with the following content:

```console
kali@kali:~$ cat ~/.config/startkderc
[General]
systemdBoot=false
```

Then logout and log back in: the issue should be fixed.

This issue has been reported upstream, one can have a look at the following places for more details:
- <https://github.com/vmware/open-vm-tools/issues/568>
- <https://bugs.debian.org/cgi-bin/bugreport.cgi?bug=1020960>
