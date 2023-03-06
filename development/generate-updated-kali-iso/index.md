---
title: Generate an Updated Kali ISO
description:
icon:
weight: 50
author: ["g0tmi1k",]
---

Kali Linux allows you to generate updated ISOs of Kali using Debian [live-build](http://live.debian.net/devel/live-build/) scripts on the fly. The easiest way to generate these images is **from within a pre existing Kali Linux environment**.
You will first need to install the `live-build` and `cdebootstrap` packages:

```console
kali@kali:~$ sudo apt update
kali@kali:~$ sudo apt install -y git live-build cdebootstrap
```

Next, we clone the Kali `cdimage` Git repository as follows:

```console
kali@kali:~$ git clone git://gitlab.com/kalilinux/build-scripts/live-build-config.git
```

Now you can change to the `live` directory under `cdimage.kali.org` and build your ISO:

```console
kali@kali:~$ cd live-build-config/
kali@kali:~$ lb clean --purge
kali@kali:~$ lb config
kali@kali:~$ lb build
```

{{% notice info %}}
The live build scripts allow for complete customization of Kali Linux images. For more information about Kali live build scripts, check out our <a href=/docs/development/live-build-a-custom-kali-iso/>Kali customization page</a>.
{{% /notice %}}
