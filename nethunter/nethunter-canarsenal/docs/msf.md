---
title: 넷헌터 CARsenal : MSF Automotive
description: Metasploit 프레임워크의 자동차 관련 모듈을 사용.
icon:
weight:
author: ["v0lk3n",]
번역: ["kmw0410"]
---

<p style="text-align: center"><img src="../assets/msf.gif" width="350" alt="CARsenal MSF Automotive"></p>

### 어떻게 작동하나요?

우선 'start msfconsole'을 실행하고, 같은 인터페이스에서 모듈을 실행할 수 있도록, msf 세션을 분리했다가 다시 연결하는 데 screen을 사용할 거에요.

msf가 시작되면, 모듈을 선택하고 "Info"로 모듈 정보를 확인한 뒤, "Set"으로 모듈을 설정하고 마지막으로 "Run"으로 실행하면 돼요.

> 참고 : 터미널 창을 자동으로 닫을 수는 없으므로, 이전 터미널 창이 여전히 열려 있지만 종료된 상태임을 기억하세요.

### 하드웨어 도구 : ELM327 릴레이

- <a href="https://raw.githubusercontent.com/rapid7/metasploit-framework/refs/heads/master/tools/hardware/elm327_relay.rb" target="_blank">elm327_relay</a> : 이 모듈은 ELM327 또는 STN1100이 기계의 시리얼 포트에 연결되어 있어야 해요. 통신을 위해 기본적은 RestFUL 웹 서버를 설정하세요.

### 보조 모듈

- <a href="https://www.rapid7.com/db/modules/auxiliary/server/local_hwbridge/" target="_blank">local_hwbridge</a> : Metasploit과 실제로 연결된 하드웨어 간의 통신을 중계하기 위해 웹 서버를 설정해요.
- <a href="https://www.rapid7.com/db/modules/auxiliary/client/hwbridge/connect/" target="_blank">connect</a> : 실제 HWBridge를 연결하면 인터렉티브 hwbridge 세션이 시작돼요. (로컬 hwbridge가 실행 중이여야 해요)

### 포스트 모듈
- <a href="https://www.rapid7.com/db/modules/post/hardware/automotive/can_flood/" target="_blank">can_flood</a> : 지정된 프레임으로 CAN 인터페이스를 대량 전송.
- <a href="https://www.rapid7.com/db/modules/post/hardware/automotive/canprobe/" target="_blank">canprobe</a> : 두 can id 사이를 스캔하고 각 바이트 위치에 데이터 쓰기.
- <a href="https://www.rapid7.com/db/modules/post/hardware/automotive/diagnostic_state/" target="_blank">diagnostic_state</a> : 테스터 프리젠트 패킷을 보내 차량을 진단 모드로 유지.
- <a href="https://www.rapid7.com/db/modules/post/hardware/automotive/ecu_hard_reset/" target="_blank">ecu_hard_reset</a> : 스터 프리젠트 패킷을 보내 차량을 진단 모드로 유지.
- <a href="https://www.rapid7.com/db/modules/post/hardware/automotive/getvinfo/" target="_blank">getvinfo</a> : 이 모듈은 DTC, 일부 일반적인 엔진 정보, 그리고 차량 정보를 조회.
- <a href="https://www.rapid7.com/db/modules/post/hardware/automotive/identifymodules/" target="_blank">identifymodules</a> : UDS DSC 쿼리에 응답할 수 있는 모듈이 있는지 CAN 버스를 스캔.
- <a href="https://www.rapid7.com/db/modules/post/hardware/automotive/malibu_overheat/" target="_blank">malibu_overheat</a> : 2006년형 말리부를 대상으로 한 간단한 샘플 임시 플러딩.
- <a href="https://www.rapid7.com/db/modules/post/hardware/automotive/mazda_ic_mover/" target="_blank">mazda_ic_mover</a> : 마즈다 2 계기판의 가속도계와 속도계 바늘을 움직임.
- <a href="https://www.rapid7.com/db/modules/post/hardware/automotive/pdt/" target="_blank">pdt</a> : 화약 장치 배치 도구(PDT) 역할을 수행.
