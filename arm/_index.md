---
title: Kali On ARM
description: Everything about ARM devices
icon: ti-harddrive
weight: 30
author: ["",]
---

For a device, image and kernel overview, see [arm.kali.org](https://arm.kali.org/)

<!--
| Device | [Build Script](https://gitlab.com/kalilinux/build-scripts/kali-arm/) | [Official Image](/get-kali/) | Community Image | Retired Image |
|--------|--------------|----------------|-----------------|---------------|
| [Banana Pi](/docs/arm/banana-pi/)                                                         | [link](https://gitlab.com/kalilinux/build-scripts/kali-arm/-/blob/main/banana-pi.sh)                       | x |   |   |
| [Banana Pro](/docs/arm/banana-pro/)                                                       | [link](https://gitlab.com/kalilinux/build-scripts/kali-arm/-/blob/main/banana-pro.sh)                      | x |   |   |
| [BeagleBone Black](/docs/arm/beaglebone-black/)                                           | [link](https://gitlab.com/kalilinux/build-scripts/kali-arm/-/blob/main/beaglebone-black.sh)                |   | x |   |
| [Chromebook Exynos (HP daisy_spring)](/docs/arm/chromebook-exynos/)                       | [link](https://gitlab.com/kalilinux/build-scripts/kali-arm/-/blob/main/chromebook-exynos.sh)               |   | x |   |
| [Chromebook Exynos (Samsung daisy_snow/peach_pi/peach_pit)](/docs/arm/chromebook-exynos/) | [link](https://gitlab.com/kalilinux/build-scripts/kali-arm/-/blob/main/chromebook-exynos.sh)               |   | x |   |
| [Chromebook Nyan (Acer Tegra)](/docs/arm/chromebook-nyan/)                                | [link](https://gitlab.com/kalilinux/build-scripts/kali-arm/-/blob/main/chromebook-nyan.sh)                 |   | x |   |
| [Chromebook Veyron (ASUS Flip)](/docs/arm/chromebook-veyron/)                             | [link](https://gitlab.com/kalilinux/build-scripts/kali-arm/-/blob/main/chromebook-veyron.sh)               |   | x |   |
| [CubieBoard2](/docs/arm/cubieboard2/)                                                     | [link](https://gitlab.com/kalilinux/build-scripts/kali-arm/-/blob/main/cubieboard2.sh)                     |   | x |   |
| [CubieTruck (CubieBoard3)](/docs/arm/cubietruck/)                                         | [link](https://gitlab.com/kalilinux/build-scripts/kali-arm/-/blob/main/cubietruck.sh)                      |   | x |   |
| [CuBox](/docs/arm/cubox/)                                                                 | [link](https://gitlab.com/kalilinux/build-scripts/kali-arm/-/blob/main/cubox.sh)                           |   | x |   |
| [Cubox-i4Pro](/docs/arm/cubox-i4pro/)                                                     | [link](https://gitlab.com/kalilinux/build-scripts/kali-arm/-/blob/main/cubox-i4pro.sh)                     |   | x |   |
| [EfikaMX](/docs/arm/efikamx/)                                                             | [link](https://gitlab.com/kalilinux/build-scripts/kali-arm/-/blob/main/archived/efikamx.sh)                |   |   | x |
| [Gateworks Newport](/docs/arm/gateworks-newport/)                                         | [link](https://gitlab.com/kalilinux/build-scripts/kali-arm/-/blob/main/gateworks-newport.sh)               |   | x |   |
| [Gateworks Ventana](/docs/arm/gateworks-ventana/)                                         | [link](https://gitlab.com/kalilinux/build-scripts/kali-arm/-/blob/main/gateworks-ventana.sh)               | x |   |   |
| [Gemini PDA](/docs/arm/gemini-pda/)                                                       |                                                                                                              |   |   | x |
| [i.MX 6ULL EVK](/docs/arm/imx-6ull-evk/)                                                  |                                                                                                              |   |   | x |
| KaliTAP                                                                                   | [link](https://gitlab.com/kalilinux/build-scripts/kali-arm/-/blob/main/archived/kalitap.sh)                |   |   | x |
| LUNA                                                                                      | [link](https://gitlab.com/kalilinux/build-scripts/kali-arm/-/blob/main/archived/luna.sh)                   |   |   | x |
| [Mini-X](/docs/arm/mini-x/)                                                               | [link](https://gitlab.com/kalilinux/build-scripts/kali-arm/-/blob/main/mini-x.sh)                          |   | x |   |
| [NanoPC-T3/T4](/docs/arm/nanopc-t/)                                                       | [link](https://gitlab.com/kalilinux/build-scripts/kali-arm/-/blob/main/nanopc-t.sh)                        | x |   |   |
| [NanoPi NEO Plus2](/docs/arm/nanopi-neo-plus2/)                                           | [link](https://gitlab.com/kalilinux/build-scripts/kali-arm/-/blob/main/nanopi-neo-plus2.sh)                | x |   |   |
| [NanoPi2](/docs/arm/nanopi2/)                                                             | [link](https://gitlab.com/kalilinux/build-scripts/kali-arm/-/blob/main/nanopi2.sh)                         |   | x |   |
| [ODROID-C0/C1/C1+](/docs/arm/odroid-c/)                                                   | [link](https://gitlab.com/kalilinux/build-scripts/kali-arm/-/blob/main/odroid-c.sh)                        |   | x |   |
| [ODROID-C2](/docs/arm/odroid-c2/)                                                         | [link](https://gitlab.com/kalilinux/build-scripts/kali-arm/-/blob/main/odroid-c2.sh)                       | x |   |   |
| [ODROID-U2/U3](/docs/arm/odroid-u/)                                                       | [link](https://gitlab.com/kalilinux/build-scripts/kali-arm/-/blob/main/odroid-u.sh)                        |   | x |   |
| ODROID-W                                                                                  | [link](https://gitlab.com/kalilinux/build-scripts/kali-arm/-/blob/main/archived/odroid-w.sh)               |   |   | x |
| ODROID-W-DEVKIT                                                                           | [link](https://gitlab.com/kalilinux/build-scripts/kali-arm/-/blob/main/archived/odroid-w-devkit.sh)        |   |   | x |
| [ODROID-XU3](/docs/arm/odroid-xu3/)                                                       | [link](https://gitlab.com/kalilinux/build-scripts/kali-arm/-/blob/main/odroid-xu3.sh)                      | x |   |   |
| [Pinebook](/docs/arm/pinebook/)                                                           | [link](https://gitlab.com/kalilinux/build-scripts/kali-arm/-/blob/main/pinebook.sh)                        | x |   |   |
| [Pinebook Pro](/docs/arm/pinebook-pro/)                                                   | [link](https://gitlab.com/kalilinux/build-scripts/kali-arm/-/blob/main/pinebook-pro.sh)                    | x |   |   |
| [Raspberry Pi 1 (Original)](/docs/arm/raspberry-pi/)                                      | [link](https://gitlab.com/kalilinux/build-scripts/kali-arm/-/blob/main/raspberry-pi1.sh)                   | x |   |   |
| [Raspberry Pi 2 (1.1)](/docs/arm/raspberry-pi-2/)                                         | [link](https://gitlab.com/kalilinux/build-scripts/kali-arm/-/blob/main/raspberry-pi.sh)                    | x |   |   |
| [Raspberry Pi 3](/docs/arm/raspberry-pi-3/)                                               | [link](https://gitlab.com/kalilinux/build-scripts/kali-arm/-/blob/main/raspberry-pi.sh)                    | x |   |   |
| [Raspberry Pi 4](/docs/arm/raspberry-pi-4/)                                               | [link](https://gitlab.com/kalilinux/build-scripts/kali-arm/-/blob/main/raspberry-pi.sh)                    | x |   |   |
| [Raspberry Pi 400](/docs/arm/raspberry-pi-400/)                                           | [link](https://gitlab.com/kalilinux/build-scripts/kali-arm/-/blob/main/raspberry-pi.sh)                    | x |   |   |
| [Raspberry Pi 2 1.2/3/4/400 (64-bit)](/docs/arm/raspberry-pi-64-bit/)                     | [link](https://gitlab.com/kalilinux/build-scripts/kali-arm/-/blob/main/raspberry-pi-64-bit.sh)             | x |   |   |
| [Raspberry Pi Zero 2 W](/docs/arm/raspberry-pi-zero-2-w/)                                 | [link](https://gitlab.com/kalilinux/build-scripts/kali-arm/-/blob/main/raspberry-pi-zero-2-w.sh)           | x |   |   |
| [Raspberry Pi Zero 2 W (Pi-Tail)](/docs/arm/raspberry-pi-zero-w-pi-tail/)                 | [link](https://gitlab.com/kalilinux/build-scripts/kali-arm/-/blob/main/raspberry-pi-zero-2-w-pitail.sh)    | x |   |   |
| [Raspberry Pi Zero](/docs/arm/raspberry-pi-zero/)                                         | [link](https://gitlab.com/kalilinux/build-scripts/kali-arm/-/blob/main/raspberry-pi-zero-w.sh)             | x |   |   |
| [Raspberry Pi Zero W](/docs/arm/raspberry-pi-zero-w/)                                     | [link](https://gitlab.com/kalilinux/build-scripts/kali-arm/-/blob/main/raspberry-pi-zero-w.sh)             | x |   |   |
| Raspberry Pi Zero W (P4wnP1 A.L.O.A.)                                                     | [link](https://gitlab.com/kalilinux/build-scripts/kali-arm/-/blob/main/raspberry-pi-zero-w-p4wnp1-aloa.sh) |   | x |   |
| [Raspberry Pi Zero W (Pi-Tail)](/docs/arm/raspberry-pi-zero-w-pi-tail/)                   | [link](https://gitlab.com/kalilinux/build-scripts/kali-arm/-/blob/main/raspberry-pi-zero-w-pitail.sh)      | x |   |   |
| [RIoTboard](/docs/arm/riotboard/)                                                         | [link](https://gitlab.com/kalilinux/build-scripts/kali-arm/-/blob/main/riotboard.sh)                       |   | x |   |
| [Samsung Galaxy Note 10.1](/docs/arm/galaxy-note-10.1/)                                   |                                                                                                              |   |   | x |
| [SS808/MK808](/docs/arm/ss808-mk808/)                                                     |                                                                                                              |   |   | x |
| [Trimslice](/docs/arm/trimslice/)                                                         | [link](https://gitlab.com/kalilinux/build-scripts/kali-arm/-/blob/main/trimslice.sh)                       |   | x |   |
| [USB Armory MKI](/docs/arm/usb-armory-mki/)                                               | [link](https://gitlab.com/kalilinux/build-scripts/kali-arm/-/blob/main/usb-armory-mki.sh)                  |   | x |   |
| [USB Armory MKII](/docs/arm/usb-armory-mkii/)                                             | [link](https://gitlab.com/kalilinux/build-scripts/kali-arm/-/blob/main/usb-armory-mkii.sh)                 |   | x |   |
| [Utilite Pro](/docs/arm/utilite-pro/)                                                     | [link](https://gitlab.com/kalilinux/build-scripts/kali-arm/-/blob/main/utilite-pro.sh)                     |   | x |   |
-->
