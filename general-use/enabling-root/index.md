---
title: Enabling Root
description:
icon:
date: 2021-03-30
type: post
weight:
author: ["gamb1t",]
tags: ["",]
keywords: ["",]
og_description:
---

## Permanent vs temporary usage

There are some cases where you may need to use superuser, root, for an extended period of time. In these cases we can easily access the root account with a simple `sudo su` (which will ask for the current user's password), selecting the root terminal icon in the Kali menu, or alternatively using `su -` (which will ask for the root user's password) if you have set a password for the root account that you know of. When finished, `exit` or CTRL+D will take us out of this elevated shell.

However, there may be other times where you may want to use root across multiple sessions without the hassle of elevating privileges. In these situations we will need to install a package and make a few modifications to fully enable the root account for use due to security reasons of keeping the root account disabled by default.

## Enabling the root account

The first thing to do is set a root password, which should be different to the current user's password ([in this case `kali`](/docs/introduction/default-credentials/)). We can do this by doing the following:

```console
kali@kali:~$ sudo passwd
[sudo] password for kali:
New password:
Retype new password:
passwd: password updated successfully
kali@kali:~$
```

{{% notice info %}}
Please note that the password prompt will not display output as you are typing in the password, but it will still register the keystrokes.
{{% /notice %}}

The next thing we need to decide is if we are wanting to use root via SSH or through the login prompt on whichever desktop environment is installed.

### Enabling root for SSH

If we look at `/etc/ssh/sshd_config` we will see a **PermitRootLogin** line. We will want to change this line to match our use case.

```console
kali@kali:~$ grep PermitRootLogin /etc/ssh/sshd_config
#PermitRootLogin prohibit-password
# the setting of "PermitRootLogin without-password".
kali@kali:~$
kali@kali:~$ man sshd_config | grep -C 1 prohibit-password
     PermitRootLogin
             Specifies whether root can log in using ssh(1).  The argument must be yes, prohibit-password, forced-commands-only, or no.  The default
             is prohibit-password.

             If this option is set to prohibit-password (or its deprecated alias, without-password), password and keyboard-interactive authentication
             are disabled for root.
kali@kali:~$
kali@kali:~$ sudo systemctl restart ssh
kali@kali:~$
```

If we have set up SSH key based login for the root account, then we can simply uncomment the appropriate line and continue on. Otherwise, we should change **PermitRootLogin** to be **yes** which will allow us to input a password.

### Enabling root for GNOME login

We will first install `kali-root-login` to change multiple configuration files that will permit us to login to the root account through the GNOME GDM3 login prompt. This step is not necessary when using other desktop environments.

```console
kali@kali:~$ sudo apt -y install kali-root-login
Reading package lists... Done
Building dependency tree
Reading state information... Done
The following NEW packages will be installed:
  kali-root-login
0 upgraded, 1 newly installed, 0 to remove and 1516 not upgraded.
Need to get 6,776 B of archives.
After this operation, 33.8 kB of additional disk space will be used.
Get:1 http://kali.download/kali kali-rolling/main amd64 kali-root-login all 2019.4.0 [6,776 B]
Fetched 6,776 B in 1s (10.9 kB/s)
Selecting previously unselected package kali-root-login.
(Reading database ... 333464 files and directories currently installed.)
Preparing to unpack .../kali-root-login_2019.4.0_all.deb ...
Adding 'diversion of /etc/gdm3/daemon.conf to /etc/gdm3/daemon.conf.original by kali-root-login'
Adding 'diversion of /etc/pam.d/gdm-password to /etc/pam.d/gdm-password.original by kali-root-login'
Adding 'diversion of /etc/pam.d/gdm-autologin to /etc/pam.d/gdm-autologin.original by kali-root-login'
Adding 'diversion of /etc/pam.d/lightdm-autologin to /etc/pam.d/lightdm-autologin.original by kali-root-login'
Adding 'diversion of /etc/pam.d/sddm to /etc/pam.d/sddm.original by kali-root-login'
Adding 'diversion of /etc/sddm.conf to /etc/sddm.conf.original by kali-root-login'
Unpacking kali-root-login (2019.4.0) ...
Setting up kali-root-login (2019.4.0) ...
Installing /usr/share/kali-root-login/daemon.conf as /etc/gdm3/daemon.conf
Installing /usr/share/kali-root-login/gdm-password as /etc/pam.d/gdm-password
Installing /usr/share/kali-root-login/gdm-autologin as /etc/pam.d/gdm-autologin
Installing /usr/share/kali-root-login/lightdm-autologin as /etc/pam.d/lightdm-autologin
Installing /usr/share/kali-root-login/sddm as /etc/pam.d/sddm
Installing /usr/share/kali-root-login/sddm.conf as /etc/sddm.conf
kali@kali:~$
```

We can now log out of our non-root user account and login to root using the password that we set earlier.