---
title: Converting VMX to an OVA
description:
icon:
type: post
weight: 400
author: ["gamb1t",]
---

VMware has **VMX format** which works for VMware products. The other commonly found format which is **OVF**, as this is a open standard (**OVA** is OVF but compressed into a single file). There are times where converting between the two formats is required.

In order to convert from VMware's VMX to a OVA file, we will use [ovftool](https://code.vmware.com/web/tool/4.4.0/ovf). If you already have either [VMware workstation](/docs/virtualization/install-vmware-host/) or VMware fusion pre-installed, you may already have it (else it can be downloaded as a standalone program for free). Its default locations are:

- Linux: `/usr/bin/ovftool`
- OS X/macOS: `/Applications/VMware Fusion.app/Contents/Library/VMware OVF Tool/ovftool`
- Windows: `C:\Program Files (x86)\VMware\VMware Workstation\OVFTool\ovftool.exe`

- - -

We will be using the [official Kali Linux VMWare image](https://www.offensive-security.com/kali-linux-vm-vmware-virtualbox-image-download/) to convert. To start with unzip it to get access to the vmx within.

```console
kali@kali:~$ 7z x kali-linux-2020.4-vmware-amd64.7z
...
kali@kali:~$
kali@kali:~$ ls Kali-Linux-*-vmware-amd64.vmwarevm/*vmx
Kali-Linux-2020.4-vmware-amd64.vmwarevm/Kali-Linux-2020.4-vmware-amd64.vmx
kali@kali:~$
```

- - -

We can now start to convert by doing the following.

```console
kali@kali:~$ ovftool Kali-Linux-*-vmware-amd64.vmwarevm/*vmx Kali-Linux-rolling-amd64.ova
Opening VMX source: Kali-Linux-2020.4-vmware-amd64.vmwarevm/Kali-Linux-2020.4-vmware-amd64.vmx
Opening OVA target: Kali-Linux-rolling-amd64.ova
Writing OVA package: Kali-Linux-rolling-amd64.ova
...
Transfer Completed
Completed successfully
kali@kali:~$
kali@kali:~$ file kali-linux-rolloing.ova
kali-linux-rolloing.ova: POSIX tar archive
kali@kali:~$ ls -lah kali-linux-rolloing.ova
-rw-r--r-- 1 kali kali 3.4G Nov 10 23:18 kali-linux-rolloing.ova
kali@kali:~$
```
- - -

Thats it!

The OVA file can be now move and used where needed such as to ESXi _(if the in-built upload feature is not working)_ or migrate to VirtualBox _(You may need to install [Guest Addition](/docs/virtualization/install-virtualbox-guest-additions/) if so)_.
