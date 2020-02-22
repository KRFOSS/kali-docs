---
title: Generate an Updated Kali ISO
description:
icon:
date: 2020-01-16
type: post
weight: 100
author: ["g0tmi1k",]
tags: ["",]
keywords: ["",]
og_description:
---

Kali Linux allows you to generate updated ISOs of Kali using Debian [live-build](http://live.debian.net/devel/live-build/) scripts on the fly. The easiest way to generate these images is **from within a pre existing Kali Linux environment**.
You will first need to install the _live-build_ and _cdebootstrap_ packages:

```markdown
sudo apt install -y git live-build cdebootstrap
```

Next, we clone the Kali _cdimage_ Git repository as follows:

```markdown
git clone git://gitlab.com/kalilinux/build-scripts/live-build-config.git
```

Now you can change to the _live_ directory under _cdimage.kali.org_ and build your ISO.

```markdown
cd live-build-config
lb clean --purge
lb config
lb build
```

{{% notice info %}}
The live build scripts allow for complete customization of Kali Linux images. For more information about Kali live build scripts, check out our <a href=/docs/development/live-build-a-custom-kali-iso/>Kali customization page</a>.
{{% /notice %}}
