---
title: Deploying Kali over Network PXE/iPXE Install
description:
icon:
date: 2019-10-26
type: post
weight: 45
author: ["g0tmi1k",]
tags: ["",]
keywords: ["",]
og_description:
---

## Setup a PXE Server

Booting and installing Kali over the network ([PXE](http://en.wikipedia.org/wiki/Preboot_Execution_Environment)) can be useful from a single laptop install with no CDROM or USB ports, to enterprise deployments supporting pre-seeding of the Kali installation.

{{% notice info %}}
You'll need to have root privileges to do this procedure, or the ability to escalate your privileges with the command "sudo su".
{{% /notice %}}

First, we need to install _dnsmasq_ to provide the DHCP/TFTP server and then edit the _dnsmasq.conf_ file.

```
apt install -y dnsmasq
nano /etc/dnsmasq.conf
```

In _dnsmasq.conf_, enable DHCP, TFTP and PXE booting and set the _dhcp-range_ to match your environment. If needed you can also define your gateway and DNS servers with the _dhcp-option_ directive as shown below:

```
interface=eth0
dhcp-range=192.168.101.100,192.168.101.200,12h
dhcp-boot=pxelinux.0
enable-tftp
tftp-root=/tftpboot/
dhcp-option=3,192.168.101.1
dhcp-option=6,8.8.8.8,8.8.4.4
```

With the edits in place, the dnsmasq service needs to be restarted in order for the changes to take effect.

```
service dnsmasq restart
```

### Download Kali PXE Netboot Images

Now, we need to create a directory to hold the Kali Netboot image and download the image we wish to serve from the Kali repos.

```
mkdir -p /tftpboot
cd /tftpboot
# for 64 bit systems:
wget http://http.kali.org/kali/dists/kali-rolling/main/installer-amd64/current/images/netboot/netboot.tar.gz
# for 32 bit systems:
wget http://http.kali.org/kali/dists/kali-rolling/main/installer-i386/current/images/netboot/netboot.tar.gz
tar zxpf netboot.tar.gz
rm netboot.tar.gz
```

## Configure Target to Boot From Network

With everything configured, you can now boot your target system and configure it to boot from the network. It should get an IP address from your PXE server and begin booting Kali.
