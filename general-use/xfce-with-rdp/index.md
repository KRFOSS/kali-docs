---
title: Setting up RDP with Xfce
description:
icon:
weight:
author: ["gamb1t",]
---

Kali Linux is supported on many different devices and systems. On some of those systems, you may only get a bare bones install and occasionally may not have direct access to a GUI such as with WSL or Docker. One simple way to get access to a GUI for Kali is by installing Xfce and setting up RDP. This can be done either manually or with the script provided [here](https://gitlab.com/kalilinux/build-scripts/kali-wsl-chroot/-/blob/master/xfce4.sh), and can be seen below.

```plaintext
#!/bin/sh
echo "[+] Installing Xfce, this will take a while"
apt-get update
apt-get dist-upgrade -y
apt-get install -y kali-desktop-xfce xorg xrdp

echo "[+] Configuring XRDP to listen to port 3390 (but not starting the service)..."
sed -i 's/port=3389/port=3390/g' /etc/xrdp/xrdp.ini
```

Before we can start the process of setting up Xfce and RDP, we must first acknowledge some differences with certain systems Kali is on. The first is Docker. To use this setup with Docker, we must supply a launch command like the following:

`docker run -p 3390:3390 --expose=3390 --tty --interactive kalilinux/kali-rolling /bin/bash`

For additional usage on Docker, such as how to resume an exited container, please read [using Kali Docker images](/docs/containers/using-kali-docker-images).

For AWS, we must be sure to allow our IP to access the proper ports when we set up the machine.

To use the script we do the following:

```console
# If on Docker, run the following command first:

┌──(root㉿182156129)-[/]
└─$ apt update && DEBIAN_FRONTEND=noninteractive apt install -y wget kali-linux-headless

# Once complete, continue. Otherwise run the following:

┌──(kali㉿kali)-[~]
└─$ wget https://gitlab.com/kalilinux/build-scripts/kali-wsl-chroot/-/raw/master/xfce4.sh

┌──(kali㉿kali)-[~]
└─$

┌──(kali㉿kali)-[~]
└─$ chmod +x xfce4.sh

┌──(kali㉿kali)-[~]
└─$

┌──(kali㉿kali)-[~]
└─$ sudo ./xfce4.sh

┌──(kali㉿kali)-[~]
└─$
```

Setting this up manually will provide more control over what configuration is done, but also will take a bit longer.

If you are using WSL, dbus-x11 needs to be installed next for xrdp and xfce to connect.

```console
┌──(kali㉿kali)-[~]
└─$ sudo apt install -y dbus-x11

┌──(kali㉿kali)-[~]
└─$
```

After you set up Xfce and RDP, you need to start the service:

```console
# If on AWS

┌──(kali㉿kali)-[~]
└─$ sudo systemctl enable xrdp --now

┌──(kali㉿kali)-[~]
└─$

# If on WSL or Docker

┌──(kali㉿kali)-[~]
└─$ sudo /etc/init.d/xrdp start

┌──(kali㉿kali)-[~]
└─$
```

In the case of AWS, you will need to change the password to the default 'kali' account before connecting. This can be done with the following command:

```console
┌──(kali㉿kali)-[~]
└─$ echo kali:kali | sudo chpasswd

┌──(kali㉿kali)-[~]
└─$
```

If you are using Docker, you will need to create a new user. You can do this with adduser

```console
┌──(kali㉿kali)-[~]
└─$ adduser kali
...

┌──(kali㉿kali)-[~]
└─$
```

You can then connect with a RDP client to that system. Keep in mind the port that is being used. If you used the script, the port would be 3390. In the case of WSL and Docker, the IP would be 127.0.0.1:3390 that you would wish to connect to from your windows system (or the host systems IP from a separate computer). In the case of AWS, the IP would be the same as you use to connect via SSH.

{{% notice info %}}

You may encounter the error `Authentication Required to Create Managed Color Device` when trying to connect. Do the following to fix this issue.

{{% /notice %}}

```
┌──(kali㉿kali)-[~]
└─$ cat <<EOF | sudo tee /etc/polkit-1/localauthority/50-local.d/45-allow-colord.pkla
[Allow Colord all Users]
Identity=unix-user:*
Action=org.freedesktop.color-manager.create-device;org.freedesktop.color-manager.create-profile;org.freedesktop.color-manager.delete-device;org.freedesktop.color-manager.delete-profile;org.freedesktop.color-manager.modify-device;org.freedesktop.color-manager.modify-profile
ResultAny=no
ResultInactive=no
ResultActive=yes
EOF
┌──(kali㉿kali)-[~]
└─$
```
