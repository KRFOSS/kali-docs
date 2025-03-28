---
title: Building NetHunter
description:
icon:
weight: 10
author: ["g0tmi1k",]
---

### Clone

Those of you who want to build a Kali NetHunter image from our GitLab repository may do so using our Python build-scripts:

```console
kali@kali:~$ git clone https://gitlab.com/kalilinux/nethunter/build-scripts/kali-nethunter-installer.git
[...]
kali@kali:~$
kali@kali:~$ cd kali-nethunter-installer/
```

- - -

### Bootstrap

Before you can build for a device, you will need to enter the **kali-nethunter-installer** directory and run `./bootstrap.sh`. This will ask you a few questions before downloading the devices folder.

```console
kali@kali:~$ ./bootstrap.sh
[?] Would you like to grab the full history of kernels? (y/N):
[?] Would you like to use SSH authentication (faster, but requires a GitLab account with SSH keys)? (y/N): N
[i] Running command: git clone --depth 1 https://gitlab.com/kalilinux/nethunter/build-scripts/kali-nethunter-kernels.git kernels
Cloning into 'kernels'...
[...]
kali@kali:~$
```

- - -

### Help

The main build-script is also located in the **kali-nethunter-installer** directory and can be used to build images for multiple devices and Android OS versions as shown below:

```console
kali@kali:~/kali-nethunter-installer$ python3 build.py -h
[i] Reading: kernels/devices.yml
usage: build.py [-h] [--generic ARCH] [--kernel KERNEL] [--kitkat] [--lollipop] [--marshmallow] [--nougat] [--oreo] [--pie] [--ten] [--eleven] [--twelve] [--thirteen] [--fourteen] [--wearos] [--rootfs SIZE]
                [--force-download] [--uninstaller] [--installer] [--no-installer] [--no-branding] [--no-freespace-check] [--supersu] [--release VERSION]

Kali NetHunter recovery flashable zip builder

options:
  -h, --help            show this help message and exit
  --generic ARCH, -g ARCH
                        Build a generic installer (modify ramdisk only)
  --kernel KERNEL, -k KERNEL
                        Allowed kernel IDs: a37-los a5ulte-cm a5ulte-tw a5xelte-tw a7xelte-los ailsa-ii alioth-los-ksu angler angler-los armani beryllium beryllium-los blueline bramble bullhead cancro-cm
                        cedric-los crosshatch davinci-miui dogo-cm dragon drg-los es2 eva-emui eva-los flo flo-cm flounder gemini4g-p1 gemini4g-p2 gemini4g-p3 gracelte graceltekor grouper gts4llte gts4lwifi
                        h830 h850 h918 h990 h990-los hammerhead hammerhead-los hammerhead-caf-los hero2lte-tw hero2lte-los hero2lte-oui hero2lte-kor-tw herolte-tw herolte-los herolte-oui herolte-kor-tw
                        hlte-can-cm hlte-can-tw hlte-dcm-tw hlte-eur-cm hlte-eur-tw hlte-eur-los hlte-kdi-tw hlte-kor-cm hlte-kor-tw hlte-spr-cm hlte-spr-tw hlte-vzw-tw honami-los htc-pmewl ido-los j53g-los
                        j5lte-los j5nlte-los j7y17lte-oui jalebi-los jfltexx-cm jiayus3a kiwi-cm klte-los klte-tw klte-chn-los klte-chnduo-los klte-duos-los klte-duos-tw klte-kdi-los klte-kdi-tw klte-kor-
                        los klte-skt-tw klte-spr-los klte-spr-tw klte-usc-tw klte-vzw-tw kminilte-los laurel-sprout-los lv517-los mako mako-cm manning manta markw-los mocha-cm on7xlte-oui onem7gpe onem8gpe
                        oneplus-nord-oos oneplus1-cm oneplus1-los oneplus2-oos oneplus2-cm oneplus2-los oneplus3-3t-oos oneplus3-3t-los oneplus3-oos oneplus3-v2-oos oneplus3-los oneplus3t-los oneplus3t-oos
                        oneplus5-oos oneplus5-cm oneplus5-los oneplus5-pa oneplus5-los-ksu oneplus5-pa-ksu oneplus6-oos oneplus6-los oneplus7-oos oneplus7-v2-oos oneplus8-oos oneplusx-cm osprey-los payton-
                        los pdx201-los pl2-los potter-los r8q-oui rmx1911-cos rmx1971 rmx2185-los rmx3031 s2-cm sakura-los santoni-miui mido-pe11 shamu shamu-los shieldtablet spacewar star2lte-los sunfish
                        surya-los suzuran-los ticwatchpro ticwatchpro3 us996 vayu victara-cm vienna yuga-cm z00e-los zeroflte-los zeroflte-tw zerolte-los zerolte-tw
  --kitkat, -4          Android 4.4
  --lollipop, -5        Android 5
  --marshmallow, -6     Android 6
  --nougat, -7          Android 7
  --oreo, -8            Android 8
  --pie, -9             Android 9
  --ten, -10            Android 10
  --eleven, -11         Android 11
  --twelve, -12         Android 12
  --thirteen, -13       Android 13
  --fourteen, -14       Android 14
  --wearos, -w          Wear OS
  --rootfs SIZE, -fs SIZE
                        Build with Kali rootfs (full, minimal or nano)
  --force-download, -f  Force re-downloading external resources
  --uninstaller, -u     Create an uninstaller
  --installer, -i       Build kernel installer only
  --no-installer        Build without the kernel installer
  --no-branding         Build without wallpaper or boot animation
  --no-freespace-check  Build without free space check
  --supersu, -su        Build with SuperSU installer included
  --release VERSION, -r VERSION
                        Specify NetHunter release version
kali@kali:~/kali-nethunter-installer$
```

- - -

### Examples
#### Example \#1

To build an Android 10 image with Kalifs for a OnePlus 7 device, we would run **build.py** as follows:

```console
kali@kali:~/kali-nethunter-installer$ python3 build.py -k oneplus7-oos --ten -fs full
[i] Reading: kernels/devices.yml
[i] Kernel ID: oneplus7-oos
[i] Android version: ten
[i] NetHunter release version: 20241022_132726
[i] rootfs: full
[i] From: kernels/devices.yml
[i]   kernelstring: NetHunter kernel
[i]   devicenames :  ['OnePlus7', 'oneplus7', 'guacamoleb', 'Guacamoleb', 'OnePlus7Pro', 'GM1915', 'GM1910', 'guacamole', 'Guacamole', 'OnePlus7T', 'OnePlus7TPro']
[i]   arch        : arm64
[i]   flasher     : anykernel
[i]   ramdisk     : auto
[i]   resolution  : 1080x2340
[i]   block       : /dev/block/bootdevice/by-name/boot
[i]   version     : 1.0
[i]   supersu     : auto
[i]   modules     : 1
[i]   slot_device : 1
[i]   author      : Re4son & yesimxev
[i] Downloading all NetHunter apps
[...]
[+] Finished creating zip
[i] Adding Kali rootfs archive to the installer zip
[+]   Added: kali-nethunter-rootfs-full-arm64.tar.xz
[+] Finished adding rootfs
[+] Created Kali NetHunter installer: nethunter-20241022_132726-oneplus7-oos-ten-kalifs-full.zip
kali@kali:~/kali-nethunter-installer$
kali@kali:~/kali-nethunter-installer$ ls -lh nethunter-20241022_132726-oneplus7-oos-ten-kalifs-full.zip
-rw-r--r-- 1 root root 2.3G Oct 22 13:28 nethunter-20241022_132726-oneplus7-oos-ten-kalifs-full.zip
kali@kali:~/kali-nethunter-installer$
```

#### Example \#2

To build an app and scripts updater image for a OnePlus 7 device, we would run **build.py** as follows:

```console
kali@kali:~/kali-nethunter-installer$ python3 build.py -k oneplus7-oos --ten
```

#### Example \#3

To build a installer updater image for a OnePlus 7 device, we would run **build.py** as follows:

```console
kali@kali:~/kali-nethunter-installer$ python3 build.py -k oneplus7-oos --ten -i
```

The resulting zip file image will be created in the **kali-nethunter-installer** directory – this is the zip file you will need to flash on your device later on.
