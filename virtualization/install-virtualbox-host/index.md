---
title: Installing VirtualBox on Kali (Host)
description:
icon:
type: post
weight: 110
author: ["g0tmi1k",]
---

You can install VirtualBox **on** Kali Linux, allowing you to use virtual machines (VMs) inside of Kali Linux. However if you are wanting to install Kali Linux **as** a VM, you want our [Kali Linux Guest VirtualBox](/docs/virtualization/install-virtualbox-guest-vm/) guide.

VMs are great. There are many upsides with using them. One of the being, able to run multiple Operating Systems (OSs) at the same time. You can have your host machine "untouched", and then only interact with the guest VMs. Another is when something is going right, take a snapshot. When something goes wrong, revert back.

VirtualBox is free and open source. There are a few other software such as QEMU, KVM/Xen with virt-manager. Then there is also [VMware Workstation & Fusion](/docs/virtualization/install-vmware-host/), which is a commercial software (there is VMware Player which is free but it is limited in features).

### Preparation

Before trying to install VirtualBox, please make sure your version of Kali Linux is [up-to-date](/docs/general-use/updating-kali/), and if required, reboot the machine.

```console
kali@kali:~$ sudo apt update
...
kali@kali:~$
kali@kali:~$ sudo apt full-upgrade -y
...
kali@kali:~$
kali@kali:~$ [ -f /var/run/reboot-required ] && sudo reboot -f
kali@kali:~$
```

### Download

The first thing we are going to do is import VirtualBox's repository key.

```console
kali@kali:~$ wget -q https://www.virtualbox.org/download/oracle_vbox_2016.asc -O- \
  | sudo apt-key add -
...
kali@kali:~$
```

- - -

We then move onto adding VirtualBox's repository.
We add this to a separate file, so it does not interfere with [Kali Linux's main repository](/docs/general-use/kali-linux-sources-list-repositories/).
Our CPU architecture is amd64. You may need to later the example below if yours is different.

One thing to bare in mind, [Kali Linux is based on Debian](/docs/policy/kali-linux-relationship-with-debian/), so we need to use [Debian's current stable version](https://www.debian.org/releases/stable/) (even though Kali Linux is a [rolling distribution](/docs/general-use/kali-branches/)). At the time of writing, its "buster":

```console
kali@kali:~$ echo "deb [arch=amd64] http://download.virtualbox.org/virtualbox/debian $( lsb_release -cs ) contrib" \
  | sudo tee /etc/apt/sources.list.d/virtualbox.list
kali@kali:~$
```

- - -

As we have altered our network repository, we need to re-build the cache.

```console
kali@kali:~$ sudo apt update
...
kali@kali:~$
```

- - -

As VirtualBox has various kernel modules (e.g. `vboxdrv`, `vboxnetflt` and `vboxnetadp`), we need to make sure they are kept up-to-date when Kali Linux's kernel gets updated. This can be achieved using [dkms](https://packages.debian.org/testing/dkms).

```console
kali@kali:~$ sudo apt install -y dkms
...
kali@kali:~$
```

### Setup

Now its time to install VirtualBox itself (along with its Extension Pack to expand VirtualBox's advanced features).

```console
kali@kali:~$ sudo apt install -y virtualbox virtualbox-ext-pack
...
kali@kali:~$
```

- - -

When prompted, read and accept the license.

You can now find VirtualBox in the menu or start it via the command line.

```console
kali@kali:~$ virtualbox
kali@kali:~$
```

If you now wish, you are able to [install Kali Linux in a VirtualBox VM](/docs/virtualization/install-virtualbox-guest-vm/) (on Kali Linux).
