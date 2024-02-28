---
title: Import Pre-Made Kali VMware VM
description:
icon:
weight: 206
author: ["gamb1t",]
---

Importing the [Kali VMware image](/get-kali/#kali-virtual-machines) is very easy.

We first need to extract the VMware image:

```console
kali@kali:~$ 7z x kali-linux-2024.1-vmware-amd64.7z
[...]
kali@kali:~$
```

If we are using Windows we can instead use the [official 7z app](https://www.7-zip.org/). Note if we are on Windows 11 the option will be hidden behind the context menu "Show more options".

We then launch VMware:

![](import-vmware-1.png)

From here we will be wanting to select 'Open a Virtual Machine'. We then navigate to the location our VM is downloaded and find the `.vmx` file:

![](import-vmware-2.png)

We select this then we are able to continue forward:

![](import-vmware-3.png)

We can verify the settings that will be set here, and if we need to change any we can. Once we are happy we can select boot the VM and use it as normal. Remember the default login is kali for the user and kali for the password!
