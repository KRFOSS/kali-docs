---
title: AWS
description:
icon:
date: 2020-08-04
type: post
weight:
author: ["gamb1t",]
tags: ["",]
keywords: ["",]
og_description:
---

## Connecting to the AWS instance

After configuring the official Kali Linux image you can connect to the instance by using the `kali` user. After connecting, a password change through `sudo passwd kali` is possible if needed.

## After connecting

After connection a user may realize that the image is quite sparse. This is to allow for customization and reduced image size. To get the default Kali tool set we can utilize [Kali's metapackages](https://www.kali.org/docs/general-use/metapackages/). Alternatively, we can install specific tools as they are needed.

If someone would like to use a GUI, they can do this through SSH forwarding. We have two options, the first is to use `ssh -X` to forward X11 and use GUI applications one at a time, or we can use RDP and forward the traffic over SSH. To set up RDP, we will run the [RDP with Xfce](https://www.kali.org/docs/general-use/xfce-with-rdp/) script used for WSL. After this, we can tunnel with `ssh -N -L 3390:127.0.0.1:3390` and connect using any remote desktop client to `127.0.0.1:3390`.

Another common utility is to use GPUs for cracking. This can be done as well through the AWS instance, however we must be careful to install the Nvidia packages after everything is up to date and the proper Linux headers are installed:

```
kali@kali:~$ sudo apt update && sudo apt dist-upgrade -y
kali@kali:~$
kali@kali:~$ sudo apt install linux-headers-5.7.0-kali3-cloud-amd64
kali@kali:~$
kali@kali:~$ sudo reboot
kali@kali:~$
kali@kali:~$ sudo apt install nvidia-driver nvidia-cuda-toolkit
kali@kali:~$
kali@kali:~$ sudo reboot
kali@kali:~$
```