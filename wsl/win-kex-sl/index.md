---
title: Win-KeX SL
description: Win-KeX Seamless Mode
icon: ti-pin
date: 2020-09-17
type: post
weight: 37
author: ["Re4son",]
tags: ["",]
keywords: ["",]
og_description:
---

## Content:

- [Overview](#overview)
- [Usage](#Usage)
  - [Start](#start)
  - [Sound Support](#sound-support)
  - [Multiscreen Support](#multiscreen-support)
  - [Stop](#stop)



## Overview

#### Win-KeX in Seamless Mode will launch a Kali Linux panel on the screen top of the Windows desktop.

##### Applications started via the panel will share the desktop with Microsoft Windows applications.

Seamless mode removes the visual segregation between linux and window apps and offers a great platform to run a penetration test in Kali Linux and copy the results straight into a Windows app for the final report.

Win-KeX utilises [VcXsrv Windows X Server](https://sourceforge.net/projects/vcxsrv/) to achieve seamless desktop integration.

![win-kex-sl](win-kex-sl.png)  

&nbsp;



## Usage  

### Start  

- Start Win-KeX as normal user in seamless mode via:  
`kex --sl`  

  When starting Win-KeX SL for the first time, ensure to select  
  
  "**<u>Public networks</u>**"  
  
  when asked for authorisation to allow traffic through the Windows Defender firewall  
  
  <img src="firewall.png" alt="Firewall" style="zoom: 50%;" />  
  
  &nbsp;  &nbsp;
  
  This will start Win-KeX in seamless mode:   
  ![Win-Kex SL](win-kex-sl.png)  
  
  The Kali panel is placed at the top of the screen and the Windows Start menu at the bottom.  
  
    
  
- **Tip:** The Kali panel might cover the title bar of maximised windows. To prevent it getting in the way you may prefer to set it to "Automatically hide" in the panel preferences.  

  &nbsp;  &nbsp;

### Sound Support  

- Win-KeX includes pulse audio support  

- To start Win-KeX with sound support, add `--sound` or `-s`, e.g.  
  `kex --win -s`  

- When starting Win-KeX with sounds support for the first time, ensure to select  
  

"**<u>Public networks</u>**"  

  when asked for authorisation to allow traffic through the Windows Defender firewall  

  ![PulseAudit-Firewall](win-kex-pulseaudio_firewall.png)    

  &nbsp;  &nbsp;

### Multiscreen Support

- Win-KeX supports multiscreen setups: 

  Open "Panel Preference" to reduce the panel length, untick "Lock panel" and move the panel to the desired screen.  

  &nbsp;

### Stop  

- To close Win-KeX SL, simply log out of the session via the "Logout" button in the panel.  
  
- To optionally shutdown the Win-KeX SL server, type  
  `kex --sl --stop`   

    &nbsp;

#### Enjoy Win-KeX!  
