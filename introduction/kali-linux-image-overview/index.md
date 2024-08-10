---
title: Kali Linux Image Overview
description:
icon:
weight:
author: ["gamb1t",]
---

Below is an overview of where you can get Kali Linux that is kept up-to-date when a new platform or system is added. Each entry that has a [Kali docs](/docs/) page available will have their page linked.

{{% notice info %}}
WSL does not have enough unique methods of obtaining Kali to warrant its own column. For this reason, it is included in the "Virtual Machines" column.
{{% /notice %}}

| Installations | Virtual Machines | Cloud | Containers | USB | ARM (Single Board Computer) | Mobile (NetHunter) |
|---|---|---|---|---|---|---|
| [Standard Single-boot](/docs/installation/hard-disk-install/)  | [Freshly Make VMware VM](/docs/virtualization/install-vmware-guest-vm/)  | [AWS](/docs/cloud/aws/) | [Docker](/docs/containers/using-kali-docker-images/) |  Live boot - [Create with Linux](/docs/usb/live-usb-install-with-linux/), [macOS](/docs/usb/live-usb-install-with-mac/), or [Windows](/docs/usb/live-usb-install-with-windows/)  | [Banana Pi](/docs/arm/banana-pi/)  | [Generic NetHunter Rootless](/docs/nethunter/nethunter-rootless/) |
| [macOS Single-Boot](/docs/installation/hard-disk-install-on-mac/) / [Dual-boot](/docs/installation/dual-boot-kali-with-mac/) | [Importing Pre-Made VMware VM](/docs/virtualization/import-premade-vmware/) | [Azure](/docs/cloud/azure/)  | [Podman](/docs/containers/using-kali-podman-images/) |  [Persistence](/docs/usb/usb-persistence/)| [Banana Pro](/docs/arm/banana-pro/)| [Generic NetHunter Lite](/docs/nethunter/#10-nethunter-editions) |
| [Dual-booting Linux](/docs/installation/dual-boot-kali-with-linux/) | [VirtualBox](/docs/virtualization/install-virtualbox-guest-vm/)| [Digital Ocean](/docs/cloud/digitalocean/) | [LXC/LXD](/docs/containers/kalilinux-lxc-images/) |  [Encrypted Persistence](/docs/usb/usb-persistence-encryption/) | [BeagleBone Black](/docs/arm/beaglebone-black/)  | [Generic NetHunter](/docs/nethunter/installing-nethunter/) |
| [Dual-booting Windows](/docs/installation/dual-boot-kali-with-windows/) | [Import VirtualBox](/docs/virtualization/import-premade-virtualbox/)| [Linode](/docs/cloud/linode/)  | [Proxmox](/docs/virtualization/install-proxmox-guest-vm/#kali-as-a-proxmox-ct-containerization) |  | [HP Chromebook](/docs/arm/chromebook-exynos/) | Gemini PDA |
| [Installing to USB](/docs/usb/usb-standalone-encrypted/) | [Hyper-V](/docs/virtualization/install-hyper-v-guest-vm/)  | |  |  | [Samsung Chromebook 1 / 2](/docs/arm/chromebook-exynos/) | LG V20 International |
| [BTRFS](/docs/installation/btrfs/) | [Parallels](/docs/virtualization/install-parallels-guest-vm/)| | |  | [Acer Tegra Chromebook](/docs/arm/chromebook-nyan/)| Nexus 10 |
| [Over a network (PXE)](/docs/installation/network-pxe/) | [UTM](/docs/virtualization/install-utm-guest-vm/)| | |  | [ASUS Chromebook Flip](/docs/arm/chromebook-veyron/)| Nexus 5 / 5X |
|  | [QEMU/Libvirt](/docs/virtualization/install-qemu-guest-vm/)| | |  | [CubieBoard2](/docs/arm/cubieboard2/) / [CubieBoard3](/docs/arm/cubietruck/) | Nexus 6 / 6P |
|  | [Vagrant](/docs/virtualization/install-vagrant-guest-vm/)  | | |  | [CuBox](/docs/arm/cubox/) | Nexus 7 |
| | [Proxmox](/docs/virtualization/install-proxmox-guest-vm/)  | | |  | [Cubox-i4Pro](/docs/arm/cubox-i4pro/) | Nexus 9 |
| | [WSL](/docs/wsl/wsl-preparations/) |  |  |  | [Gateworks Newport](/docs/arm/gateworks-newport/) | Nokia 3.1 |
| |  |  |  |  | [Gateworks Ventana](/docs/arm/gateworks-ventana/) | Nokia 6.1 / 6.1 Plus |
| |  |  |  |  | [Mini-X](/docs/arm/mini-x/) | OnePlus 2 |
| |  |  |  |  | [NanoPC-T3 / T4](/docs/arm/nanopc-t/) | OnePlus 3 / 3T |
| |  |  |  |  | [NanoPi NEO Plus2](/docs/arm/nanopi-neo-plus2/) | OnePlus 6 / 6T |
| |  |  |  |  | [NanoPi2](/docs/arm/nanopi2/) | OnePlus 7 / 7 Pro / 7T / 7T Pro |
| |  |  |  |  | [ODROID-C0 / C1 / C1+](/docs/arm/odroid-c/) / [ODROID-C2](/docs/arm/odroid-c2/) | OnePlus 8 / 8T / 8 Pro |
| |  |  |  |  | [ODROID-U2 / U3](/docs/arm/odroid-u/) / [ODROID-XU3](/docs/arm/odroid-xu3/) | OnePlus Nord |
| |  |  |  |  | [Pinebook](/docs/arm/pinebook/) / [Pinebook Pro](/docs/arm/pinebook-pro/) | OnePlus One |
| |  |  |  |  | [Radxa Zero](/docs/arm/radxa-zero-emmc/) | Samsung Galaxy S6 |
| |  |  |  |  | [Raspberry Pi 1 (Original)](/docs/arm/raspberry-pi/) / [2 (1.1)](/docs/arm/raspberry-pi-2/) / [3](/docs/arm/raspberry-pi-3/) / [4](/docs/arm/raspberry-pi-4/) / [400](/docs/arm/raspberry-pi-400/) | Samsung Galaxy Tab S4 Wi-Fi / LTE |
| |  |  |  |  | [Raspberry Pi Zero](/docs/arm/raspberry-pi-zero/) / [Zero W](/docs/arm/raspberry-pi-zero-w/) / [Zero 2 W](/docs/arm/raspberry-pi-zero-2-w/) | Sony Xperia Z1 |
| |  |  |  |  | [RIoTboard](/docs/arm/riotboard/) | TicWatch Pro / Pro 4G/LTE / Pro 2020 |
| |  |  |  |  | [Trimslice](/docs/arm/trimslice/) | Xiaomi Mi 9T MIUI 11 |
| |  |  |  |  | [USB Armory MKI](/docs/arm/usb-armory-mki/) / [MKII](/docs/arm/usb-armory-mkii/) | Xiaomi Mi A3 |
| |  |  |  |  | [Utilite Pro](/docs/arm/utilite-pro/) | Xiaomi Pocophone F1 |
| |  |  |  |  |  | ZTE Axon 7 |
