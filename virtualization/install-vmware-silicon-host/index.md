---
title: Installing VMware on Apple Silicon (M1/M2) Macs (Host)
description:
icon:
weight: 105
author: ["gamb1t",]
---

{{% notice info %}}
You need to be on the 22H2 release of the VMware Technical Preview.
If you are not on at least Player Version e.x.p. (20191287) then you need to update.

Due to a limitation of the VMware updater software, if you are on an earlier version, it will report that there are no updates available. You need to go to VMware's website and download and install manually.
{{% /notice %}}

At the time of writing VMware is still offering Apple Silicon support through their Public Tech Preview for free. This is subject to change when Apple Silicon support is added to their main lineup, however until then the steps to get started are pretty straightforward.

We will first go to ~~[Get Fusion M1](https://www.vmware.com/go/get-fusion-m1) which will auto-redirect us to the most current Tech Preview~~ (At the time of writing this page is out of date, the current version is [22H2](https://customerconnect.vmware.com/downloads/get-download?downloadGroup=FUS-PUBTP-22H2)). From here we will want to download the first file, which is the `.dmg`.

![](install-silicon-vmware-1.png)

After it downloads we will just double-click on the file and we are presented with the following:

![](install-silicon-vmware-2.png)

We will double-click on this and after following the prompts we are all done and can use VMware's Public Tech Preview like normal.
