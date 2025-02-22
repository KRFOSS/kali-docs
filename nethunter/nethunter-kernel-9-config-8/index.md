---
title: Configuring the Kernel - CAN
description:
icon:
weight: 35
author: ["v0lk3n",]
---

### CAN support

CAN support will be needed for CAN Arsenal usage. Note that this documentations may be updated a lot since CAN Arsenal is in Experimental Version.

In section ***"Networking support"***:


- Select ***"CAN bus subsystem support"***
- Select ***"Network physical/parent device Netlink interface"***


![](1-kernel_can.png)


Under ***"CAN bus subsystem support --->"***

- Select ***"Raw CAN Protocol (raw access with CAN-ID filtering)"***
- Select ***"Broadcast Manager CAN Protocol (with content filtering)"***
- Select ***"CAN Gateway/Router (with netlink configuration)"***

![](2-kernel_can.png)


Under ***"CAN Device Drivers --->"***

- Select ***"Virtual Local CAN Interface (vcan)"***
- Select ***"Serial / USB serial CAN Adaptors (slcan)"***
- Select ***"Platform CAN drivers with Netlink support"***
- Select ***"CAN bit-timing calculation"***
- Select ***"Enable LED triggers for Netlink based drivers"***
- Select ***"Aeroflex Gaisler GRCAN and GRHCAN CAN devices"***
- Select ***"Xilinx CAN"***
- Select ***"Bosch C_CAN/D_CAN devices"***
- Select ***"Bosch CC770 and Intel AN82527 devices"***
- Select ***"IFI CAN_FD IP"***
- Select ***"Bosch M_CAN devices"***
- Select ***"Philips/NXP SJA1000 devices"***
- Select ***"Softing Gmbh CAN generic support"***

![](3-kernel_can.png)

Under ***"CAN SPI interfaces --->"***

- Select ***"Holt HI311x SPI CAN controllers"***
- Select ***"Microchip MCP251x SPI CAN controllers"***

![](4-kernel_can.png)

Under ***"CAN USB interfaces --->"***

- Select ***"EMS CPC-USB/ARM7 CAN/USB interface"***
- Select ***"ESD USB/2 CAN/USB interface"***
- Select ***"Geschwister Schneider UG interfaces"***
- Select ***"Kvaser CAN/USB interface"***
- Select ***"PEAK PCAN-USB/USB Pro interfaces for CAN 2.0b/CAN-FD"***
- Select ***"8 devices USB2CAN interface"***

![](5-kernel_can.png)


In Section ***"Networking Support"***

Under ***"Networking options"***

- Select ***"Virtual Socket protocol"***
- Select ***"NETLINK: socket monitoring interface"***

![](6-kernel_can.png)

Under ***"QoS and/or fair queueing"***

- Select ***"CAN Identifier"***

![](7-kernel_can.png)


In section ***"Device Drivers ---> USB support ---> USB Serial Converter support --->"*** :

- Select ***"USB Serial Console device support"***
- Select ***"USB Generic Serial Driver"***
- Select ***"USB Winchiphead CH341 Single Port Serial Driver"***
- Select ***"USB FTDI Single Port Serial Driver"***
- Select ***"USB Prolific 2303 Single Port Serial Driver"***

![](8-kernel_can.png)

### ELM327 

#### Kernel 6.0 or Higher

This driver has become an official part of Linux since v6.0

In Section ***"Networking support"***

Under ***" > CAN bus subsystem support >  CAN Device Drivers --->"***

- Select as module (\<M\>) ***Serial / USB serial ELM327 based OBD-II Interfaces (can327)***

#### Kernel 4.11 or Higher

For Kernel 4.11 or higher. You can add ELM327 driver by following these step :

- Go to root of your Kernel repository and run these commands

```
git submodule add https://github.com/V0lk3n/elmcan drivers/net/can/elmcan
cp drivers/net/can/elmcan/can327.c drivers/net/can/
```

- Edit ***drivers/net/can/Makefile*** and add the following line.

```
obj-$(CONFIG_CAN_CAN327)	+= can327.o
```

- Edit ***drivers/net/can/Kconfig*** and add the following config.

```
config CAN_CAN327
	tristate "Serial / USB serial ELM327 based OBD-II Interfaces (can327)"
	depends on TTY
	select CAN_RX_OFFLOAD
	help
	  CAN driver for several 'low cost' OBD-II interfaces based on the
	  ELM327 OBD-II interpreter chip.

	  This is a best effort driver - the ELM327 interface was never
	  designed to be used as a standalone CAN interface. However, it can
	  still be used for simple request-response protocols (such as OBD II),
	  and to monitor broadcast messages on a bus (such as in a vehicle).

	  Please refer to the documentation for information on how to use it:
	  Documentation/networking/device_drivers/can/can327.rst

	  If this driver is built as a module, it will be called can327.
```

Finally, build the kernel.

In Section ***"Networking support"***

Under ***"CAN bus subsystem support >  CAN Device Drivers --->***

- Select as module (\<M\>) ***Serial / USB serial ELM327 based OBD-II Interfaces (can327)***

#### Kernel Lower than 4.11

For Kernel lower than 4.11. You can add ELM327 driver by following these step :

- Go to root of your Kernel repository and run these commands

```
git submodule add -b linux-pre-4.11 https://github.com/V0lk3n/elmcan drivers/net/can/elmcan
```

- Edit ***drivers/net/can/Makefile*** and add the following line.

```
obj-y                           += elmcan/
```

Finally, build the kernel.

In Section ***"Networking support"***

Under ***"CAN bus subsystem support >  CAN Device Drivers --->***

- Select as module (\<M\>) ***Serial / Serial ELM327 driver***

### ISO 15765-2 Driver (CAN-ISOTP)

Go to your kernel sources folder and clone as submodule can-isotp driver.

```
git submodule add https://github.com/V0lk3n/can-isotp drivers/net/can/
```

Download ***"isotp.h"*** to ***"include/uapi/linux/can"***

```
cd include/uapi/linux/can
wget https://raw.githubusercontent.com/v0lk3n/can-isotp/refs/heads/master/include/uapi/linux/can/isotp.h
```

Edit drivers/net/can/Kconfig and add the following line :

```
source "drivers/net/can/can-isotp/Kconfig"
```

Edit drivers/net/can/Makefile and add the following line :

```
obj-y				+= can-isotp/
```

In Section ***"Networking Support"***

Under ***"CAN bus subsystem support ---> CAN Device Drivers"***

- Select as Module ***"CAN ISO 15765-2 driver"***

Save, Exit, then build!
