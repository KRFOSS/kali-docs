---
title: NetHunter Rootless
description:
icon:
date: 2019-12-20
type: post
weight: 100
author: ["re4son",]
tags: ["",]
keywords: ["",]
og_description:
---

## NetHunter Rootless Edition

##### *Maximum flexibility with no commitment*  

Install Kali NetHunter on any stock, unrooted Android device without voiding the warranty.  

[![](images/010-NH-Rootless-Installation_Start_s.jpg)](images/010-NH-Rootless-Installation_Start.jpg)

[![](images/020-NH-Rootless-KeX_s.jpg)](images/020-NH-Rootless-KeX_s.jpg)

  

Prerequisite:  
--------------  

Android Device  
(Stock unmodified device, no root or custom recovery required)  

  

Installation:  
--------------  

* Install the NetHunter-Store app from https://store.nethunter.com  
* From the NetHunter Store, install __Termux__, __NetHunter-KeX client__, and __Hacker's keyboard__  
  _Note:_  
       _The button "install" may not change to "installed" in the store client after installation - just ignore it._  
      _Starting termux for the first time may seem stuck while displaying "installing" on some devices - just hit enter._ 
  
* Open Termux and type:  
  `termux-setup-storage`  
  `pkg install wget`   
  `wget -O install-nethunter-termux https://offs.ec/2MceZWr`  
  `chmod +x install-nethunter-termux`  
  `./install-nethunter-termux`  

  

Usage:  
-------  

Open Termux and type one of the following:  

| Command                | To                                                      |
| ---------------------- | ------------------------------------------------------- |
| `nethunter`            | start Kali NetHunter command line interface             |
| `nethunter kex passwd` | Configure the KeX password (only needed before 1st use) |
| `nethunter kex &`      | start Kali NetHunter Desktop Experience                 |
| `nethunter kex stop`   | stop Kali NetHunter Desktop Experience                  |

Note: The command `nethunter` can be abbreviated to `nh`.  
_Tip: If you run kex in the background (`&`) without having set a password, bring it back to the foreground first when prompted to enter the password, i.e. via `fg <job id>` - you can later send it to the background again via `Ctrl + z` and `bg <job id>`_  

To use KeX, start the KeX client, enter your password and click connect  
_Tip: For a better viewing experience, enter a custom resolution under "Advanced Settings" in the KeX Client_  