---
title: NetHunter CAN-Arsenal
description:
icon:
weight:
author: ["v0lk3n",]
---

CAN-Arsenal is used to communicate with your car, for testing, diagnostics or car hacking.


## Menu

![](nethunter-btarsenal1.png)

### Documentation

This button will redirect to the following documentation.

### Setup

This button will install needed CAN tools and packages. Note that it shouldn't be needed as it should be launched at first run of CAN Arsenal.

### Update

This button will update the installed CAN tools and packages.


## Settings

![](nethunter-btarsenal1.png)

Settings are used to configure CAN Arsenal toolset.

## Interface

![](nethunter-btarsenal1.png)

Interface section is used to Start or Stop CAN, VCAN or SLCAN interface.

### CAN

***Start CAN Interface - Settings Prerequisite :*** 

Set "CAN Interface" and "UART Speed" in Settings

> CAN Interface should respect the following format : can0-9 (such as can0, can1, can2...)


***Start CAN Interface - Used command :***

```bash
sudo ip link set <CAN Interface> type can bitrate <UART Speed>
sudo ip link set <CAN Interface> up
```


***Stop CAN Interface - Settings Prerequisite :*** 

Set "CAN Interface" in Settings

> CAN Interface should respect the following format : can0-9 (such as can0, can1, can2...)


***Stop CAN Interface - Used command :***

```bash
sudo ip link set <CAN Interface> down
```

### VCAN

***Start VCAN Interface - Settings Prerequisite :*** 

Set "CAN Interface" and "MTU" in Settings

> VCAN Interface should respect the following format : vcan0-9 (such as vcan0, vcan1, vcan2...)


***Start VCAN Interface - Used command :***

```bash
ip link add dev <CAN Interface> type vcan
ip link set <CAN Interface> mtu <MTU>
ip link set <CAN Interface> up
```


***Stop VCAN Interface - Settings Prerequisite :*** 

Set "CAN Interface" in Settings

> VCAN Interface should respect the following format : vcan0-9 (such as vcan0, vcan1, vcan2...)

***Stop VCAN Interface - Used command :***

```bash
sudo ip link set <CAN Interface> down 
sudo ip link delete <CAN Interface>
```

### SLCAN

***Start SLCAN Interface - Settings Prerequisite :*** 

Set "CAN Interface", "USB Device", "CAN Speed", "UART Speed" and "Flow Control" in Settings

> SLCAN Interface should respect the following format : slcan0-9 (such as slcan0, slcan1, slcan2...)

> CAN USB Adapter should be plugged in your device and hit refresh button to set USB Device with you'r plugged adapter.


***Start SLCAN Interface - Used command :***

```bash
sudo slcan_attach -f -s<CAN Speed> -o <USB Device>
sudo slcand -o -s<CAN Speed> -t <Flow Control> -S <UART Speed> <USB Device> <CAN Interface>
sudo ip link set <CAN Interface> up
```


***Stop SLCAN Interface - Settings Prerequisite :*** 

Set "CAN Interface" and "USB Device" in Settings.

> SLCAN Interface should respect the following format : slcan0-9 (such as slcan0, slcan1, slcan2...)

> CAN USB Adapter should be plugged in your device and hit refresh button to set USB Device with you'r plugged adapter.


***Stop SLCAN Interface - Used command :***

```bash
sudo ip link set <CAN Interface> down
sudo slcan_attach -d <USB Device>
```


## Tools

![](nethunter-btarsenal1.png)


### Can-Utils : CanGen

Used to generate CAN Traffic.


***CanGen - Settings Prerequisite :*** 

Your desired CAN Interface should be started and set in Settings.


***CanGen - Used command :***

```bash
cangen <CAN Interface> -v
```


### Can-Utils : CanSniffer

Used to sniff CAN Traffic.


***CanSniffer - Settings Prerequisite :*** 

Your desired CAN Interface should be started and set in Settings.


***CanSniffer - Used command :***

```bash
cansniffer <CAN Interface>
```


### Can-Utils : CanDump

Used to dump CAN traffic to an output file.


***CanDump - Settings Prerequisite :*** 

Your desired CAN Interface should be started and set with "Output" path in Settings. 


***CanDump - Used command :***

```bash
candump <CAN Inteface> -f <Output Log>
```


### Can-Utils : CanSend

Used to replay a specific sequence to car.


***CanSend - Settings Prerequisite :*** 

Your desired CAN Interface should be started and set with "Sequence" in Settings. 

***CanSend - Used command :***

```bash
cansend <CAN Interface> <Sequence>
```

### Can-Utils : CanPlayer

Used to replay dumped sequences from a log file to car.

Your desired CAN Interface should be started and set with "Input" path in Settings. 

> CAN Interface will be taken from the Input Log, check that your interface is the same one. (If you dump with vcan0, you should replay with vcan0)

```bash
canplayer -I <Input Log>
```

### Custom Script : SequenceFinder

<a href="https://raw.githubusercontent.com/V0lk3n/NetHunter-CANArsenal/refs/heads/main/sequence_finder.sh">You can see the source code here.</a>

Used to find the exact sequence of the desired action from a log file.

>This custom script will auto split a log files using head and tail. Replay theses with user input in loop using CanPlayer, until finding the exact sequence of the desired action. Finally it replay it using CanSend.


***SequenceFinder - Settings Prerequisite :*** 

Your desired CAN Interface should be started and set with "Input" path in Settings. 

> CAN Interface will be taken from the Input Log, check that your interface is the same one. (If you dump with vcan0, you should replay with vcan0)


***SequenceFinder - Used command :***

```bash
/opt/car_hacking/sequence_finder.sh <Input Log>
```


## Cannelloni

Used to communicate with two machine on a CAN interface by Ethernet.


***SequenceFinder - Settings Prerequisite :*** 

Your desired CAN Interface should be set in Settings.

In Cannelloni, "RHOST", "RPORT" and "LPORT" need to be set.

> Both device should be linked using an Ethernet Cable.

```bash
sudo cannelloni -I <CAN Interface> -R <RHOST> -r <RPORT> -l <LPORT>
```


## Logging

<img src="/img/07-logging.jpg" alt="menu" width="500"/>

#### Asc2Log

Command to run Asc2Log to convert ASC format file to classic log file :

Prerequisite : Set "Input" and "Output" path in Settings.

```bash
asc2log -I <Input Log> -O <Output File>
```


#### Log2Asc

Log2Asc is used to convert dumped LOG file to the ASC format :

Prerequisite : Set "Input", "Output" path and "CAN Interface" in Settings.

```bash
log2asc -I <Input Log> -O <Output File> <CAN Interface>
```


## Custom Command

![](nethunter-btarsenal1.png)

Used in case you need to run a specific command which doesnt match the one provided.



