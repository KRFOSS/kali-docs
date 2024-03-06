---
title: Building NetHunter
description:
icon:
weight: 10
author: ["g0tmi1k",]
---

Those of you who want to build a Kali NetHunter image from our GitLab repository may do so using our Python build scripts:

```console
kali@kali:~$ git clone https://gitlab.com/kalilinux/nethunter/build-scripts/kali-nethunter-project.git
kali@kali:~$
kali@kali:~$ cd kali-nethunter-project/nethunter-installer/
```

Before you can build for a device, you will need to enter the **nethunter-installer** directory and run `./bootstrap.sh`. This will ask you a few questions before downloading the devices folder.

The main build script is also located in the **nethunter-installer** directory and can be used to build images for multiple devices and Android OS versions as shown below:

```console
kali@kali:~/kali-nethunter/nethunter-installer$ python3 build.py -h
usage: build.py [-h] [--device DEVICE] [--kitkat] [--lollipop] [--marshmallow] [--nougat] [--oreo] [--pie]
                [--ten] [--forcedown] [--uninstaller] [--kernel] [--nokernel] [--nobrand] [--nofreespace]
                [--supersu] [--nightly] [--generic ARCH] [--rootfs SIZE] [--release VERSION]

Kali NetHunter recovery flashable zip builder

optional arguments:
  -h, --help            show this help message and exit
  --device DEVICE, -d DEVICE
                        Allowed device names: a5ulte a5ulte-touchwiz a5xelte ailsa_ii angler angler-los armani beryllium bullhead cancrocm catfish cedric davinci-miui dogo dragon
                        drg es2 flo flocm flounder gemini4g_p1 gemini4g_p2 gemini4g_p3 gracelte graceltekor gts4llte gts4lwifi grouper h830 h850 h918 h990 hammerhead
                        hammerheadcafcm herolte heroltekor herolte-cm hero2lte hero2ltekor hero2lte-cm r8q hltecan hltecan-touchwiz hltedcm-touchwiz hlteeur hlteeur-touchwiz
                        hlteeur-cm hltekdi-touchwiz hltekor hltekor-touchwiz hltespr hltespr-touchwiz hltevzw-touchwiz honami htc_pmewl ido j7y17lte jalebi jfltexx jiayus3a kiwi
                        klte kltechn kltechnduo klteduos kltekdi kltekor kltespr klte-touchwiz klteduos-touchwiz kltekdi-touchwiz klteskt-touchwiz kltespr-touchwiz klteusc-
                        touchwiz kltevzw-touchwiz kminilte laurel_sprout mako makocm manning manta markw mocha on7xlte onem7gpe onem8gpe oneplus1 oneplus2cm oneplus2oos
                        oneplus3-any oneplus3 oneplus3-cm oneplus3T-cm oneplus3-oos oneplus3T-oos oneplus3T-los oneplus5-oos oneplus5-cm oneplus6-oos oneplus7-oos oneplus7-all
                        oneplus_nord oneplusxcm osprey payton-los pl2 potter s2 santoni-miui shamu shamucm shieldtablet suzuran us996 vayu victara vienna yuga Z00E zeroflte
                        zeroflte-tw zerolte zerolte-tw
  --kitkat, -kk         Android 4.4.4
  --lollipop, -l        Android 5
  --marshmallow, -m     Android 6
  --nougat, -n          Android 7
  --oreo, -o            Android 8
  --pie, -p             Android 9
  --ten, -q             Android 10
  --eleven, -R          Android 11
  --wearos, -w          WearOS
  --forcedown, -f       Force redownloading
  --uninstaller, -u     Create an uninstaller
  --kernel, -k          Build kernel installer only
  --nokernel, -nk       Build without the kernel installer
  --nobrand, -nb        Build without wallpaper or boot animation
  --nofreespace, -nf    Build without free space check
  --supersu, -su        Build with SuperSU installer included
  --nightly, -ni        Use nightly mirror for Kali rootfs download (experimental)
  --generic ARCH, -g ARCH
                        Build a generic installer (modify ramdisk only)
  --rootfs SIZE, -fs SIZE
                        Build with Kali chroot rootfs (full, minimal or nano)
  --release VERSION, -r VERSION
                        Specify NetHunter release version
kali@kali:~/kali-nethunter/nethunter-installer$
```

To build an Android 10 image with Kalifs for a OnePlus 7 device, we would run **build.py** as follows:

```console
kali@kali:~/kali-nethunter/nethunter-installer$ python3 build.py -d oneplus7-oos --ten -fs full
```

To build an app and scripts updater image for a OnePlus 7 device, we would run **build.py** as follows:

```console
kali@kali:~/kali-nethunter/nethunter-installer$ python3 build.py -d oneplus7-oos --ten
```

To build a kernel updater image for a OnePlus 7 device, we would run **build.py** as follows:

```console
kali@kali:~/kali-nethunter/nethunter-installer$ python3 build.py -d oneplus7-oos --ten -k
```

The resulting zip file image will be created in the **nethunter-installer** directory – this is the zip file you will need to flash on your device later on.
