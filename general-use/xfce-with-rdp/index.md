---
title: Setting up RDP with Xfce
description:
icon:
type: post
weight:
author: ["gamb1t",]
---

Kali Linux is supported on many different devices and systems. On some of those systems, you may only get a bare bones install and occasionally may not have direct access to a GUI such as with WSL. One simple way to get access to a GUI for Kali is by installing Xfce and setting up RDP. This can be done either manually or with the script provided [here](https://gitlab.com/kalilinux/build-scripts/kali-wsl-chroot/-/blob/master/xfce4.sh), and can be seen below.

```
#!/bin/sh

echo "[+] Installing Xfce, this will take a while"
apt-get update
apt-get dist-upgrade -y --force-yes
apt-get install --yes --force-yes kali-desktop-xfce xorg xrdp

echo "[+] Configuring XRDP to listen to port 3390 (but not starting the service)..."
sed -i 's/port=3389/port=3390/g' /etc/xrdp/xrdp.ini
```

To use the script we do the following:

```console
kali@kali:~$ wget https://gitlab.com/kalilinux/build-scripts/kali-wsl-chroot/-/raw/master/xfce4.sh
kali@kali:~$
kali@kali:~$ chmod +x xfce4.sh
kali@kali:~$
kali@kali:~$ sudo ./xfce4.sh
kali@kali:~$
```

Setting this up manually will provide more control over what configuration is done, but also will take a bit longer. After you set up Xfce and RDP, you need to start the service and connect. To start the service you will need to run the following:

```console
kali@kali:~$ sudo systemctl enable xrdp --now
kali@kali:~$
```

You can then connect with a RDP client to that system. Keep in mind the port that is being used. If you used the script, the port would be 3390. In the case of WSL, the IP would be 127.0.0.1:3390 that you would wish to connect to from your windows system.
