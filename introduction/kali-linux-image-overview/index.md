---
title: Kali Linux 이미지 개요
description:
icon:
weight:
author: ["gamb1t",]
번역: ["xenix4845"]
---

아래는 새로운 플랫폼이나 시스템이 추가될 때마다 업데이트되는 Kali Linux를 얻을 수 있는 방법에 대한 개요입니다. [Kali 문서](/docs/) 페이지가 있는 각 항목은 해당 페이지로 링크되어 있습니다.

{{% notice info %}}
WSL은 Kali를 얻는 방법이 충분히 독특하지 않아 자체 열을 갖추지 않습니다. 이러한 이유로 "가상 머신" 열에 포함되어 있습니다.
{{% /notice %}}

| 설치 | 가상 머신 | 클라우드 | 컨테이너 | USB | ARM (싱글 보드 컴퓨터) | 모바일 (NetHunter) |
|---|---|---|---|---|---|---|
| [표준 단일 부팅](/docs/installation/hard-disk-install/)  | [VMware VM 직접 만들기](/docs/virtualization/install-vmware-guest-vm/)  | [AWS](/docs/cloud/aws/) | [Docker](/docs/containers/using-kali-docker-images/) |  라이브 부팅 - [Linux로 생성](/docs/usb/live-usb-install-with-linux/), [macOS](/docs/usb/live-usb-install-with-mac/), 또는 [Windows](/docs/usb/live-usb-install-with-windows/)  | [Banana Pi](/docs/arm/banana-pi/)  | [일반 NetHunter Rootless](/docs/nethunter/nethunter-rootless/) |
| [macOS 단일 부팅](/docs/installation/hard-disk-install-on-mac/) / [이중 부팅](/docs/installation/dual-boot-kali-with-mac/) | [사전 제작된 VMware VM 가져오기](/docs/virtualization/import-premade-vmware/) | [Azure](/docs/cloud/azure/)  | [Podman](/docs/containers/using-kali-podman-images/) |  [영구 저장소](/docs/usb/usb-persistence/)| [Banana Pro](/docs/arm/banana-pro/)| [일반 NetHunter Lite](/docs/nethunter/#10-nethunter-editions) |
| [Linux와 이중 부팅](/docs/installation/dual-boot-kali-with-linux/) | [VirtualBox](/docs/virtualization/install-virtualbox-guest-vm/)| [Digital Ocean](/docs/cloud/digitalocean/) | [LXC/LXD](/docs/containers/kalilinux-lxc-images/) |  [암호화된 영구 저장소](/docs/usb/usb-persistence-encryption/) | [BeagleBone Black](/docs/arm/beaglebone-black/)  | [일반 NetHunter](/docs/nethunter/installing-nethunter/) |
| [Windows와 이중 부팅](/docs/installation/dual-boot-kali-with-windows/) | [VirtualBox 가져오기](/docs/virtualization/import-premade-virtualbox/)| [Linode](/docs/cloud/linode/)  | [Proxmox](/docs/virtualization/install-proxmox-guest-vm/#kali-as-a-proxmox-ct-containerization) |  | [HP Chromebook](/docs/arm/chromebook-exynos/) | Gemini PDA |
| [USB에 설치](/docs/usb/usb-standalone-encrypted/) | [Hyper-V](/docs/virtualization/install-hyper-v-guest-vm/)  | |  |  | [Samsung Chromebook 1 / 2](/docs/arm/chromebook-exynos/) | LG V20 International |
| [BTRFS](/docs/installation/btrfs/) | [Parallels](/docs/virtualization/install-parallels-guest-vm/)| | |  | [Acer Tegra Chromebook](/docs/arm/chromebook-nyan/)| Nexus 10 |
| [네트워크 통해 설치 (PXE)](/docs/installation/network-pxe/) | [UTM](/docs/virtualization/install-utm-guest-vm/)| | |  | [ASUS Chromebook Flip](/docs/arm/chromebook-veyron/)| Nexus 5 / 5X |
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
| |  |  |  |  | [Raspberry Pi 1 (원본)](/docs/arm/raspberry-pi/) / [2 (1.1)](/docs/arm/raspberry-pi-2/) / [3](/docs/arm/raspberry-pi-3/) / [4](/docs/arm/raspberry-pi-4/) / [400](/docs/arm/raspberry-pi-400/) | Samsung Galaxy Tab S4 Wi-Fi / LTE |
| |  |  |  |  | [Raspberry Pi Zero](/docs/arm/raspberry-pi-zero/) / [Zero W](/docs/arm/raspberry-pi-zero-w/) / [Zero 2 W](/docs/arm/raspberry-pi-zero-2-w/) | Sony Xperia Z1 |
| |  |  |  |  | [RIoTboard](/docs/arm/riotboard/) | TicWatch Pro / Pro 4G/LTE / Pro 2020 |
| |  |  |  |  | [Trimslice](/docs/arm/trimslice/) | Xiaomi Mi 9T MIUI 11 |
| |  |  |  |  | [USB Armory MKI](/docs/arm/usb-armory-mki/) / [MKII](/docs/arm/usb-armory-mkii/) | Xiaomi Mi A3 |
| |  |  |  |  | [Utilite Pro](/docs/arm/utilite-pro/) | Xiaomi Pocophone F1 |
| |  |  |  |  |  | ZTE Axon 7 |
