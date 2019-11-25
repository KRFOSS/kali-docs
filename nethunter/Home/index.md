---
title: Kali NetHunter Documentation
description:
icon:
date: 2019-10-26
type: post
weight: 1
author: ["g0tmi1k",]
tags: ["",]
keywords: ["",]
og_description:
---

## Kali NetHunter Documentation

The Kali NetHunter is an Android ROM overlay that includes a robust **Mobile Penetration Testing Platform**.  
The overlay includes a custom kernel, a Kali Linux chroot, and an accompanying Android application, which allows for easier interaction with various security tools and attacks.  
Beyond the [penetration testing tools](http://tools.kali.org) arsenal within Kali Linux, NetHunter also supports several additional classes, such as **HID Keyboard Attacks**, **BadUSB attacks**, **Evil AP MANA attacks**, and much more.  
The included NetHunter Store client provides access to a wide range of additional security related apps.  
For more information about the moving parts that make up NetHunter, check out our [NetHunter Components](https://gitlab.com/kalilinux/nethunter/build-scripts/kali-nethunter-project/wikis/Nethunter-Components) page. NetHunter is an open-source project developed by [Offensive Security](https://www.offensive-security.com) and the community.

## 1.0 Supported Devices and ROMs
The following table lays out NetHunter supported hardware as well as the corresponding ROM or Android versions for which NetHunter is built:

| Device                   | Android Version         | Notes                             |
|--------------------------|:-----------------------:|-----------------------------------|
| Nexus 4 (mako)           | **5.1.1** <br> **CM 13.0** |                                |
| Nexus 5 (hammerhead)     | **5.1.1** or **6.0.1** <br> **CM 13.0** or **CM 14.1** |    |
| Nexus 5x (bullhead)      | **6.0.1**               |                                   |
| Nexus 6 (shamu)          | **5.1.1** or **6.0.1** <br> **LOS 16.0** |                      |
| Nexus 6P (angler)        | **6.0.1** or **7.1.2** <br> **LOS 14.1**  | USB problems with anything newer than 7.1.2 <br> Latest supported LOS image available [here](https://build.nethunter.com/contributors/re4son/angler/)                               |
| Nexus 7 2013 (flo)       | **5.1.1** or **6.0.1** <br> **CM 13.0** |                   |
| Nexus 9 (flounder)       | **5.1.1** or **6.0.1**  |                                   |
| Nexus 10 (manta)         | **5.1.1**               |                                   |
| OnePlus One (oneplus1)   | **CM 12.1** or **13.0** | Our preferred device              |
| OnePlus 2 (oneplus2)     | **CM 12.1** - **16.0**  |                                   |
| OnePlus 3 (oneplus3)     | **6.0.1** or **7.0.0**  | Unified build in 7.0.0 (OxygenOS) |
| OnePlus 3T (oneplus3)    | **6.0.1** or **7.0.0**  | Unified build in 7.0.0 (OxygenOS) |
| OnePlus 7 (guacamoleb)   | **OOS 9.5.8**           | Important: Install Disable_Dm-Verity_ForceEncrypt |
| OnePlus 7 Pro (guacamole)| **OOS 9.5.8**           | Important: Install Disable_Dm-Verity_ForceEncrypt |
| OnePlus X (oneplusx)     | **CM 13.0**             |                                   |
| Galaxy Note 3 (hlte)     | **CM 12.1** or **13.0** <br> **TouchWiz 5.0** |             |
| Galaxy S5 (klte)         | **LineageOS 14.1** <br> **TouchWiz 5.1** or **6.0** |  |
| Galaxy S7 (herolte)      | **TouchWiz 6.0.1**      | **Warning**: Exynos models only!  |
| Galaxy S7 edge (hero2lte)| **TouchWiz 6.0.1**      | **Warning**: Exynos models only!  |
| Galaxy Tab S4 Wifi (830) | **TouchWiz 9.0.1**      | [@re4son](https://twitter.com/re4sonkernel)'s preferred device         |
| Galaxy Tab S4 LTE (835) | **TouchWiz 9.0.1**      |                                    |
| Gemini (geminipda)       | **7.0.0**               | [@re4son](https://twitter.com/re4sonkernel)'s other preferred device  |
| LG G5 T-Mobile (h830)    | **7.0.0**               |                                   |
| LG G5 International (h850)| **7.0.0**              |                                   |
| LG V20 T-Mobile (h918)   | **7.0.0**               | **Warning**: Requires exploit on v10d firmware to unlock flashing! |
| LG V20 International (990DS)| **7.0.0**              |                                   |
| HTC One M7 GPE (onem7gpe)| **5.1.1**               | Google Play Edition               |
| HTC 10 (htc_pmewl)       | **6.0.1**               |                                   |
| Sony Xperia ZR (dogo)    | **CM 13.0**               |                                   |
| Sony Xperia Z (yuga)     | **CM 13.0**               |                                   |
| Sony Xperia Z1 (honami)    | **CM 13.0**               |                                   |
| SHIELD tablet (shieldtablet) <br> SHIELD tablet K1 | **6.0.1** <br> **CM 13.0** |      |
| ZTE Axon 7 (ailsa_ii)    | **6.0.1**               | [@jcadduono](https://github.com/jcadduono)'s preferred device |

## 2.0 Downloading NetHunter
Official release NetHunter images for your specific supported device can be download from the Offensive Security NetHunter project page located at the following URL:

* https://www.offensive-security.com/kali-linux-nethunter-download

Once the zip file has downloaded, verify the SHA1 sum of the NetHunter zip image against the values on the Offensive Security NetHunter download page. If the SHA1 sums do not match, do not attempt to continue with the installation procedure.

The SHA256 sums for each file can be found in the SHA256SUMS file at the top of every download page. You may also enable zip signature verification before flashing and TWRP will verify the entire zip for you before installing.

For a fresh install, you will need a **nethunter-generic-[arch]-kalifs-\*.zip** as well as a **kernel-nethunter-[device]-[os]-\*.zip**. The kernel should be flashed last. The **update-nethunter-generic-[arch]-\*.zip** files are for updating your installation or if you wish to download the Kali rootfs inside the NetHunter app instead.

## 3.0 Building NetHunter
Those of you who want to build a NetHunter image from our GitHub repository may do so using our Python build scripts.  Check out our [Building NetHunter](https://gitlab.com/kalilinux/nethunter/build-scripts/kali-nethunter-project/wikis/Building-Nethunter) page for more information.
You can find additional instructions on using the NetHunter installer builder or adding your own device in the [README](https://gitlab.com/kalilinux/nethunter/build-scripts/kali-nethunter-project/blob/master/nethunter-installer/README.md) located in the [nethunter-installer](https://gitlab.com/kalilinux/nethunter/build-scripts/kali-nethunter-project/blob/master/nethunter-installer) git directory.

## 4.0 Installing NetHunter on top of Android
Now that you've either downloaded a NetHunter image or built one yourself, the next steps are to prepare your Android device and then install the image. "Preparing your Android device" includes:

  - **unlocking** your device and **updating it to stock** AOSP or LineageOS (CM). (Check point [1.0](#10-supported-devices-and-roms) for supported roms)
  - **installing [Team Win Recovery Project](https://twrp.me/)** as a custom recovery.
  - **installing** [Magisk](https://magiskmanager.com/) to root the device
  - Once you have a custom recovery, all that remains is to flash the NetHunter installer zip file onto your Android device.

## 5.0 Post Installation Setup

* Open the NetHunter App and start the Kali Chroot Manager.
* Install the Hacker Keyboard from the NetHunter Store using the NetHunter Store app.
* Install any other apps from the NetHunter app store as required.
* Configure Kali Services, such as SSH.
* Set up custom commands.
* Initialize the Exploit Database.

## 6.0 Kali NetHunter Attacks and Features
####Kali NetHunter Application
* [**Home Screen**](https://gitlab.com/kalilinux/nethunter/build-scripts/kali-nethunter-project/wikis/NetHunter-Home-Screen) - General information panel, network interfaces and HID device status.
* [**Kali Chroot Manager**](https://gitlab.com/kalilinux/nethunter/build-scripts/kali-nethunter-project/wikis/NetHunter-Chroot-Manager) - For managing chroot metapackage installations.
* [**Check App Update**](https://gitlab.com/kalilinux/nethunter/build-scripts/kali-nethunter-project/wikis/NetHunter-App-Update) - For checking Kali NetHunter Android App updates.
* [**Kali Services**](https://gitlab.com/kalilinux/nethunter/build-scripts/kali-nethunter-project/wikis/NetHunter-Kali-Services) - Start / stop various chrooted services. Enable or disable them at boot time.
* [**Custom Commands**](https://gitlab.com/kalilinux/nethunter/build-scripts/kali-nethunter-project/wikis/NetHunter-Custom-Commands) - Add your own custom commands and functions to the launcher.
* [**MAC Changer**](https://gitlab.com/kalilinux/nethunter/build-scripts/kali-nethunter-project/wikis/NetHunter-MAC-Changer) - Change your Wi-Fi MAC address (only on certain devices)
* [**VNC Manager**](https://gitlab.com/kalilinux/nethunter/build-scripts/kali-nethunter-project/wikis/NetHunter-VNC-Manager) - Set up an instant VNC session with your Kali chroot.
* [**HID Attacks**](https://gitlab.com/kalilinux/nethunter/build-scripts/kali-nethunter-project/wikis/NetHunter-HID-Attacks) - Various HID attacks, Teensy style.
* [**DuckHunter HID**](https://gitlab.com/kalilinux/nethunter/build-scripts/kali-nethunter-project/wikis/NetHunter-DuckHunter) - Rubber Ducky style HID attacks
* [**BadUSB MITM Attack**](https://gitlab.com/kalilinux/nethunter/build-scripts/kali-nethunter-project/wikis/NetHunter-BadUSB) - Nuff said.
* [**MANA Wireless Toolkit**](https://gitlab.com/kalilinux/nethunter/build-scripts/kali-nethunter-project/wikis/NetHunter-Mana-Wireless) - Setup a malicious Access Point at the click of a button.
* [**MITM Framework**](https://gitlab.com/kalilinux/nethunter/build-scripts/kali-nethunter-project/wikis/NetHunter-MITMf) - Inject binary backdoors into downloaded executables on the fly.
* [**NMap Scan**](https://gitlab.com/kalilinux/nethunter/build-scripts/kali-nethunter-project/wikis/NetHunter-NMAP) - Quick Nmap scanner interface.
* [**Metasploit Payload Generator**](https://gitlab.com/kalilinux/nethunter/build-scripts/kali-nethunter-project/wikis/NetHunter-MPG) - Generating Metasploit payloads on the fly.
* [**Searchsploit**](https://gitlab.com/kalilinux/nethunter/build-scripts/kali-nethunter-project/wikis/NetHunter-Searchsploit) - Easy searching for exploits in the Exploit-DB.

####3rd Party Android Applications (WIP)
* [**NetHunter Terminal Application**](https://gitlab.com/kalilinux/nethunter/build-scripts/kali-nethunter-project/wikis/NH-App-Terminal)
* [**DriveDroid**](https://gitlab.com/kalilinux/nethunter/build-scripts/kali-nethunter-project/wikis/NH-App-DriveDriod)
* [**USB Keyboard**](https://gitlab.com/kalilinux/nethunter/build-scripts/kali-nethunter-project/wikis/NH-App-Keyboard)
* [**Shodan**](https://gitlab.com/kalilinux/nethunter/build-scripts/kali-nethunter-project/wikis/NH-App-Shodan)
* [**Router Keygen**](https://gitlab.com/kalilinux/nethunter/build-scripts/kali-nethunter-project/wikis/NH-App-Router-Keygen)
* [**cSploit**](https://gitlab.com/kalilinux/nethunter/build-scripts/kali-nethunter-project/wikis/NH-App-Csploit)

## 7.0 Porting NetHunter to New Devices:

If you're interested in porting NetHunter to other Android devices, check out the following links. If your port works, make sure to tell us about it so we can include these kernels in our releases!

1. [Getting Started](https://gitlab.com/kalilinux/nethunter/build-scripts/kali-nethunter-project/wikis/Porting-Nethunter)
2. [Modifying a Kernel](https://gitlab.com/kalilinux/nethunter/build-scripts/kali-nethunter-project/wikis/Modifying-the-Kernel)
3. [Adding Your Device](https://gitlab.com/kalilinux/nethunter/build-scripts/kali-nethunter-project/blob/master/nethunter-installer/README.md)

## 8.0 Known Working Hardware:

1. [Wireless Cards](https://gitlab.com/kalilinux/nethunter/build-scripts/kali-nethunter-project/wikis/Wireless-Cards)
2. SDR - RTL-SDR (based on RTL2832U)

## NetHunter App:

1. The NetHunter App can be found [here](https://gitlab.com/kalilinux/nethunter/apps/kali-nethunter-app/releases)
2. The source code for building the NetHunter App can be found on GitLab [here](https://gitlab.com/kalilinux/nethunter/apps/kali-nethunter-app)
