---
title: 넷헌터 WifiPumpkin
description:
icon:
weight:
author: ["yesimxev",]
번역: ["xenix4845"]
---

[WifiPumpkin3](https://github.com/P0cL4bs/wifipumpkin3)는 불량 Wi-Fi AP와 MitM 공격을 수행하는 [P0cL4bs](https://github.com/P0cL4bs)의 악성 액세스 포인트 구현이에요.

캡티브 포털(captive portal, 인증 페이지)이 있거나 없는 가짜 액세스 포인트를 실행할 수 있어요. ssid, 채널 번호 등의 설정을 커스터마이징하세요. 일부 기기는 부팅 시 안드로이드에서 생성할 수 있는 가상 wlan1을 지원해요. 그렇지 않으면 스크립트가 대신 생성을 시도할 거예요. 오래된 기기는 wlan1 가상 AP가 없으므로 wlan0도 사용할 수 있어요. 그렇지 않으면 외부 어댑터를 사용하세요. 업스트림(upstream, 상위 연결)은 wlan0(예: wlan1을 AP로 사용하는 경우) 또는 모바일 네트워크(rmnet_data1/2/3)일 수 있어요 - IPv4 주소가 있는 것을 사용하세요.

모든 설정이 만족스럽게 구성되면 **Start** 버튼을 탭하여 공격을 시작하세요.

![](nethunter-wifipumpkin.png)
