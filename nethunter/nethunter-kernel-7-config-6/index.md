---
title: 4.x 커널 구성하기 - USB
description:
icon:
weight: 34
author: ["re4son",]
번역: ["xenix4845"]
---

## 커널 구성 계속

### USB 모뎀

CDC ACM 지원은 Proxmark와 유사한 기기에 필요해요

***Device Drivers -> USB support***로 이동하여 다음 옵션을 선택하세요:

- ***"USB Modem (CDC ACM) support"*** 선택
  (CONFIG_USB_ACM=y)

![](nh-kernel-270-usb-1.png)

### USB 가젯 지원

4.x 이상 버전의 커널은 (대부분의 경우) 패치가 필요하지 않으며, 아래와 같이 커널 구성에서 간단히 활성화할 수 있다는 점을 유의하세요.

다양한 USB 기반 공격에는 Generic serial, CDC ACM, CDC ECM, HID가 필요해요.

***Device Drivers -> USB support -> USB Gadget Support***로 이동하여 다음을 선택하세요:

- ***"Generic serial bulk in/out"*** 선택
  (CONFIG_USB_CONFIGFS_SERIAL=y)
- ***"Abstract Control Model (CDC ACM)"*** 선택
  (CONFIG_USB_CONFIGFS_ACM=y)
- ***"Object Exchange Model (CDC OBEX)"*** 선택
  (CONFIG_USB_CONFIGFS_OBEX=y)
- ***"Network Control Model (CDC NCM)"*** 선택
  (CONFIG_USB_CONFIGFS_NCM=y)
- ***"Ethernet Control Model (CDC ECM)"*** 선택
  (CONFIG_USB_CONFIGFS_ECM=y)
- ***"Ethernet Control Model (CDC ECM) subset"*** 선택
  (CONFIG_USB_CONFIGFS_ECM_SUBSET=y)
- ***"RNDIS"*** 선택
  (CONFIG_USB_CONFIGFS_RNDIS=y)
- ***"Ethernet Emulation Model (EEM)"*** 선택
  (CONFIG_USB_CONFIGFS_EEM=y)
- ***"Mass Storage"*** 선택
  (CONFIG_USB_CONFIGFS_MASS_STORAGE=y)
- ***"HID function"*** 선택
  (CONFIG_USB_CONFIGFS_F_HID=y)

![](nh-kernel-280-usb-2.png)

## 종료, 저장 및 빌드
