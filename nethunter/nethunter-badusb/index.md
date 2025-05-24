---
title: 넷헌터 BadUSB 공격
description:
icon:
weight:
author: ["re4son",]
번역: ["xenix4845"]
---

이것은 Black Hat USA 2014에서 시연된 [BadUSB](https://srlabs.de/badusb/) 공격을 우리가 구현한 버전이에요. 이 USB 모드를 활성화하면 OTG USB 케이블과 함께 기기를 대상 컴퓨터에 연결했을 때 네트워크 인터페이스로 변환돼요. USB 케이블을 PC에 연결하면 해당 PC(Windows 또는 Linux)의 모든 트래픽이 넷헌터 기기를 통해 강제로 전송되고, 여기서 트래픽을 MitM(중간자 공격)할 수 있어요.

![](nethunter-badusb.png)
