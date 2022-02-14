---
title: Configuring the 3.x Kernel - USB
description:
icon:
weight: 32
author: ["yesimxev",]
---

## Kernel Configuration cont.

**If your kernel is above 4.x please skip to the [next page](nethunter-kernel-7-config-6/)**

### USB Modem

CDC ACM support is required for Proxmark and similar devices

Navigate to ***Device Drivers -> USB support*** and select the following option:

- select ***"USB Modem (CDC ACM) support"***
  (CONFIG_USB_ACM=y)

![](nh-kernel-270-usb-1.png)

&nbsp;

### USB Gadget support

USB Gadget support is only possible with patches on 3.x kernels.

Please refer to [patching kernel](nethunter-kernel-1-patching/) page.

## Exit, save, and build
