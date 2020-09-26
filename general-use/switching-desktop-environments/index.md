---
title: Switching Desktop Environments
description:
icon:
type: post
weight:
author: ["gamb1t",]
---

During install a user may select whichever desktop environment that they prefer. However, when using the official VM this is not a possibility. In these cases, and many others, a user may wish to change their desktop environment.

To get started we will first update the system and install the `kali-desktop-*` [metapackage](/docs/general-use/metapackages/) for the given DE and update the default x-session-manager to be the one we will be using from now on. When we install KDE we will be asked which login manager to use. We will select "sddm" as we will have to replace KDE due to how it interacts with Xfce.

```
kali@kali:~$ sudo apt update
kali@kali:~$
kali@kali:~$ sudo apt install -y kali-desktop-kde
kali@kali:~$
kali@kali:~$ sudo update-alternatives --config x-session-manager
kali@kali:~$
```

If we choose to install KDE, we have to remember a few conflicts that might come up. We _can_ have KDE installed alongside other DEs, however the way that the packages are currently configured there will be a few configuration conflicts. For example, when both KDE and Xfce are installed Xfce will not be able to have its cursor selected.

To work around this we will remove Xfce and only have KDE installed, and we do not advise having other DEs alongside it. Keep in mind that this only applies for KDE; you may have both Xfce and GNOME installed at the same time with no conflicts.

```
kali@kali:~$ sudo apt purge --autoremove kali-desktop-xfce
kali@kali:~$
```
We will now reboot the system and make sure that all our changes were made properly.
