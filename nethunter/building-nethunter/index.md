---
title: Building NetHunter
description:
icon:
date: 2019-11-29
type: post
weight: 1000
author: ["g0tmi1k",]
tags: ["",]
keywords: ["",]
og_description:
---

Those of you who want to build a NetHunter image from our GitLab repository may do so using our Python build scripts.

```markdown
root@kali:~# git clone https://gitlab.com/kalilinux/nethunter/build-scripts/kali-nethunter-project
root@kali:~# cd kali-nethunter/nethunter-installer
```
Before you can build for a device, you will need to enter the **nethunter-installer** directory and run `./bootstrap.sh`. This will ask you a few questions before downloading the devices folder.

The main build script is also located in the **nethunter-installer** directory and can be used to build images for multiple devices and Android OS versions as shown below:

```html
root@kali:~/kali-nethunter/nethunter-installer# python build.py -h
usage: build.py [-h] [--device DEVICE] [--kitkat] [--lollipop] [--marshmallow]
                [--nougat] [--forcedown] [--uninstaller] [--kernel]
                [--nokernel] [--nosu] [--generic ARCH] [--rootfs SIZE]
                [--release VERSION]

Kali NetHunter recovery flashable zip builder

optional arguments:
  -h, --help            show this help message and exit
  --device DEVICE, -d DEVICE
                        Allowed device names: htc_pmewl manta flounder flocm
                        flo grouper angler shamu shamucm bullhead
                        hammerheadmon hammerheadcm hammerhead makocm mako
                        shieldtablet oneplusxcm oneplus2cm oneplus2 oneplus3
                        oneplus1 h830 h850 hlteeur hltecan hltespr hltekor
                        hlteeur-touchwiz hltecan-touchwiz hltespr-touchwiz
                        hltekor-touchwiz hltedcm-touchwiz hltekdi-touchwiz
                        jfltexx klte kltekdi kltespr kltevzw kltechn klte-
                        touchwiz klteduos-touchwiz kltespr-touchwiz klteusc-
                        touchwiz kltevzw-touchwiz klteskt-touchwiz kltekdi-
                        touchwiz cancrocm a5ulte a5ulte-touchwiz
  --kitkat, -kk         Android 4.4.4
  --lollipop, -l        Android 5
  --marshmallow, -m     Android 6
  --nougat, -n          Android 7
  --forcedown, -f       Force redownloading
  --uninstaller, -u     Create an uninstaller
  --kernel, -k          Build kernel installer only
  --nokernel, -nk       Build without the kernel installer
  --nosu, -ns           Build without SuperSU installer
  --generic ARCH, -g ARCH
                        Build a generic installer (modify ramdisk only)
  --rootfs SIZE, -fs SIZE
                        Build with Kali chroot rootfs (full or minimal)
  --release VERSION, -r VERSION
                        Specify NetHunter release version
root@kali:~/kali-nethunter/nethunter-installer#
```

To build a Lollipop image for a OnePlus One device, we would run **build.py** as follows:

```markdown
root@kali:~/kali-nethunter/nethunter-installer# python build.py -d oneplus1 --lollipop
```

The resulting zip file image will be created in the **nethunter-installer** directory – this is the zip file you will need to flash on your device later on.
