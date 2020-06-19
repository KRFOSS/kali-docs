---
title: Converting to an OVA
description:
icon:
date: 2020-06-19
type: post
weight:
author: ["gamb1t",]
tags: ["",]
keywords: ["",]
og_description:
---
The process to convert a vmx file to ova is not too complicated, however it does take some time to get to that point. The first thing that must be done is to download the [ovftool](https://code.vmware.com/web/tool/4.4.0/ovf) which is free after signing in. After ovftool is downloaded, we can download the [official Kali Linux VMWare](https://www.offensive-security.com/kali-linux-vm-vmware-virtualbox-image-download/) file and unzip it to get access to the vmx within. Then we just run the tool as follows:

```
kali@kali:~$ ./ovftool Downloads/Kali-Linux-2020.2-amd64.vmwarevm/Kali-Linux-2020.2-vmware-amd64.vmx kali.ova
Opening VMX source: /home/kali/Downloads/Kali-Linux-2020.2-amd64.vmwarevm/Kali-Linux-2020.2-vmware-amd64.vmx
Opening OVA target: /home/kali/kali.ova
Writing OVA package: /home/kali/kali.ova
Transfer Completed
Completed successfully
kali@kali:~$
```

Then you can use the resulting ova file as you would normally with VirtualBox.
