---
title: Waydroid on NetHunter Pro
description:
icon:
weight:
author: ["ShubhamVis98",]
---

### What is Waydroid

Waydroid is an open-source project that allows you to run the Android operating system in a container on a Linux system, effectively letting you run Android apps alongside Linux apps on the same device. It achieves this by using Linux containers (LXC) to isolate the Android environment from the host system, while still allowing interaction between the two.

### Install Instructions

{{< youtube BeCkoZ4EfOs >}}

**Install pre-requisites**
```
sudo apt install curl ca-certificates -y
```

**Add the official repository**
```
curl -sSL https://repo.waydro.id -o wd.sh
sudo bash wd.sh bookworm
```

**Install waydroid using apt**

If you see any package dependeny error while running `apt` command, try switching the suite to `trixie` at above step.

```
sudo apt install waydroid
sudo waydroid init
reboot
```

After reboot, you can start waydroid from Phosh app drawer.
