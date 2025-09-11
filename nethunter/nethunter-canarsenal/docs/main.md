---
title: 넷헌터 CARsenal : 메인
description: CAN 인터페이스를 구성하고 VIN 식별 번호를 디코딩하세요.
icon:
weight:
author: ["v0lk3n",]
번역: ["kmw0410"]
---

<p style="text-align: center"><img src="../assets/main.gif" width="350" alt="CARsenal Main"></p>

### 메인 : CAN 인터페이스

***CAN 인터페이스 시작 - 설정 사전 조건 :***

인터페이스에서 "CAN 인터페이스"와 "CAN 종류"를 설정하고, 필요하면 'MTU'와 'txqueulen'을 활성화하여 사용자 지정 값을 설정할 수 있어요.

```bash
# VCAN 유형 : 인터페이스를 먼저 생성하세요
sudo ip link add dev <caniface> type vcan

# MTU 또는 txqueuelen 값이 지정된 경우
sudo ip link set <caniface> mtu <Value>
sudo ip link set <caniface> txqueuelen <Value>

# 인터페이스 활성화
sudo ip link set <caniface> up
```

***인터페이스 리셋 - 사용된 명령어 :***

다음 <a href="https://raw.githubusercontent.com/V0lk3n/NetHunter-CarArsenal/refs/heads/main/can_reset.sh" target="_blank">스크립트</a>를 실행하여 인터페이스를 리셋하세요


### 메인 : 서비스

<p style="text-align: center"><img src="../assets/main-services.gif" width="350" alt="CARsenal Main Services"></p>

> 주황색 버튼을 길게 눌러 서비스 명령어를 사용자 지정할 수 있어요.

인터페이스 섹션에서는 CAN 인터페이스를 설정할 수 있어요. 설정에서 인터페이스 이름을 지정하고, 필요하면 MTU와 txqueuelen 값을 사용자 지정하세요.

또한 다음과 같은 데몬/서비스를 활성화할 수도 있어요 :

- slcand : 시리얼 CAN 디바이스를 위한 데몬.
- hlcand : ELM327 마이크로컨트롤러용으로 만들어진 slcand 포크.
- socketcand : 브릿지 CAN 인텊페이스를 위한 데몬.
- slcan_attach : 시리얼 CAN 디바이스 연결.
- ldattach : 기기 연결.
- RFCOMM
    - bind : 블루투스를 장치에 연결
    - connect : RFCOMM 장치를 원격 블루투스 장치에 연결


### VIN 정보

VIN 정보는 VIN 식별자를 해석하고 체크섬을 확인하는 데 사용돼요.

```bash
vininfo show <vinNumber>
vininfo check <vinNumber>
```
