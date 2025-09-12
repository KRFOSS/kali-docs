---
title: 넷헌터 CARsenal
description:
icon:
weight:
author: ["v0lk3n",]
번역: ["kmw0410"]
---

CARsenal은 자동차 보안 도구 세트를 제공하는 데 사용돼요.

## 필수 조건 - 커널 수정

커널에 CAN 지원이 활성화되어 있어야 해요. 더 많은 정보는 <a href="https://kali.krfoss.org/nethunter-kernel-9-config-8" target="_blank">"커널 구성 - CARsenal"</a> 문서에서 확인할 수 있어요.


## CARsenal 문서

- <a href="docs/main" target="_blank">메인</a> : CAN 인터페이스 구성과 VIN 식별자를 해석.
- <a href="docs/tools" target="_blank">도구</a> : can-utils 스위트, cannelloni 또는 Freediag를 제공.
- <a href="docs/canusb" target="_blank">CAN-USB</a> : CAN 분석 USB 장치를 사용하여 신호를 덤프 및 전송.
- <a href="docs/caribou" target="_blank">Caribou</a> : Caring Caribou 자동차 보안 탐색 도구.
- <a href="docs/simulator" target="_blank">시뮬레이터</a> : ICSim와 UDSim 시뮬레이터.
- <a href="docs/msf" target="_blank">MSF</a> : Metasploit 자동차 모듈.

## 자료

***도구 문서***
* <a href="https://github.com/linux-can/can-utils" target="_blank">can-utils</a>
* <a href="https://freediag.sourceforge.io/" target="_blank">freediag</a>
* <a href="https://github.com/kobolt/usb-can" target="_blank">usb-can</a>
* <a href="https://github.com/mguentner/cannelloni" target="_blank">cannelloni</a>
* <a href="https://github.com/idlesign/vininfo" target="_blank">VIN Info</a>
* <a href="https://github.com/CaringCaribou/caringcaribou" target="_blank">CaringCaribou</a>
* <a href="https://github.com/zombieCraig/ICSim" target="_blank">ICSim</a>
* <a href="https://github.com/zombieCraig/UDSim" target="_blank">UDSim</a>


***가이드***
* <a href="https://www.offsec.com/blog/introduction-to-car-hacking-the-can-bus" target="_blank">자동차 해킹에 대한 소개: CAN 버스</a>


## 크레딧

* <a href="https://github.com/alexmohr" target="_blank">Alexmohr</a> - can-usb 포크
* <a href="https://github.com/CaringCariboucaringcaribou" target="_blank">CaringCaribou</a> - 도구
* <a href="https://github.com/fenugrec" target="_blank">Fenugrec</a> - freediag
* <a href="https://github.com/idlesign/vininfo" target="_blank">idlesign</a> - VIN 정보
* <a href="https://gitlab.com/kimoc0der" target="_blank">Kimocoder</a> - 도움 및 지원
* <a href="https://github.com/kobolt" target="_blank">Kobolt</a> - usb-can
* <a href="https://github.com/linux-can" target="_blank">Linux-Can</a> - can-utils와 socketcand
* <a href="https://github.com/mguentner" target="_blank">Mguentner</a> - cannelloni
* <a href="https://gitlab.com/V0lk3n" target="_blank">V0lk3n</a> - 넷헌터 CARsenal 탭
* <a href="https://gitlab.com/yesimxev" target="_blank">Yesimxev</a> - 도움 및 지원
* <a href="https://github.com/zombieCraig" target="_blank">zombieCraig</a> - ICSim와 UDSim
* <a href="https://www.rapid7.com/"> rapid7</a> - Metasploit 프레임워크
* <a href="https://www.crunchbase.com/person/craig-smith-12"> Craig Smith</a> - MSF 자동차 모듈
* <a href="https://x.com/pietro_biondi94"> Pietro Biondi</a> - MSF 자동차 모듈
* <a href="https://ph.linkedin.com/in/shipjayturla"> Jay Turla</a> - MSF 자동차 모듈
* <a href="https://x.com/johbraun"> Johannes Braun</a> - MSF 자동차 모듈
* <a href="https://de.linkedin.com/in/j%C3%BCrgen-d%C3%BCrrwang-75160217a"> Juergen Duerrwang</a> - MSF 자동차 모듈

<br>
<br>

- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -

<br>
<br>






## 메인

<p style="text-align: center"><img src="assets/main.gif" width="350" alt="CARsenal Main"></p>

### 메인 : CAN 인터페이스

***CAN 인터페이스 시작 - 설정 필수 조건 :***

인터페이스에서 "CAN 인터페이스", "CAN 타입"을 설정해주세요. 그리고 선택적으로 'MTU'와 'txqueulen'을 활성화하여 사용자 정의 값을 설정할 수 있어요.

```bash
# VCAN 타입의 경우 : 먼저 인터페이스를 생성
sudo ip link add dev <caniface> type vcan

# MTU 또는 txqueuelen 값이 지정된 경우
sudo ip link set <caniface> mtu <Value>
sudo ip link set <caniface> txqueuelen <Value>

# 인터페이스 활성화
sudo ip link set <caniface> up
```

***인터페이스 재설정 - 사용된 명령 :***

인터페이스를 재설정하기 위해 <a href="https://raw.githubusercontent.com/V0lk3n/NetHunter-CarArsenal/refs/heads/main/can_reset.sh" target="_blank">다음 스크립트</a>를 실행해요.


### 메인 : 서비스

<p style="text-align: center"><img src="assets/main-services.gif" width="350" alt="CARsenal Main Services"></p>

> 주황색 버튼을 길게 눌러 서비스 명령을 사용자 정의할 수 있어요.

인터페이스 섹션은 CAN 인터페이스를 구성하는 데 사용돼요. 설정에서 인터페이스 이름을 지정할 수 있고, 선택적으로 사용자 정의 MTU 및 txqueuelen 값을 설정할 수 있어요.

또한 다음과 같은 데몬/서비스를 활성화할 수 있어요:

- slcand : 시리얼 CAN 장치용 데몬.
- hlcand : ELM327 마이크로컨트롤러용으로 만든 slcand 포크.
- socketcand : CAN 인터페이스를 브릿지하는 데몬.
- slcan_attach : 시리얼 CAN 장치를 연결.
- ldattach : 장치를 연결.
- RFCOMM
    - bind : 블루투스를 장치에 바인딩.
    - connect : RFCOMM 장치를 원격 블루투스 장치에 연결


### VIN 정보

VIN 정보는 VIN 식별자를 디코딩하고 체크섬을 확인하는 데 사용돼요.

```bash
vininfo show <vinNumber>
vininfo check <vinNumber>
```

<br>
<br>

- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -

<br>
<br>






## 도구

<p style="text-align: center"><img src="assets/tools.gif" width="350" alt="CARsenal Tools"></p>

> 설정을 구성하면 명령이 업데이트돼요. 주황색 버튼을 길게 눌러 명령도 편집할 수 있어요.

### 도구 : 제공되는 도구

- <a href="https://github.com/linux-can/can-utils" target="_blank">can-utils</a> : SocketCAN 사용자 공간 유틸리티 및 도구.
    - <a href="https://manpages.debian.org/trixie/can-utils/cangen.1.en.html" target="_blank">cangen</a> : 테스트 목적의 CAN 프레임 생성기.
    - <a href="https://manpages.debian.org/trixie/can-utils/cansniffer.1.en.html" target="_blank">cansniffer</a> : 휘발성 CAN 콘텐츠 시각화 도구.
    - <a href="https://manpages.debian.org/trixie/can-utils/candump.1.en.html" target="_blank">candump</a> : CAN 버스 트래픽을 덤프.
    - <a href="https://manpages.debian.org/trixie/can-utils/cansend.1.en.html" target="_blank">cansend</a> : CAN_RAW 소켓을 통해 CAN 프레임을 전송.
    - <a href="https://manpages.debian.org/trixie/can-utils/canplayer.1.en.html" target="_blank">canplayer</a> : 압축된 CAN 프레임 로그 파일을 CAN 장치로 재생.
    - <a href="https://manpages.debian.org/trixie/can-utils/asc2log.1.en.html" target="_blank">asc2log</a> : ASC 로그 파일을 압축된 CAN 프레임 로그 파일로 변환.
    - <a href="https://manpages.debian.org/trixie/can-utils/log2asc.1.en.html" target="_blank">log2asc</a> : 압축된 CAN 프레임 로그 파일을 ASC 로그 파일로 변환.

- <a href="https://github.com/fenugrec/freediag" target="_blank">freediag</a> : 자동차 진단 시스템에 액세스.
    - <a href="https://github.com/fenugrec/freediag" target="_blank">diagtest</a> : Freediag의 독립 실행형 프로그램으로, 코드 경로를 실행하는 데 사용.

- <a href="https://github.com/mguentner/cannelloni" target="_blank">cannelloni</a> : UDP, TCP 또는 SCTP를 사용하여 두 장치 간에 CAN 프레임을 전송.

- <a href="https://raw.githubusercontent.com/V0lk3n/NetHunter-CarArsenal/refs/heads/main/sequence_finder.sh">sequence_finder</a> : 로그 파일을 분할하고, 원하는 동작의 정확한 시퀀스를 찾을 때까지 CanPlayer로 재생한 다음 CanSend를 사용하여 재생하는 사용자 정의 스크립트.

<br>
<br>

- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -

<br>
<br>






## CAN-USB

<p style="text-align: center"><img src="assets/canusb.gif" width="350" alt="CARsenal CAN-USB"></p>

> 설정을 구성하면 명령이 업데이트돼요.

CAN-USB는 아래 표시된 저비용 하드웨어를 사용해요.

<img src="assets/USB-CAN.jpg" alt="CARsenal CAN-USB Hardware">

<br>
<br>

- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -

<br>
<br>






## Caribou

<p style="text-align: center"><img src="assets/caribou.gif" width="350" alt="CARsenal Caring Caribou"></p>

> 모듈과 하위 모듈을 선택하면 설정 필드에 매개변수가 표시돼요.

### 모듈 및 하위 모듈

- <a href="https://github.com/CaringCaribou/caringcaribou/blob/master/documentation/dump.md" target="_blank">Dump</a>
- <a href="https://github.com/CaringCaribou/caringcaribou/blob/master/documentation/fuzzer.md" target="_blank">Fuzzer</a>
	- brute
	- identify
	- mutate
	- random
	- replay
- <a href="https://github.com/CaringCaribou/caringcaribou/blob/master/documentation/listener.md" target="_blank">Listener</a>
- module_template
- <a href="https://github.com/CaringCaribou/caringcaribou/blob/master/documentation/send.md" target="_blank">Send</a>
	- file
	- message
- <a href="https://github.com/CaringCaribou/caringcaribou/blob/master/documentation/uds.md" target="_blank">UDS</a>
    - discovery
    - services
    - subservices
    - ecu_reset
    - testerpresent
    - security_seed
    - dump_dids
    - read_mem
    - auto
- <a href="https://github.com/CaringCaribou/caringcaribou/blob/master/documentation/uds_fuzz.md" target="_blank">UDS_Fuzz</a>
    - delay_fuzzer
    - seed_randomness_fuzzer
- <a href="https://github.com/CaringCaribou/caringcaribou/blob/master/documentation/xcp.md" target="_blank">XCP</a>
	- discovery
	- info
	- commands
	- dump

<br>
<br>

- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - 

<br>
<br>






## 시뮬레이터

<p style="text-align: center"><img src="assets/simulator.gif" width="350" alt="CARsenal Simulator"></p>

> 시뮬레이터가 실행되면, 더 나은 제어를 위해 ICSim/UDSim을 플로팅할 수 있어요. 또한 Controls WebView를 활성화/비활성화할 수도 있어요.

### 어떻게 작동하나요?

시뮬레이터를 시작할 때 kex나 다른 프로그램이 실행 중인 경우 문제를 피하기 위해 디스플레이 3~5를 사용해요.

- 디스플레이 3 : ICSim
- 디스플레이 4 : Controls
- 디스플레이 5 : UDSim

그 다음 각 디스플레이에서 가상 프레임버퍼(Xvfb)를 시작하고, 창 관리자로 fluxbox를 실행하며, VNC 서버로 x11vnc를 시작해요.

완료되면 각 VNC 디스플레이에서 시뮬레이터를 실행하고 브라우저에서 액세스할 수 있도록 noVNC를 시작해요.

마지막으로, 넷헌터 앱이 noVNC의 웹뷰를 로드하여 디스플레이를 제공해요.


### ICSim

ICSim 문서는 <a href="https://github.com/zombieCraig/ICSim" target="_blank">여기에서 찾을 수 있어요</a>.

ICSim은 <a href="https://raw.githubusercontent.com/V0lk3n/NetHunter-CARsenal/refs/heads/main/icsim_service.sh"> 다음 스크립트</a>를 통해 시작/중지돼요.

### UDSim

UDSim 문서는 <a href="https://github.com/zombieCraig/UDSim" target="_blank">여기에서 찾을 수 있어요</a>.

UDSim은 <a href="https://raw.githubusercontent.com/V0lk3n/NetHunter-CARsenal/refs/heads/main/udsim_service.sh"> 다음 스크립트</a>를 통해 시작/중지돼요.

<br>
<br>

- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -

<br>
<br>






## MSF

<p style="text-align: center"><img src="assets/msf.gif" width="350" alt="CARsenal MSF Automotive"></p>

### 어떻게 사용하나요?

먼저 'start msfconsole'을 눌러야 해요. 같은 인스턴스에서 모듈을 실행하기 위해 screen을 사용하여 msf 세션을 분리하고 다시 연결해요.

msf가 시작되면 모듈을 선택하고 "Info"를 사용하여 모듈 정보를 읽고, "Set"을 사용하여 모듈을 구성하고, 마지막으로 "Run"을 사용하여 실행해요.

> 참고 : 현재 터미널 창을 자동으로 닫을 수 없어서, 이전 터미널 창이 여전히 열려 있지만 종료된 상태로 유지된다는 점을 염두에 두세요.

### 하드웨어 도구 : ELM327 릴레이 

- <a href="https://raw.githubusercontent.com/rapid7/metasploit-framework/refs/heads/master/tools/hardware/elm327_relay.rb" target="_blank">elm327_relay</a> : 이 모듈은 장치의 시리얼에 연결된 ELM327 또는 STN1100이 필요해요. 통신을 위한 기본 RESTful 웹 서버를 설정해요.

### Auxiliary 모듈

- <a href="https://www.rapid7.com/db/modules/auxiliary/server/local_hwbridge/" target="_blank">local_hwbridge</a> : Metasploit과 물리적으로 연결된 하드웨어 간의 통신을 브릿지하기 위한 웹 서버를 설정해요.
- <a href="https://www.rapid7.com/db/modules/auxiliary/client/hwbridge/connect/" target="_blank">connect</a> : 대화형 hwbridge 세션을 시작할 물리적 HWBridge에 연결해요(local_hwbridge가 실행 중이어야 함).

### Post 모듈
- <a href="https://www.rapid7.com/db/modules/post/hardware/automotive/can_flood/" target="_blank">can_flood</a> : 제공된 프레임으로 CAN 인터페이스를 플러딩해요.
- <a href="https://www.rapid7.com/db/modules/post/hardware/automotive/canprobe/" target="_blank">canprobe</a> : 두 CAN ID 사이를 스캔하고 각 바이트 위치에 데이터를 작성해요.
- <a href="https://www.rapid7.com/db/modules/post/hardware/automotive/diagnostic_state/" target="_blank">diagnostic_state</a> : 테스터 현재 패킷을 전송하여 라운드에서 차량을 진단 상태로 유지해요.
- <a href="https://www.rapid7.com/db/modules/post/hardware/automotive/ecu_hard_reset/" target="_blank">ecu_hard_reset</a> : ECU 재설정 서비스 식별자(0x11)에서 하드 재설정을 수행해요.
- <a href="https://www.rapid7.com/db/modules/post/hardware/automotive/getvinfo/" target="_blank">getvinfo</a> : 이 모듈은 DTC, 일반적인 엔진 정보 및 차량 정보를 쿼리해요.
- <a href="https://www.rapid7.com/db/modules/post/hardware/automotive/identifymodules/" target="_blank">identifymodules</a> : UDS DSC 쿼리에 응답할 수 있는 모든 모듈에 대해 CAN 버스를 스캔해요.
- <a href="https://www.rapid7.com/db/modules/post/hardware/automotive/malibu_overheat/" target="_blank">malibu_overheat</a> : 2006 Malibu용 간단한 샘플 온도 플러딩.
- <a href="https://www.rapid7.com/db/modules/post/hardware/automotive/mazda_ic_mover/" target="_blank">mazda_ic_mover</a> : Mazda 2 계기판의 가속도계와 속도계의 바늘을 움직여요.
- <a href="https://www.rapid7.com/db/modules/post/hardware/automotive/pdt/" target="_blank">pdt</a> : 화공 장치 배치 도구(PDT) 역할을 수행해요.
