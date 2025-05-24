---
title: 넷헌터 HID 키보드 공격
description:
icon:
weight:
author: ["re4son",]
번역: ["xenix4845"]
---

넷헌터 HID 공격은 기기와 OTG USB 케이블을 주어진 명령어를 타이핑할 수 있는 사전 프로그래밍된 키보드로 변환해요. 이전에는 ["Teensy"](https://www.pjrc.com/teensy/) 타입의 기기만 이런 기능을 할 수 있었지만... 더 이상은 아니에요! 이 공격은 일반적으로 매우 잘 작동해요. 하지만 응답하지 않게 되면 메뉴에서 **Reset USB**를 선택하여 USB 스택(stack, 네트워크 계층)을 새로 고치기만 하면 돼요.

![](nethunter-hid.png)
