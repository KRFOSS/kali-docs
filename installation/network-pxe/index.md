---
title: Deploying Kali over Network PXE/iPXE Install
description:
icon:
weight: 400
author: ["g0tmi1k",]
---

Booting and installing Kali Linux over the network ([PXE](https://en.wikipedia.org/wiki/Preboot_Execution_Environment)) can be useful from a single laptop install with no CDROM or [USB](/docs/usb/) ports, to enterprise deployments supporting pre-seeding of the Kali Linux installation.

### Setup a PXE Server

First, we need to install **[dnsmasq](https://packages.debian.org/testing/dnsmasq)** to provide the DHCP/TFTP server and then edit the `dnsmasq.conf` file:

```console
kali@kali:~$ sudo apt install -y dnsmasq
[...]
kali@kali:~$
kali@kali:~$ sudo vim /etc/dnsmasq.conf
kali@kali:~$
```

- - -

In `dnsmasq.conf`, enable **DHCP**, **TFTP** and **PXE booting** and set the `dhcp-range` to match your environment (we are using **192.168.101.100-200**). If needed you can also define your gateway and DNS servers with the `dhcp-option` directive as shown below:

```plaintext
interface=eth0
dhcp-range=192.168.101.100,192.168.101.200,12h
dhcp-boot=pxelinux.0
enable-tftp
tftp-root=/tftpboot/
dhcp-option=3,192.168.101.1
dhcp-option=6,8.8.8.8,8.8.4.4
```

- - -

With the edits in place, the dnsmasq service needs to be restarted in order for the changes to take effect.

```console
kali@kali:~$ sudo systemctl restart dnsmasq
kali@kali:~$
```

#### Download Kali PXE Netboot Images

Now, we need to create a directory to hold the Kali Linux Netboot image and download the image we wish to serve.

```console
kali@kali:~$ sudo mkdir -p /tftpboot/
kali@kali:~$

# 64-bit:
kali@kali:~$ sudo wget http://http.kali.org/kali/dists/kali-rolling/main/installer-amd64/current/images/netboot/netboot.tar.gz -P /tftpboot/
# 32-bit:
kali@kali:~$ sudo wget http://http.kali.org/kali/dists/kali-rolling/main/installer-i386/current/images/netboot/netboot.tar.gz -P /tftpboot/

kali@kali:~$ sudo tar -zxpf /tftpboot/netboot.tar.gz -C /tftpboot
kali@kali:~$
kali@kali:~$ sudo rm -f /tftpboot/netboot.tar.gz
kali@kali:~$
```

### Configure Target to Boot From Network

With everything configured, you can now boot your target system and configure it to boot from the network. It should get an IP address from your PXE server and begin booting Kali Linux.

### Post Installation

Now that you've completed installing Kali Linux, it's time to customize your system. The [General Use section](/docs/general-use/) has more information and you can also find tips on how to get the most out of Kali Linux in our [User Forums](https://forums.kali.org/).

One last thing we need to do if we want to use this system in the future is set up a cron job to pull in the new Netboot images regularly in case of kernel updates. We will create a simple script for this purpose named pxe.sh:

```plaintext
#!/bin/sh

## We input our desired path for the PXE image to be saved to
tftp=/tftpboot
arch=amd64

## We remove the previous directory containing the PXE image and download the newest version
rm -rf $tftp/
mkdir -p $tftp/
wget http://http.kali.org/kali/dists/kali-rolling/main/installer-$arch/current/images/netboot/netboot.tar.gz -P $tftp/
tar -zxpf /tftpboot/netboot.tar.gz -C $tftp
rm -f $tftp/netboot.tar.gz
```

We save this script to `/opt` and are sure to set it's permissions so you can only edit it with **root** or **sudo**. An example of this is to set the file to `770` or `700` with chmod, and set it to `root:root` with chown.

We can now add it to a cron job.

```console
kali@kali:~$ sudo crontab -e
...
0 5 * * 2 /opt/tftpboot.sh
...
kali@kali:~$
```