---
title: Configuring the 3.x Kernel - USB
description:
icon:
weight:
author: ["yesimxev",]
---

## Kernel Configuration cont.

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
