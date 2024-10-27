---
title: Installing NetHunter on the OnePlus 7
description:
icon:
weight:
author: ["re4son",]
---

![](one-plus-7p.png)

# From a reset to running NetHunter in 4 steps:
  
1. Flash latest Android 10 with the unbrick tool
2. Flash TWRP and Magisk
3. Disable force encryption of data partition
4. Install NetHunter
5. Disable OnePlus update service
  
## 1. Flash latest stock (OOS) Android 10
  
1.01.	Download and unzip [guacamoleb_14_P.32_210127.zip](https://kali.download/nethunter-images/devices/guacamole/guacamoleb_14_P.32_210127.zip) on your Windows computer
1.02	Open admin cmd and type: "bcdedit /set testsigning on", reboot  
1.03	Run MsmDownloadTool V4.0.exe  
1.04	Power off OnePlus 7, press vol + & vol - at the same time, count to 5 and connect phone to laptop  
1.05	When device is detected (bing) press start  
1.06	reboot and set up device  
1.07	In developer options, enable OEM unlock & USB debugging  
1.08	sudo apt install adb  
1.09	adb kill-server && ./adb start-server  
1.10	adb reboot bootloader  
1.11	sudo fastboot oem unlock  
1.12	Reboot and set up phone  
  
## 2. Flash TWRP and Magisk
  
2.01	Copy to Oneplus7  
2.02	Download [Magisk-v19.3.zip](https://kali.download/nethunter-images/devices/guacamole/Magisk-v19.3.zip) to Oneplus7
2.03	Download [twrp-3.3.1-1-guacamoleb.img](https://kali.download/nethunter-images/devices/guacamole/twrp-3.3.1-1-guacamoleb.img) to your PC
2.04	Install Android platform tools  
2.05	In developer options of your phone, enable USB debugging  
2.06	adb kill-server && ./adb start-server  
2.07	adb reboot bootloader  
2.08	sudo fastboot boot twrp-3.3.1-1-guacamoleb.img  
2.09	Device automatically reboots into trwp  
2.10	In twrp, install twrp-installer-3.3.1-1-guacamoleb.zip & Magisk-v19.3.zip, reboot  
2.11	Reboot  
  
## 3. Disable force ecryption of data partition
  
3.01	Reboot into recovery  
3.02	format /data  
3.03	reboot into recovery  
3.04	install magisk  
3.05	install Disable_Dm-Verity_ForceEncrypt_11.02.2020.zip  
3.06	reboot to system  
3.07	set up phone but skip fingerprint or any other security. Set up later (not sure if that re-encrypts the phone)  
  
## 4. Install NetHunter
  
4.01	Download image from [our download page](/get-kali/#kali-mobile)  
4.02    there is a bug in the current installer requiring to install the kernel separately, [download it here](https://kali.download/nethunter-images/devices/guacamole/kernel-nethunter-2021.3-oneplus7-oos-ten.zip)
4.03	reboot into recovery  
4.04	install nethunter zip  
4.06    install magisk again  
4.07	install disable-force-encrypter again  
4.08    reboot  
4.09	run NetHunter app, wait for the initial setup to finish then reboot  
4.10	update NetHunter app from store  
  
## 5. Disable OnePlus update service
  
5.01	Open Android terminal as root  
5.02	su -c pm disable com.oneplus.opbackup  
5.03	"System Update" service is now disabled  
  
### Enjoy Kali NetHunter on the OnePlus 7  
  
  
Please help with the development by submitting issues and pull requests. We much appreciate it.
