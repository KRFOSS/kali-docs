---
title: The Android Hacking Landscape
description:
icon:
date: 2019-10-26
type: post
weight: 999
author: ["g0tmi1k",]
tags: ["",]
keywords: ["",]
og_description:
---

## The Android Hacking Landscape
------------------------------------------

[Before we get into any Android hacking, we need to set the stage and
discuss some terms and concepts. Anyone can follow instructions, but
it's important to understand the basic concepts because before long,
we'll be getting in pretty deep.]

[Having said this, we're going to jump in fairly quickly. There's simply
no sugarcoating this stuff. If you make it through the next few pages,
we'll make a basic Android hacker out of you yet! We think the results
are worth it! Let's get started.]

[*[Bootloaders]{.underline}*: When you turn on your device, the
bootloader executes first, handing over control to the boot image, which
contains the phone's startup files. The boot image runs the device's
kernel (the bridge between the software and hardware), then loads the
Android operating system.]

[If your bootloader is "locked", you can only flash (short for "write to
the device's flash" or semi-synonymous with "install") boot images that
have been digitally signed by the manufacturer. If your bootloader is
unlocked, you can flash third-party boot images. An OEM signed Android
boot or recovery image also verifies the Android system partition. If
the verification fails on a system boot, it will reboot to recovery. In
recovery, it will ask for an OEM signed zip to restore the partition's
integrity or recover the incorrect bits from a duplicate system
partition (in the case of newer Nougat devices). While you may be able
to temporarily "root" a device (hang in there!) without unlocking the
bootloader, in order to extend your root state, you need to
unlock.]

[In most cases, the first step to unlocking the full potential of your
Android device is to unlock the bootloader. ***Unlocking your bootloader
may wipe all of your data. Always backup your device before
unlocking***. We'll discuss backup strategies once we dive
in.]

[*[Rooting]{.underline}* (which is somewhat similar to "jailbreaking" an
Apple device) gives you root access to the Android OS and allows you to
modify the software on the device or install other software that isn't
approved (or "signed") by the manufacturer. Once your bootloader is
unlocked, you can run a third-party application (like superuser
or] [supersu) which will grant you full access to the
operating system and you can literally change anything you want. Rooting
does not erase anything on your device (but unlocking the bootloader
likely did that already).]

[Beware, though.. manufacturers sign their apps to make sure they are
"clean". Running unsigned, untrusted third-party apps can introduce
spyware and other malware. In addition, you can "brick" your device if
you root it improperly. The odds of this are very slim if you follow the
instructions carefully, but if you are careless, and race through
instructions there's a chance you'll lose your device. Is the very
minimal amount of risk worth it? It boils down to one question: "Are you
an Android hacker or not?"]

[*[Recovery]{.underline}*: A "recovery" is a run environment that
resides in a bootable partition on the device which can be activated
with a combination of keypresses or command-line instructions from a
connected computer. The key function of this environment is to assist in
recovering the device. The typical way to load the recovery environment
is through the fastboot utility triggered from a connected PC or device
key press combination at boot time or through an alternative,
OEM-specific 'Download mode' tool. The stock recovery for most devices
only allows you to reboot, apply a factory (signed) update, wipe and/or
factory reset the device or wipe the cache partition. A custom recovery
like Team Win Recovery Project (TWRP) or ClockWorkMod extend these
capabilities, allowing you to install custom operating systems (or
"ROMs"), backup and restore the device, mount external filesystems and
more.]

[*[Custom ROMs]{.underline}*: In Android parlance, "ROM" generally
refers to the Android operating system. The traditional acronym for ROM
(read-only memory) still applies: the Android operating system is stored
in the /system partition which is mounted read-only to standard
(non-root) users] [as opposed to the /data partition which is
user-writeable. This is precisely the reason you need to root your
device to change the operating system, since it resides on /system. Your
device likely came from the factory with a "stock ROM", while a "custom
ROM" is a customized third-party replacement.]

[Why install a ROM? You can install the latest and greatest version of
Android, without waiting for an OTA (over the air) update, install a
version of Android that the manufacturer hasn't released for your
device, or you can install a custom ROM made by a third party. The
latter can create problems for you if the developer releases crummy
code: you could experience battery life issues because of unoptimized
code, you may experience hardware problems from untested devices or you
could experience device instability because of poorly written code.
However, if you trust that the developers know what they're doing, a
custom ROM can absolutely revolutionize your device.]

[The two primary branches of Android are Google's Android Open Source
Project ("AOSP",
[[https://source.android.com]{.underline}](https://source.android.com))
and CyanogenMod ("CM",
[[http://www.cyanogenmod.com]{.underline}](http://www.cyanogenmod.com))
which although discontinued is being carried on by Lineage
([[http://lineageos.org]{.underline}](http://lineageos.org)). Most other
ROMs are derivatives of these, like CoopherheadOS
([[https://copperhead.co/android]{.underline}](https://copperhead.co/android))
which was designed for security-minded users.]

[NetHunter wasn't originally designed to be a ROM, but rather as an
"overlay". The zip file was meant to be flashed over top of an AOSP-type
ROM where it would modify parts of the /system partition. In addition,
this version of the NetHunter package would add Android apps into /data
along with the Kali Linux "chroot" ("simulated root") environment. This
made NetHunter more universal, and the overlay could be installed on
just about any rootable / unlocked Android device. However, the primary
tradeoff is that the NetHunter "overlay" does not make any modifications
to the] [kernel, meaning certain capabilities won't work
including HID, DriveDroid, external wireless adapters and more. However,
a ROM-based version of NetHunter is available which will offer a full
compliment of features including centralized updates.]
[Teamwork: Rooting and Unlocking]
--------------------------------------------

[Rooting your device enhances your privileges and gives you full access
to the Android operating system. This means you can do whatever you like
*[inside Android]{.underline}.* There are ways to temporarily root your
device, but they are often incomplete, often depend on a specific
version-dependent vulnerability and must be run each time you want to
execute a root-privileged action. In order to permanently root your
device, you must write write a third-party binary (like superuser) and
an app to launch that binary into the system partition. However, a
locked bootloader blocks write access to the system
partition.]

[We've seen the value of installing a custom recovery (which allows full
backup and restore, for example) and understand the value of installing
a custom ROM (running NetHunter, for example) but in order to do this,
we need more than root inside Android, we need the ability to load an
alternate operating system, and this is the job of the bootloader. If we
can modify (unlock) the bootloader, we can not only install custom ROMs
and recoveries, we can gain permanent root within the Android
OS.]

[This is why unlocking the bootloader and rooting are often spoken of in
similar context, although we hope by now you understand that they are
very different. Once you've gained a general grasp on all of this, it's
time to take the next step and backup your data (if necessary) then
unlock and root your Android device.]

[]
-------------

 []
-------------

[Non-root Data Backup]
---------------------------------

[Back up everything you can before unlocking and rooting your device. If
you've got a new device, you can safely skip this step. We generally
recommend that you unlock and root your device while it is brand new so
you can avoid the unpleasantries of backing up a non-rooted
device.]

[There are several methods you can use to backup your data without
rooting your device. Note that these will NOT be full system backups,
but rather only partial data copies since you don't have root access to
the device.]

[First, you can use the "manual method" to copy data from the device.
Simply plug in the device, and use the file manager (on Windows), the
Android File Transfer program (download to use on OSX) or mount (on
Linux) to copy the files and folders that you're interested in. Note
that some of these methods may fail when you copy system folders and
files (like those in the Android directory). Pay attention to obvious
folders like DCIM and Pictures and spend time navigating the file tree
to copy folders and files that are valuable to you:]

![pasted-image-54.png](){width="5.310664916885389in"
height="4.348958880139983in"}[Second, you can use the built-in Google sync functions to back up
various files to your Google online storage space. First, navigate to
Settings -\> Backup & Reset, ensure that "Back up my data" is checked
and that a "Backup account" is selected:]

![](){width="1.9473972003499562in"
height="3.4739588801399823in"}[Next, check out Settings -\> Accounts -\> Google and select the backup
account you set up to see the options you are currently
syncing:]

![](){width="1.8345833333333332in"
height="3.276042213473316in"}[Each of these items, if selected, will be copied from your device to
the Google cloud, and can be restored later from any Android device,
when you sign in with that account. Be sure to select "Sync now" from
the Sync menu to synchronize data before unlocking your
bootloader:]

![](){width="1.9719619422572179in"
height="3.5156255468066493in"}[There is no easy way to be absolutely sure that all your content is
backed up (despite the "Last synced" date that is shown) since there is
no obvious indicator that a sync has completed. However, in some apps,
like Google Photos, different icons may appear that show whether or not
a photo or folder has been synced. Note the cloud icon
(]![](){width="0.23437554680664918in"
height="0.20833333333333334in"}[) that indicates a folder has not been
synced:]

![](){width="1.6397189413823272in"
height="2.901042213473316in"}[You can click this icon to synchronize that folder, and repeat for any
other folders that have not been synced. In the case of photos, you can
connect to http://photos.google.com (logged in with the "backup account"
you used in settings) to confirm that your photos have been
synchronized:]

![](){width="4.786458880139983in"
height="3.582173009623797in"}[You may be able to repeat this process with other synchronized
selections in Settings -\> Accounts, but generally speaking, you'll be
placing a lot of faith in Google's automated sync services. Note that
you can use both methods together if you're paranoid.]

[If you have synchronized your contacts, you can review and verify them
at http://contacts.google.com:]

![](){width="4.796875546806649in"
height="3.666841644794401in"}[At this point, most of your data should be backed up, although you
should be very careful when it comes to app data that you are paranoid
about losing. For example, if you have expense reports, receipts, or
other sensitive data that is captured by a third-party (non-Google) app,
you should use the "export" or "share" options of those apps to get the
data off your device before proceeding. There are also a plethora of
Apps on the Play Store that will do various levels of backups. Use them
in addition to these techniques if you are extremely paranoid about
losing data.]

[If you think this is a bit scary and a serious pain in the rear-end,
you're right. Stock Android devices only allow you to do full and
complete backups through the Google infrastructure or with a mishmash of
backups apps, built-in syncs, in-app exports and the tossing of copious
chicken bones and prayer for good measure. Wouldn't it be nice to launch
a full low-level backup that grabs absolutely everything from your
device and stores it in a format that you can later restore? Then you'd
be free to try out different operating systems and tweak your phone
without fear of losing anything you care about. Even better, wouldn't it
be great to have more than one specialized operating system installed on
your device that you could boot as needed? Well, you can do all of that
and more once your device is rooted. So let's get to it.]

[Preparing for Bootloader Unlock]
--------------------------------------------

[Now it's time to take your first step towards hacking your Android
device. First, we will unlock the bootloader and then we will root the
device. To begin, we'll install some tools.]

### [Installing USB drivers]

[If you connect your device to your computer with a USB cable, and you
can't "see" it in your file manager, you may need to install USB to
allow your device to communicate with your computer. On a Mac, you can
simply install the Android File Transfer utility
([[https://www.android.com/filetransfer]{.underline}](https://www.android.com/filetransfer)),
while on Windows, you can install the "naked" ADB drivers
([[http://www.xda-developers.com/universal-naked-driver-solves-your-adb-driver-problems-on-windows]{.underline}](http://www.xda-developers.com/universal-naked-driver-solves-your-adb-driver-problems-on-windows/))
or the [[Universal ADB
drivers]{.underline}](http://www.koushikdutta.com/post/universal-adb-driver)
([[http://www.koushikdutta.com/post/universal-adb-driver]{.underline}](http://www.koushikdutta.com/post/universal-adb-driver)).
For Linux, you may need to simply search the internet for "USB driver",
the name of your phone and the name of your operating system, or simply
"android usb drivers linux".]

### [Installing Android Studio Tools]

[Next, we'll need to install the adb and fastboot tools. If you're on a
Windows machine, you can install Minimal ADB and Fastboot by shimp208
([[https://forum.xda-developers.com/showthread.php?t=2317790]{.underline}](https://forum.xda-developers.com/showthread.php?t=2317790))
which is a compact version of the tools available from the much larger
Android Studio bundle. Alternatively, you can download the full Android
Studio for any OS from https://developer.android.com/studio/index.html.
Install and run the Studio installer, then locate the adb and fastboot
commands which are under
/Users/\<user\>/Library/Android/sdk/platform-tools on OSX and
\\Users\\\<user\>\\sdk\\platform-tools on Windows.]

### [Enabling Developer Mode]

[The developer options in Settings -\> Developer Options come in handy
for all sorts of things, but the option may not be visible on your
device. To enable it, navigate to Settings -\> About device -\> Software
and tap on the Build Number setting seven times.]

[You should now be able to navigate to Developer Options near the bottom
of the Settings menu. Enable USB debugging from Settings -\> Developer
on your device. It is listed as "Android Debugging - Enable the Android
Debug (adb) Interface" or "USB Debugging":]

![](){width="2.1474606299212597in"
height="3.8177088801399823in"}[Once you enable this option, connect your device via USB, and you
should see a prompt on your device asking you to approve your computer's
RSA fingerprint:]

![](){width="2.625648512685914in"
height="2.057292213473316in"}[Approve this option, and for ease of use, "Always Allow" this device to
perform USB debugging commands. If you do not see this prompt,
double-check that you have USB debugging enabled.]

[You can test the installation of the Android Studio command line tools
by changing into the appropriate sdk directory and executing the
following on Windows:]

[adb version]

[or the following on OSX:]

[./adb version]

[The result of the preceding command should be something like
this:]

[Android Debug Bridge version 1.0.36]

[Revision 8453623d5db51-android]

[Next, you can check for your connected device with adb devices (on
Windows) or ./adb devices (on OSX). The output should show your device
ID and the word "device":]

[\$ ./adb devices]

[List of devices attached]

[070577f007a446 device]

[If instead of device, you see unauthorized, you have not properly
enabled USB debugging or accepted your computer's RSA fingerprint. Try
again.]

> *[Sidebar: Note that all OSX and Linux commands should likely be
> preceded with the "./" characters unless the commands are in your
> path. For brevity, we won't reiterate this again.]*

[With adb and fastboot installed and your device connected and
responsive, let's now check for safety features that may be in place
that could prevent bootloader unlocking.]

### ###  ### [Enabling OEM Unlock]

[If you are running a Google AOSP-based operating system that's been
updated to version 5.0 ("Lollipop") or newer, you'll need to make sure
the "OEM Unlock" security feature is enabled. This setting was put in
place to prevent someone from flashing your phone and accessing your
data without your consent.]

[To enable this feature, navigate to Settings -\> Developer and enable
OEM unlock.]

[If you don't see this setting, you do not have this safety
feature.]

###  [Unlocking the OnePlus One Bootloader]
-------------------------------------------------

[With our environment configured now it's finally time to unlock the
bootloader. We will begin in fastboot mode.]

### [Entering Fastboot Mode]

[The most common way to fastboot is to power off your device and then
power it on again, while holding down a specific key
combination.]

[On a OnePlus One, hold down **Volume Up** and **Power** at the same
time.]

[Alternatively, you can fastboot into the bootloader menu from the
command line. Connect your device to your PC via USB and type adb reboot
bootloader.]

### [Checking Bootloader Status]

[Before unlocking the bootloader, check to see if it's already unlocked.
Use fastboot oem device-info to list the device info. You should see
something like:]

[\$ **./fastboot oem device-info**]

[\...]

[(bootloader) Device tampered: true]

[(bootloader) Device unlocked: true]

[(bootloader) off-mode-charge: true]

[OKAY \[ 0.101s\]]

[finished. total time: 0.101s]

[The "Device unlocked" line shows the lock state of the device. If your
device is already unlocked, you can skip to rooting your device.
Otherwise, you'll need to unlock your bootloader.]

### [The Moment of Truth]

[Well, you've made it this far. No sense backing down now. Welcome to
the moment of truth. Your unlock is moments away. First, make sure
you're in fastboot mode:]

[\$ **./fastboot devices**]

[4814f2ca fastboot]

[With the device in fastboot mode, unlock the bootloader and reboot your
device:]

[\$ **./fastboot oem unlock**]

[\...]

[OKAY \[ 0.002s\]]

[finished. total time: 0.002s]

[\$ **./fastboot reboot**]

[rebooting\...]

[finished. total time: 0.004s]

[After rebooting, head back into fastboot mode:]

[\$ **./adb reboot bootloader**]

[Now, you'll need a replacement recovery. We are going to use a ROM that
has been pre-rolled to include the TWRP recovery, including multirom
support, compiled by the NetHunter team. It can be downloaded from:
<https://build.nethunter.com/nethunteros/CM14_1/bacon/twrp-multirom-bacon-20161212-01.img>.]

[Copy the .img file to the same folder as adb and fastboot. For example,
if we are in the adb directory, and we downloaded the image to
\~/Downloads:]

[\$ **cp /Users/j/Downloads/twrp-multirom-bacon-20161212-01.img
.**]

[With the img file in place, verify that the file size looks sane. A
zero length file, for example, would be a bad thing to
flash.]

[\$ **ls -l twrp-multirom-bacon-20161212-01.img **]

[-rw-r\--r\-- 1 root staff 13893632 Jan 3 19:26
twrp-multirom-bacon-20161212-01.img]

[Next, check that you are in fastboot and flash the image:]

[\$ **./fastboot devices**]

[4814f2ca fastboot]

[\$ **./fastboot flash recovery
twrp-multirom-bacon-20161212-01.img**]

[target reported max download size of 1073741824 bytes]

[sending \'recovery\' (13568 KB)\...]

[OKAY \[ 0.427s\]]

[writing \'recovery\'\...]

[OKAY \[ 0.185s\]]

[finished. total time: 0.612s]

[Now that the recovery is flashed, hold down Volume-Down and Power until
the system reboots. Let go of all keys when you see the OnePlus One
logo. The TWRP will load and the Read-Only system prompt will be
shown:]

![](){width="2.414278215223097in"
height="4.307292213473316in"}[Swipe to allow system modifications. This is one of the joys of a
custom recovery.]

[Wiping the device]
------------------------------

[After swiping to allow system modifications, you will be taken to the
TWRP main menu.]

![](){width="1.9922080052493438in"
height="3.5468755468066493in"}[Select Wipe to be taken to the wipe menu:]

![](){width="1.97625656167979in"
height="3.5364588801399823in"}[Select Advanced Wipe to open the advanced wipe options:]

![](){width="2.045593832020997in"
height="3.6302088801399823in"}[Select "Dalvik / ART Cache", "System" and "Data" and swipe to wipe the
device. You will see the status:]

![](){width="2.084488188976378in"
height="3.7031255468066493in"}[Now, select Back to return to the main menu.]

[Installing NetHunter]
---------------------------------

[You'll need to download quite a few files before we get
started.]

-   [The Multirom Installer allows you to have more than one ROM on the
    > system at a time:
    > [[https://build.nethunter.com/nethunteros/CM14\_1/bacon/multirom-20161206-v33e-unofficial-bacon.zip]{.underline}](https://build.nethunter.com/nethunteros/CM14_1/bacon/multirom-20161206-v33e-unofficial-bacon.zip)]

-   [Nethunter OS:
    > [[https://build.nethunter.com/nethunteros/CM14\_1/bacon/los-14.1-20170103-bacon-nethunteros.zip]{.underline}](https://build.nethunter.com/nethunteros/CM14_1/bacon/los-14.1-20170103-bacon-nethunteros.zip)]

-   [NetHunter Kali FS (chroot):
    > [[https://images.offensive-security.com/kalifs-full.tar.xz]{.underline}](https://images.offensive-security.com/kalifs-full.tar.xz)]

-   [You'll also need a secondary ROM to act as your day-to-day
    > "legitimate" phone image. As a reminder, it's never a good idea to
    > use NetHunter as a day-to-day device OS. A good choice is the last
    > official CyangenOS release:
    > [[https://www.androidfilehost.com/?fid=24591000424960111]{.underline}](https://www.androidfilehost.com/?fid=24591000424960111)]

[Copy each of these files to your device. You can create a new directory
to keep things organized. We will use 0SourceFiles so the directory
sorts to the top of the Android file listing. On your local machines,
from the adb directory, create a directory and copy all the zip files
into that directory. Make sure to copy the Kali chroot file to the the
director adb is in, not into 0SourceFiles. The Kali chroot installer
will search for this file in the root of your internal storage, not a
subfolder.]

[\$ **mkdir 0SourceFiles**]

[\$ **cd 0SourceFiles/**]

[\$ **cp \~/Downloads/multirom-20161206-v33e-unofficial-bacon.zip
.**]

[\$ **cp \~/Downloads/los-14.1-20170103-bacon-nethunteros.zip
.**]

[\$ **cp \~/Downloads/cm-13.1.2-ZNH2KAS3P0-bacon-signed.zip
.**]

[\$ **cd ..**]

[\$ **cp \~/Downloads/kalifs-full.tar.xz .**]

[Next, push the files to the device with adb push:]

[\$ **./adb push 0SourceFiles/ /sdcard**]

[/sdcard/0SourceFiles/: 4 files pushed. 0 file\...pped. 4.7 MB/s
(2088925658 bytes in 425.582s]

[\$ **./adb push kalifs-full.tar.xz /sdcard**]

### [Installing NetHunter OS]

[Now, from the main TWRP menu, select INSTALL and browse to the folder
containing your zip files:]

![](){width="2.5254965004374452in"
height="4.484375546806649in"}[Select the los-14.1-20170103-bacon-nethunteros.zip file, and click
"Install Image".]

[]
-------------

![](){width="2.310246062992126in"
height="4.119792213473316in"}[Check "Inject MultiROM after installation" (optional), and uncheck "Zip
Signature verification" unless you also downloaded an MD5 file for this
zip file. Swipe to confirm and begin the flash process.]

![](){width="2.4127296587926508in"
height="4.296875546806649in"}[The flash process should complete normally. Click back to return to the
main menu.]

### [Install the MultiROM Installer]

[.]![](){width="2.3074792213473314in"
height="4.119792213473316in"}[Click "Install" from the main TWRP menu.]

![](){width="2.5254965004374452in"
height="4.484375546806649in"}[Select the multirom-20161206-v33e-unofficial-bacon.zip file and click
"Install Image".]

![](){width="2.2985629921259845in"
height="4.098958880139983in"}[Check "Inject MultiROM after installation" (optional), and uncheck "Zip
Signature verification" unless you also downloaded an MD5 file for this
zip file. Swipe to confirm and begin the flash process.]

![](){width="2.437382983377078in"
height="4.338542213473316in"}[Once the flash completes, press back to return to the TWRP main
menu.]

### [Install Secondary ROM]

[Next, install the secondary "daily use" ROM, which in this case is the
last release of Cyanogen before converting to Lineage.]

[ ] ![](){width="2.3074792213473314in"
height="4.119792213473316in"}[From the TWRP main menu, click the button under the battery indicator
to reveal the MultiROM menu:]

![](){width="2.475628827646544in"
height="4.411458880139983in"}[Click "Add ROM".]

![](){width="2.5677088801399823in"
height="4.567243000874891in"}[Check "Android", and select your first Internal Storage device. Click
"Next" to continue.]

![](){width="2.649691601049869in"
height="4.734375546806649in"}[Click "Zip File" to load the secondary ROM from your internal
storage.]

![](){width="2.6271555118110235in"
height="4.692708880139983in"}[Swipe to confirm flashing the secondary ROM.]

[ ] ![](){width="2.649296806649169in"
height="4.726719160104987in"}[Click "Reboot System" to restart your device. When the device reboots,
you will have a new boot menu. Select "Internal" to boot into NetHunter.
The device will likely return to the boot menu again (as supersu loads
it's one-time installer). Simply select "Internal" after the second
reboot to load NetHunter.]

### [Installing the Kali Chroot]

[Now that the NetHunter OS is installed, it's time to install the Kali
chroot, which is basically the Kali Linux environment.]

![](){width="2.9114588801399823in"
height="5.176936789151356in"}[From the Kali home screen, click on the NetHunter icon. You will be
prompted with a series of prompts, requesting system
privileges:]

![](){width="3.139734251968504in"
height="5.578125546806649in"}[Click "Allow" on each of the prompt messages. There will be
several.]

[ ] ![](){width="3.145903324584427in"
height="5.598958880139983in"}[From the NetHunter menu (accessible from the icon in the upper
left-hand corner), select "Kali Chroot Manager".]

![](){width="2.8569608486439195in"
height="5.078125546806649in"}[From the Chroot Manager, click "INSTALL KALI CHROOT".]

[ ] ![](){width="2.9052515310586178in"
height="5.161458880139983in"}[In this case, we have the compressed Kali Chroot file already copied to
the device, so we will select "USE SDCARD".]

![](){width="2.963542213473316in"
height="5.287227690288714in"}[Select "FULL CHROOT".]

![](){width="3.1787106299212597in"
height="5.651042213473316in"}[The Chroot will install. This takes some time.]

[fastboot reboot]

["Note many devices will replace your custom recovery automatically during first boot. To prevent this, use [Google](http://www.google.com/) to find the proper key combo to enter recovery. After typing fastboot reboot, hold the key combo and boot to TWRP. Once TWRP is booted, TWRP will patch the stock ROM to prevent the stock ROM from replacing TWRP. If you don\'t follow this step, you will have to repeat the install."] []
----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

[Notes (not for publication)]
----------------------------------------

[Fastboot: For example, on the Nexus 4, 5, 7 and 10, hold down **Volume
Down**, **Volume Up** and **Power** simultaneously to power on the
phone. On a Samsung device, hold down **Volume Down**, **Home** and
**Power**.]

[For each device, hold the buttons until the bootloader menu appears.
For all other devices, simply Google the device name and "fastboot" for
instructions.]

[You can check the state of the bootloader from fastboot since some
devices will show you the lock state on this screen. For example, the
fastboot screen of an unlocked Nexus 5 reports "LOCK STATE -
unlocked".]

From
https://developer.android.com/studio/command-line/adb.html

[Android Debug Bridge (adb) is a versatile command line tool that lets
you communicate with an emulator instance or connected Android device.
It facilitates a variety of device actions, such as installing and
debugging apps, and it provides access a Unix shell that you can use to
run a variety of commands on an emulator or connected device. It is a
client-server program that includes three components:]

-   [A client, which sends commands. The client runs on your development
    > machine. You can invoke a client from a command-line terminal by
    > issuing an adb command.]

-   [A daemon, which runs commands on a device. The daemon runs as a
    > background process on each emulator or device
    > instance.]

-   [A server, which manages communication between the client and the
    > daemon. The server runs as a background process on your
    > development machine.]

[You can find the adb tool in android\_sdk/platform-tools/.]

[The Android Debug Bridge (ADB), is a command-line-based tool that
allows for interaction between your computer and your Linux-based
Android device. Fastboot is an engineering protocol that allows
modification to file system images from a computer over a USB
connection.]

[Both ADB and Fastboot are packaged as a part of the Android Software
Development Kit (SDK), and allow you to perform simple tasks like
pushing and pulling files from your device, but they can also manipulate
your bootloader and install custom recoveries.]

[Usually, you would have to install the Android SDK to get both tools,
but today I\'m going to show you how to get both on your Mac in less
than 5 minutes!]

[How to boot Fastboot Mode for Google Nexus 5:]

[Power off the Nexus 5 completely.]

[Next, press and hold]

[That's it, now you're free to connect your smartphone to your PC and
launch Command Prompt in order to use one of the following fasboot
commands: flash, erase, reboot, devices or format.]

[Simply switch into Multiple recoveries:
http://nexus5.wonderhowto.com/how-to/fastest-way-get-ota-updates-with-custom-recovery-your-nexus-5-0157148/]

[GREAT WRITEUP of everything!:
http://android.stackexchange.com/questions/2885/what-is-the-difference-between-rooting-jailbreak-rom-mod-etc/2891\#2891]

[root/unroot nexus 5:
http://www.tothemobile.com/guide-root-restoreunroot-nexus-5-android-4-2-2-complete-stock]

[Next 5 All in one beginner's guide:
http://forum.xda-developers.com/google-nexus-5/general/how-to-nexus-5-one-beginners-guide-t2510966]

Unlock your bootloader:
http://www.howtogeek.com/239798/how-to-unlock-your-android-phones-bootloader-the-official-way/

[Next, install Team Win Recovery Project: (TWRP):
http://www.howtogeek.com/240047/how-to-flash-twrp-recovery-on-your-android-phone]

[You may need to Google instructions for booting your phone into
recovery. You'll first need to fastboot your phone (using a combination
of buttons when you power on), then enter recovery mode. Try
https://www.recovery-android.com/boot-android-phone-into-recovery-mode.html
for some common procedures.]

[Next, make a backup image of your phone:
http://www.howtogeek.com/240582/how-to-back-up-and-restore-your-android-phone-with-twrp]

["TWRP makes "nandroid" backups, which are near-complete images of your
system. Instead of using them to restore individual files or apps, you
use nandroid backups to restore your phone to exactly the state it was
in when you backed up: the version of Android, your wallpaper, your home
screen, right down to which text messages you had left
unread."]

[BB: "Net hunter is not a ROM, it should be installed over a ROM"..."I
would suggest flashing a second rom in multirom then advanced \>
multirom \> list roms (pick rom) \> flash zip (nethunter)."]

[In response to my sentence, "A locked bootloader verifies the Android
system partition and may restore it to factory stock if it has changed,
while an unlocked bootloader will generally not", jcadduono says:
[[https://docs.google.com/document/d/1ngadJREK0gbfKdauvPtjMkqRvjUnEfcq-5IX-Z4jFn0/edit?userstoinvite=jc%40adduono.com&ts=584857c8&actionButton=1&disco=AAAAA4oSbOs\#heading=h.d6i1z3qtbgrl]{.underline}](https://docs.google.com/document/d/1ngadJREK0gbfKdauvPtjMkqRvjUnEfcq-5IX-Z4jFn0/edit?userstoinvite=jc%40adduono.com&ts=584857c8&actionButton=1&disco=AAAAA4oSbOs#heading=h.d6i1z3qtbgrl)]

> *["nope, a locked bootloader (aboot) verifies aboot images only (boot
> partition, recovery partition, alternate boot/recovery partitions if
> they exist, it only verifies the one it is currently
> loading)]*
>
> []
>
> *[it is the kernel\'s job to verify the integrity of the system
> partition based on a verity key in the android boot image\'s ramdisk.
> the kernel determines which partitions need verity checks (99.9% of
> the time its only /system) by reading the android fstab in the
> ramdisk. (fstab.\$(getprop ro.hardware))]*
>
> []
>
> *[if the system image verity check fails, then the device reboots into
> recovery.]*
>
> []
>
> *[if the boot image \"secure boot\" check fails, usually bootloader
> will send you into recovery.]*
>
> []
>
> *[recovery (sometimes) has the option to flash an OEM signed zip file
> with adb sideload or install from sdcard, this zip is signed by OEM
> and stock recovery only accepts that OEM\'s signature and none
> other.]*
>
> []
>
> *[if both boot and recovery images fail secure boot check then it
> should drop into bootloader/fastboot mode.]*
>
> []
>
> *[aboot is almost always verified by secondary bootloader, and if
> aboot verification fails then you\'re dropped into whatever fallback
> secondary bootloader has set up, which is generally raw flashing
> through some crazy hardware tool/serial. yikes.]*
>
> []
>
> *[nougat comes with verity fec (correction) where if a partition
> appears incorrect it copies correct bits from a backup partition (only
> pixel uses this right now I think? it uses up a ton of extra space on
> ufs having extra partition copies!)"]*

[]













The Android Hacking Landscape

Before we get into any Android hacking, we need to set the stage and discuss some terms and concepts. Anyone can follow instructions, but it’s important to understand the basic concepts because before long, we’ll be getting in pretty deep.

Having said this, we’re going to jump in fairly quickly. There’s simply no sugarcoating this stuff. If you make it through the next few pages, we’ll make a basic Android hacker out of you yet! We think the results are worth it! Let’s get started.

Bootloaders: When you turn on your device, the bootloader executes first, handing over control to the boot image, which contains the phone’s startup files. The boot image runs the device’s kernel (the bridge between the software and hardware), then loads the Android operating system.

If your bootloader is “locked”, you can only flash (short for “write to the device’s flash” or semi-synonymous with “install”) boot images that have been digitally signed by the manufacturer. If your bootloader is unlocked, you can flash third-party boot images. An OEM signed Android boot or recovery image also verifies the Android system partition. If the verification fails on a system boot, it will reboot to recovery. In recovery, it will ask for an OEM signed zip to restore the partition’s integrity or recover the incorrect bits from a duplicate system partition (in the case of newer Nougat devices). While you may be able to temporarily “root” a device (hang in there!) without unlocking the bootloader, in order to extend your root state, you need to unlock.

In most cases, the first step to unlocking the full potential of your Android device is to unlock the bootloader. Unlocking your bootloader may wipe all of your data. Always backup your device before unlocking. We’ll discuss backup strategies once we dive in.

Rooting (which is somewhat similar to “jailbreaking” an Apple device) gives you root access to the Android OS and allows you to modify the software on the device or install other software that isn’t approved (or “signed”) by the manufacturer. Once your bootloader is unlocked, you can run a third-party application (like superuser or supersu) which will grant you full access to the operating system and you can literally change anything you want. Rooting does not erase anything on your device (but unlocking the bootloader likely did that already).

Beware, though.. manufacturers sign their apps to make sure they are “clean”. Running unsigned, untrusted third-party apps can introduce spyware and other malware. In addition, you can “brick” your device if you root it improperly. The odds of this are very slim if you follow the instructions carefully, but if you are careless, and race through instructions there’s a chance  you’ll lose your device. Is the very minimal amount of risk worth it? It boils down to one question: “Are you an Android hacker or not?”

Recovery: A “recovery” is a run environment that resides in a bootable partition on the device which can be activated with a combination of keypresses or command-line instructions from a connected computer. The key function of this environment is to assist in recovering the device. The typical way to load the recovery environment is through the fastboot utility triggered from a connected PC or device key press combination at boot time or through an alternative, OEM-specific ‘Download mode’ tool. The stock recovery for most devices only allows you to reboot, apply a factory (signed) update, wipe and/or factory reset the device or wipe the cache partition. A custom recovery like Team Win Recovery Project (TWRP) or ClockWorkMod extend these capabilities, allowing you to install custom operating systems (or “ROMs”), backup and restore the device, mount external filesystems and more.

Custom ROMs: In Android parlance, “ROM” generally refers to the Android operating system. The traditional acronym for ROM (read-only memory) still applies: the Android operating system is stored in the /system partition which is mounted read-only to standard (non-root) users as opposed to the /data partition which is user-writeable. This is precisely the reason you need to root your device to change the operating system, since it resides on /system. Your device likely came from the factory with a “stock ROM”, while a  “custom ROM” is a customized third-party replacement.

Why install a ROM? You can install the latest and greatest version of Android, without waiting for an OTA (over the air) update, install a version of Android that the manufacturer hasn’t released for your device, or you can install a custom ROM made by a third party. The latter can create problems for you if the developer releases crummy code: you could experience battery life issues because of unoptimized code, you may experience hardware problems from untested devices or you could experience device instability because of poorly written code. However, if you trust that the developers know what they’re doing, a custom ROM can absolutely revolutionize your device.

The two primary branches of Android are Google’s Android Open Source Project (“AOSP”, https://source.android.com) and CyanogenMod (“CM”, http://www.cyanogenmod.com) which although discontinued is being carried on by Lineage (http://lineageos.org). Most other ROMs are derivatives of these, like CoopherheadOS (https://copperhead.co/android) which was designed for security-minded users.

NetHunter wasn’t originally designed to be a ROM, but rather as an “overlay”. The zip file was meant to be flashed over top of an AOSP-type ROM where it would modify parts of the /system partition. In addition, this version of the NetHunter package would add Android apps into /data along with the Kali Linux “chroot” (“simulated root”) environment. This made NetHunter more universal, and the overlay could be installed on just about any rootable / unlocked Android device. However, the primary tradeoff is that the NetHunter “overlay” does not make any modifications to the kernel, meaning certain capabilities won’t work including HID, DriveDroid, external wireless adapters and more. However, a ROM-based version of NetHunter is available which will offer a full compliment of features including centralized updates.
Teamwork: Rooting and Unlocking
Rooting your device enhances your privileges and gives you full access to the Android operating system. This means you can do whatever you like inside Android. There are ways to temporarily root your device, but they are often incomplete, often depend on a specific version-dependent vulnerability and must be run each time you want to execute a root-privileged action. In order to permanently root your device, you must write write a third-party binary (like superuser) and an app to launch that binary into the system partition. However, a locked bootloader blocks write access to the system partition.

We’ve seen the value of installing a custom recovery (which allows full backup and restore, for example) and understand the value of installing a custom ROM (running NetHunter, for example) but in order to do this, we need more than root inside Android, we need the ability to load an alternate operating system, and this is the job of the bootloader. If we can modify (unlock) the bootloader, we can not only install custom ROMs and recoveries, we can gain permanent root within the Android OS.

This is why unlocking the bootloader and rooting are often spoken of in similar context, although we hope by now you understand that they are very different. Once you’ve gained a general grasp on all of this, it’s time to take the next step and backup your data (if necessary) then unlock and root your Android device.


Non-root Data Backup

Back up everything you can before unlocking and rooting your device. If you’ve got a new device, you can safely skip this step. We generally recommend that you unlock and root your device while it is brand new so you can avoid the unpleasantries of backing up a non-rooted device.

There are several methods you can use to backup your data without rooting your device. Note that these will NOT be full system backups, but rather only partial data copies since you don’t have root access to the device.

First, you can use the “manual method” to copy data from the device. Simply plug in the device, and use the file manager (on Windows), the Android File Transfer program (download to use on OSX) or mount (on Linux) to copy the files and folders that you’re interested in. Note that some of these methods may fail when you copy system folders and files (like those in the Android directory). Pay attention to obvious folders like DCIM and Pictures and spend time navigating the file tree to copy folders and files that are valuable to you:


Second, you can use the built-in Google sync functions to back up various files to your Google online storage space. First, navigate to Settings -> Backup & Reset, ensure that “Back up my data” is checked and that a “Backup account” is selected:



Next, check out Settings -> Accounts -> Google and select the backup account you set up to see the options you are currently syncing:



Each of these items, if selected, will be copied from your device to the Google cloud, and can be restored later from any Android device, when you sign in with that account. Be sure to select “Sync now” from the Sync menu to synchronize data before unlocking your bootloader:



There is no easy way to be absolutely sure that all your content is backed up (despite the “Last synced” date that is shown) since there is no obvious indicator that a sync has completed. However, in some apps, like Google Photos, different icons may appear that show whether or not a photo or folder has been synced. Note the cloud icon () that indicates a folder has not been synced:


You can click this icon to synchronize that folder, and repeat for any other folders that have not been synced. In the case of photos, you can connect to http://photos.google.com (logged in with the “backup account” you used in settings) to confirm that your photos have been synchronized:


You may be able to repeat this process with other synchronized selections in Settings -> Accounts, but generally speaking, you’ll be placing a lot of faith in Google’s automated sync services. Note that you can use both methods together if you’re paranoid.

If you have synchronized your contacts, you can review and verify them at http://contacts.google.com:



At this point, most of your data should be backed up, although you should be very careful when it comes to app data that you are paranoid about losing. For example, if you have expense reports, receipts, or other sensitive data that is captured by a third-party (non-Google) app, you should use the “export” or “share” options of those apps to get the data off your device before proceeding. There are also a plethora of Apps on the Play Store that will do various levels of backups. Use them in addition to these techniques if you are extremely paranoid about losing data.

If you think this is a bit scary and a serious pain in the rear-end, you’re right. Stock Android devices only allow you to do full and complete backups through the Google infrastructure or with a mishmash of backups apps, built-in syncs, in-app exports and the tossing of copious chicken bones and prayer for good measure. Wouldn’t it be nice to launch a full low-level backup that grabs absolutely everything from your device and stores it in a format that you can later restore?  Then you’d be free to try out different operating systems and tweak your phone without fear of losing anything you care about. Even better, wouldn’t it be great to have more than one specialized operating system installed on your device that you could boot as needed? Well, you can do all of that and more once your device is rooted. So let’s get to it.



Preparing for Bootloader Unlock

Now it’s time to take your first step towards hacking your Android device. First, we will unlock the bootloader and then we will root the device. To begin, we’ll install some tools.

Installing USB drivers

If you connect your device to your computer with a USB cable, and you can’t “see” it in your file manager, you may need to install USB to allow your device to communicate with your computer. On a Mac, you can simply install the Android File Transfer utility (https://www.android.com/filetransfer), while on Windows, you can install the “naked”  ADB drivers (http://www.xda-developers.com/universal-naked-driver-solves-your-adb-driver-problems-on-windows) or the Universal ADB drivers (http://www.koushikdutta.com/post/universal-adb-driver). For Linux, you may need to simply search the internet for “USB driver”, the name of your phone and the name of your operating system, or simply “android usb drivers linux”.
Installing Android Studio Tools

Next, we’ll need to install the adb and fastboot tools. If you’re on a Windows machine, you can install Minimal ADB and Fastboot by shimp208 (https://forum.xda-developers.com/showthread.php?t=2317790) which is a compact version of the tools available from the much larger Android Studio bundle. Alternatively, you can download the full Android Studio for any OS from https://developer.android.com/studio/index.html. Install and run the Studio installer, then locate the adb and fastboot commands which are under /Users/<user>/Library/Android/sdk/platform-tools on OSX and \Users\<user>\sdk\platform-tools on Windows.

Enabling Developer Mode

The developer options in Settings -> Developer Options come in handy for all sorts of things, but the option may not be visible on your device. To enable it,  navigate to Settings -> About device -> Software and tap on the Build Number setting seven times.

You should now be able to navigate to Developer Options near the bottom of the Settings menu. Enable USB debugging from Settings -> Developer on your device. It is listed as  “Android Debugging - Enable the Android Debug (adb) Interface” or “USB Debugging”:



Once you enable this option, connect your device via USB, and you should see a prompt on your device asking you to approve your computer’s RSA fingerprint:



Approve this option, and for ease of use, “Always Allow” this device to perform USB debugging commands. If you do not see this prompt, double-check that you have USB debugging enabled.

You can test the installation of the Android Studio command line tools by changing into the appropriate sdk directory and executing the following on Windows:

adb version

or the following on OSX:

./adb version

The result of the preceding command  should be something like this:

Android Debug Bridge version 1.0.36
Revision 8453623d5db51-android

Next, you can check for your connected device with adb devices (on Windows) or ./adb devices (on OSX). The output should show your device ID and the word “device”:

$ ./adb devices
List of devices attached
070577f007a446		device

If instead of device, you see unauthorized, you have not properly enabled USB debugging or accepted your computer’s RSA fingerprint. Try again.

Sidebar: Note that all OSX and Linux commands should likely be preceded with the “./” characters unless the commands are in your path. For brevity, we won’t reiterate this again.

With adb and fastboot installed and your device connected and responsive, let’s now check for safety features that may be in place that could prevent bootloader unlocking.



Enabling OEM Unlock

If you are running a Google AOSP-based operating system that’s been updated to version 5.0 (“Lollipop”) or newer,  you’ll need to make sure the “OEM Unlock” security feature is enabled. This setting was put in place to prevent someone from flashing your phone and accessing your data without your consent.

To enable this feature, navigate to Settings -> Developer and enable OEM unlock.

If you don’t see this setting, you do not have this safety feature.

Unlocking the OnePlus One Bootloader
With our environment configured now it’s finally time to unlock the bootloader. We will begin in fastboot mode.
Entering Fastboot Mode

The most common way to fastboot is to power off your device and then power it on again, while holding down a specific key combination.

On a OnePlus One, hold down Volume Up and Power at the same time.

Alternatively, you can fastboot into the bootloader menu from the command line. Connect your device to your PC via USB and type adb reboot bootloader.

Checking Bootloader Status

Before unlocking the bootloader, check to see if it’s already unlocked. Use fastboot oem device-info to list the device info. You should see something like:

$ ./fastboot oem device-info
...
(bootloader) 	Device tampered: true
(bootloader) 	Device unlocked: true
(bootloader) 	off-mode-charge: true
OKAY [  0.101s]
finished. total time: 0.101s

The “Device unlocked” line shows the lock state of the device. If your device is already unlocked, you can skip to rooting your device. Otherwise, you’ll need to unlock your bootloader.

The Moment of Truth

Well, you’ve made it this far. No sense backing down now. Welcome to the moment of truth. Your unlock is moments away. First, make sure you’re in fastboot mode:

$ ./fastboot devices
4814f2ca	fastboot



With the device in fastboot mode, unlock the bootloader and reboot your device:

$ ./fastboot oem unlock
...
OKAY [  0.002s]
finished. total time: 0.002s
$ ./fastboot reboot
rebooting...

finished. total time: 0.004s

After rebooting, head back into fastboot mode:

$ ./adb reboot bootloader

Now, you’ll need a replacement recovery. We are going to use a ROM that has been pre-rolled to include the TWRP recovery, including multirom support, compiled by the NetHunter team. It can be downloaded from: https://build.nethunter.com/nethunteros/CM14_1/bacon/twrp-multirom-bacon-20161212-01.img.

Copy the .img file to the same folder as adb and fastboot. For example, if we are in the adb directory, and we downloaded the image to ~/Downloads:

$ cp /Users/j/Downloads/twrp-multirom-bacon-20161212-01.img  .

With the img file in place, verify that the file size looks sane. A zero length file, for example, would be a bad thing to flash.

$ ls -l twrp-multirom-bacon-20161212-01.img
-rw-r--r--  1 root  staff  13893632 Jan  3 19:26 twrp-multirom-bacon-20161212-01.img

Next, check that you are in fastboot and flash the image:

$ ./fastboot devices
4814f2ca	fastboot
$ ./fastboot flash recovery twrp-multirom-bacon-20161212-01.img
target reported max download size of 1073741824 bytes
sending 'recovery' (13568 KB)...
OKAY [  0.427s]
writing 'recovery'...
OKAY [  0.185s]
finished. total time: 0.612s

Now that the recovery is flashed, hold down Volume-Down and Power until the system reboots. Let go of all keys when you see the OnePlus One logo. The TWRP will load and the Read-Only system prompt will be shown:



Swipe to allow system modifications. This is one of the joys of a custom recovery.

Wiping the device
After swiping to allow system modifications,  you will be taken to the TWRP main menu.



Select Wipe to be taken to the wipe menu:


Select Advanced Wipe to open the advanced wipe options:



Select “Dalvik / ART Cache”, “System” and “Data” and swipe to wipe the device. You will see the status:


Now, select Back to return to the main menu.
Installing NetHunter

You’ll need to download quite a few files before we get started.

The Multirom Installer allows you to have more than one ROM on the system at a time: https://build.nethunter.com/nethunteros/CM14_1/bacon/multirom-20161206-v33e-unofficial-bacon.zip

Nethunter OS: https://build.nethunter.com/nethunteros/CM14_1/bacon/los-14.1-20170103-bacon-nethunteros.zip

NetHunter Kali FS (chroot): https://images.offensive-security.com/kalifs-full.tar.xz

You’ll also need a secondary ROM to act as your day-to-day “legitimate” phone image. As a reminder, it’s never a good idea to use NetHunter as a day-to-day device OS. A good choice is the last official CyangenOS release: https://www.androidfilehost.com/?fid=24591000424960111

Copy each of these files to your device. You can create a new directory to keep things organized. We will use 0SourceFiles so the directory sorts to the top of the Android file listing. On your local machines, from the adb directory, create a directory and copy all the zip files into that directory. Make sure to copy the Kali chroot file to the the director adb is in, not into 0SourceFiles. The Kali chroot installer will search for this file in the root of your internal storage, not a subfolder.

$ mkdir 0SourceFiles
$ cd 0SourceFiles/
$ cp ~/Downloads/multirom-20161206-v33e-unofficial-bacon.zip  .
$ cp ~/Downloads/los-14.1-20170103-bacon-nethunteros.zip .
$ cp ~/Downloads/cm-13.1.2-ZNH2KAS3P0-bacon-signed.zip .
$ cd ..
$ cp ~/Downloads/kalifs-full.tar.xz .


Next, push the files to the device with adb push:

$ ./adb push 0SourceFiles/ /sdcard
/sdcard/0SourceFiles/: 4 files pushed. 0 file...pped. 4.7 MB/s (2088925658 bytes in 425.582s
$ ./adb push kalifs-full.tar.xz /sdcard

Installing NetHunter OS

Now, from the main TWRP menu, select INSTALL and browse to the folder containing your zip files:



Select the los-14.1-20170103-bacon-nethunteros.zip file, and click “Install Image”.



Check “Inject MultiROM after installation” (optional), and uncheck “Zip Signature verification” unless you also downloaded an MD5 file for this zip file. Swipe to confirm and begin the flash process.



The flash process should complete normally. Click back to return to the main menu.



Install the MultiROM Installer

.

Click “Install” from the main TWRP menu.



Select the multirom-20161206-v33e-unofficial-bacon.zip file and click “Install Image”.


Check “Inject MultiROM after installation” (optional), and uncheck “Zip Signature verification” unless you also downloaded an MD5 file for this zip file. Swipe to confirm and begin the flash process.



Once the flash completes, press back to return to the TWRP main menu.


Install Secondary ROM

Next, install the secondary “daily use” ROM, which in this case is the last release of Cyanogen before converting to Lineage.



From the TWRP main menu, click the button under the battery indicator to reveal the MultiROM menu:


Click “Add ROM”.


Check “Android”, and select your first Internal Storage device. Click “Next” to continue.


Click “Zip File” to load the secondary ROM from your internal storage.


Swipe to confirm flashing the secondary ROM.


Click “Reboot System” to restart your device. When the device reboots, you will have a new boot menu. Select “Internal” to boot into NetHunter. The device will likely return to the boot menu again (as supersu loads it’s one-time installer). Simply select “Internal” after the second reboot to load NetHunter.


Installing the Kali Chroot

Now that the NetHunter OS is installed, it’s time to install the Kali chroot, which is basically the Kali Linux environment.



From the Kali home screen, click on the NetHunter icon. You will be prompted with a series of prompts, requesting system privileges:



Click “Allow” on each of the prompt messages. There will be several.


From the NetHunter menu (accessible from the icon in the upper left-hand corner), select “Kali Chroot Manager”.



From the Chroot Manager, click “INSTALL KALI CHROOT”.



In this case, we have the compressed Kali Chroot file already copied to the device, so we will select “USE SDCARD”.



Select “FULL CHROOT”.



The Chroot will install. This takes some time.







fastboot reboot

“Note many devices will replace your custom recovery automatically during first boot. To prevent this, use Google to find the proper key combo to enter recovery. After typing fastboot reboot, hold the key combo and boot to TWRP. Once TWRP is booted, TWRP will patch the stock ROM to prevent the stock ROM from replacing TWRP. If you don't follow this step, you will have to repeat the install.”
Notes (not for publication)



Fastboot: For example, on the Nexus 4, 5, 7 and 10, hold down Volume Down, Volume Up and Power simultaneously to power on the phone. On a Samsung device, hold down Volume Down, Home and Power.

For each device, hold the buttons until the bootloader menu appears. For all other devices, simply Google the device name and “fastboot” for instructions.

You can check the state of the bootloader from fastboot since some devices will show you the lock state on this screen. For example, the fastboot screen of an unlocked Nexus 5 reports “LOCK STATE - unlocked”.

From https://developer.android.com/studio/command-line/adb.html :


Android Debug Bridge (adb) is a versatile command line tool that lets you communicate with an emulator instance or connected Android device. It facilitates a variety of device actions, such as installing and debugging apps, and it provides access a Unix shell that you can use to run a variety of commands on an emulator or connected device. It is a client-server program that includes three components:
A client, which sends commands. The client runs on your development machine. You can invoke a client from a command-line terminal by issuing an adb command.
A daemon, which runs commands on a device. The daemon runs as a background process on each emulator or device instance.
A server, which manages communication between the client and the daemon. The server runs as a background process on your development machine.

You can find the adb tool in android_sdk/platform-tools/.

The Android Debug Bridge (ADB), is a command-line-based tool that allows for interaction between your computer and your Linux-based Android device. Fastboot is an engineering protocol that allows modification to file system images from a computer over a USB connection.

Both ADB and Fastboot are packaged as a part of the Android Software Development Kit (SDK), and allow you to perform simple tasks like pushing and pulling files from your device, but they can also manipulate your bootloader and install custom recoveries.
Usually, you would have to install the Android SDK to get both tools, but today I'm going to show you how to get both on your Mac in less than 5 minutes!


How to boot Fastboot Mode for Google Nexus 5:
Power off the Nexus 5 completely.
Next, press and hold

That’s it, now you’re free to connect your smartphone to your PC and launch Command Prompt in order to use one of the following fasboot commands: flash, erase, reboot, devices or format.




Simply switch into Multiple recoveries: http://nexus5.wonderhowto.com/how-to/fastest-way-get-ota-updates-with-custom-recovery-your-nexus-5-0157148/




GREAT WRITEUP of everything!: http://android.stackexchange.com/questions/2885/what-is-the-difference-between-rooting-jailbreak-rom-mod-etc/2891#2891

root/unroot nexus 5: http://www.tothemobile.com/guide-root-restoreunroot-nexus-5-android-4-2-2-complete-stock

Next 5 All in one beginner’s guide: http://forum.xda-developers.com/google-nexus-5/general/how-to-nexus-5-one-beginners-guide-t2510966

Unlock your bootloader: http://www.howtogeek.com/239798/how-to-unlock-your-android-phones-bootloader-the-official-way/

Next, install Team Win Recovery Project: (TWRP): http://www.howtogeek.com/240047/how-to-flash-twrp-recovery-on-your-android-phone.

You may need to Google instructions for booting your phone into recovery. You’ll first need to fastboot your phone (using a combination of buttons when you power on), then enter recovery mode. Try https://www.recovery-android.com/boot-android-phone-into-recovery-mode.html for some common procedures.

Next, make a backup image of your phone: http://www.howtogeek.com/240582/how-to-back-up-and-restore-your-android-phone-with-twrp

“TWRP makes “nandroid” backups, which are near-complete images of your system. Instead of using them to restore individual files or apps, you use nandroid backups to restore your phone to exactly the state it was in when you backed up: the version of Android, your wallpaper, your home screen, right down to which text messages you had left unread.”

BB: “Net hunter is not a ROM, it should be installed over a ROM”…”I would suggest flashing a second rom in multirom then advanced > multirom > list roms (pick rom) > flash zip (nethunter).”

In response to my sentence, “A locked bootloader verifies the Android system partition and may restore it to factory stock if it has changed, while an unlocked bootloader will generally not”, jcadduono says: https://docs.google.com/document/d/1ngadJREK0gbfKdauvPtjMkqRvjUnEfcq-5IX-Z4jFn0/edit?userstoinvite=jc%40adduono.com&ts=584857c8&actionButton=1&disco=AAAAA4oSbOs#heading=h.d6i1z3qtbgrl

“nope, a locked bootloader (aboot) verifies aboot images only (boot partition, recovery partition, alternate boot/recovery partitions if they exist, it only verifies the one it is currently loading)

it is the kernel's job to verify the integrity of the system partition based on a verity key in the android boot image's ramdisk. the kernel determines which partitions need verity checks (99.9% of the time its only /system) by reading the android fstab in the ramdisk. (fstab.$(getprop ro.hardware))

if the system image verity check fails, then the device reboots into recovery.

if the boot image "secure boot" check fails, usually bootloader will send you into recovery.

recovery (sometimes) has the option to flash an OEM signed zip file with adb sideload or install from sdcard, this zip is signed by OEM and stock recovery only accepts that OEM's signature and none other.

if both boot and recovery images fail secure boot check then it should drop into bootloader/fastboot mode.

aboot is almost always verified by secondary bootloader, and if aboot verification fails then you're dropped into whatever fallback secondary bootloader has set up, which is generally raw flashing through some crazy hardware tool/serial. yikes.

nougat comes with verity fec (correction) where if a partition appears incorrect it copies correct bits from a backup partition (only pixel uses this right now I think? it uses up a ton of extra space on ufs having extra partition copies!)”






