---
title: 3D acceleration with Mesa 21 & Direct3D 12
description: Enabled 3D acceleration for Kali Linux in WSL
icon: ti-pin
archived: "true"
weight:
author: ["arnaudr",]
---

**This page is obsolete**. Everything required to run Kali Linux in WSL is already available in Kali Rolling, there is no need to install packages from the `kali-experimental` branch.

## Content:

- [Overview](#overview)
- [Installation Steps](#installation-steps)

## Overview

Mesa 3D is a fundamental part of the graphics stack that powers Kali Linux. The version 21 of Mesa 3D, released in March 2021, brings support for GPU accelerated 3D graphics in WSL. This feature is available for hardware that support Direct3D 12. And roughly, it means that for those who run Kali Linux in Windows via WSL, GUI apps that can use a GPU should perform much better.

Note that at the moment, GUI app support in WSL is not yet released to the public, it is only available as part of the "Insider Builds" of WSL. Also note that this is something very new and not widely tested.

In this page, we describe the procedure to install the latest version of Mesa from the branch `kali-experimental` of Kali Linux.

## Installation Steps

Open a terminal, and login as the root user:

```console
kali@kali:~$ sudo su
```

Make sure that your installation of Kali Linux is up to date:

```console
root@kali:$ apt update
[…]
root@kali:$
root@kali:$ apt upgrade
[…]
```

Add the `kali-experimental` suite to your APT sources:

```console
root@kali:$ cat << 'EOF' > /etc/apt/sources.list.d/kali-experimental.list
deb http://http.kali.org/kali kali-experimental main contrib non-free
EOF
```

Update again so that APT knows about Kali experimental:

```console
root@kali:$ apt update
[…]
```

And now we can upgrade the graphics stack, ie. Mesa and DRM:

```console
root@kali:$ apt install -t kali-experimental '?upgradable ?source-package("mesa|libdrm")'
[…]
The following additional packages will be installed:
  libdrm-amdgpu1 libllvm12
The following NEW packages will be installed:
  libllvm12
The following packages will be upgraded:
  libdrm-amdgpu1 libegl-mesa0 libgbm1 libgl1-mesa-dri libglapi-mesa libglx-mesa0 libxatracker2 mesa-va-drivers mesa-vdpau-drivers mesa-vulkan-drivers
10 upgraded, 1 newly installed, 0 to remove and 36 not upgraded.
```

Let's take a bit of time to understand this command-line:
- `-t kali-experimental`: ask APT to install packages from `kali-experimental`.
- `?upgradable`: select packages that can be upgraded (have a newer candidate).
- `?source-package("mesa|libdrm")`: select only packages that belong to the
  `mesa` and the `libdrm` source packages.

That's it, Kali Linux is now using Mesa 3D 21!
