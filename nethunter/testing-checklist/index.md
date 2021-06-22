---
title: Testing Checklist
description:
icon:
weight:
author: ["g0tmi1k",]
---

Beginning a checklist for testing new devices:

- Does it boot?

- Did applications install and do they work?

    - Android VNC
    - BlueNMEA
    - DriveDroid
    - Hacker's Keyboard
    - Kali NetHunter Application
    - RF Analyzer
    - SuperSU (This is installed separately)
    - Terminal Emulator

- Check Settings > About Phone > Kernel Version - Does it say Kali?

- Launch Kali NetHunter Application

    - HID Keyboard Attack
        - Can you update/write to config file
        - Does it work?

- Launcher Kali terminal shell

    - Metasploit
        - Run commands: `service postgresql start && service metasploit start`
            - Did it generate username/database?
        - Run command: msfconsole
            - Be patient, this can take 10 minutes or longer

    - Social Engineering Toolkit

- External WiFi support

    - Plugin device, run commands: `ifconig wlan1 up && wifite`
        - Does wifite see wlan1?
    - Run commands (assume wlan1 up): `airmon-ng start wlan1 && airodump-ng mon0`
        - Check for errors
    - Run kalimenu > Wireless attacks > kismet

- Kalimenu
