---
title: Configuring the 4.x Kernel - USB
description:
icon:
weight: 34
author: ["re4son",]
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

Please note that kernels above 4.x version (in most cases) don't need any patches, we can simply enable in kernel config as seen below.

Generic serial, CDC ACM, CDC ECM, and HID are required for various USB based attacks.

Navigate to ***Device Drivers -> USB support -> USB Gadget Support*** and choose:

- select ***"Generic serial bulk in/out"***
  (CONFIG_USB_CONFIGFS_SERIAL=y)
- select ***"Abstract Control Model (CDC ACM)"***
  (CONFIG_USB_CONFIGFS_ACM=y)
- select ***"Object Exchange Model (CDC OBEX)"***
  (CONFIG_USB_CONFIGFS_OBEX=y)
- select ***"Network Control Model (CDC NCM)"***
  (CONFIG_USB_CONFIGFS_NCM=y)
- select ***"Ethernet Control Model (CDC ECM)"***
  (CONFIG_USB_CONFIGFS_ECM=y)
- select ***"Ethernet Control Model (CDC ECM) subset"***
  (CONFIG_USB_CONFIGFS_ECM_SUBSET=y)
- select ***"RNDIS"***
  (CONFIG_USB_CONFIGFS_RNDIS=y)
- select ***"Ethernet Emulation Model (EEM)"***
  (CONFIG_USB_CONFIGFS_EEM=y)
- select ***"Mass Storage"***
  (CONFIG_USB_CONFIGFS_MASS_STORAGE=y)
- select ***"HID function"***
  (CONFIG_USB_CONFIGFS_F_HID=y)

![](nh-kernel-280-usb-2.png)

&nbsp;

## Exit, save, and build
