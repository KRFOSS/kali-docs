---
title: 넷헌터 CAN-아스날 (CAN-Arsenal)
description:
icon:
weight:
author: ["v0lk3n",]
번역: ["xenix4845"]
---

CAN-아스날은 테스트, 진단 또는 자동차 해킹을 위해 CAN 버스(CAN Bus)와 통신하는 데 사용돼요.

> 경고: 현재 실험 버전입니다

## 전제 조건 - 커널 수정

커널에서 CAN 지원이 활성화되어 있어야 해요. 자세한 정보는 <a href="/docs/nethunter/nethunter-kernel-9-config-8/">"커널 구성 - CAN 아스날"</a> 문서를 참조하세요.

## 메뉴

<img src="nethunter-canarsenal1.jpg" width="500">

### 문서

이 버튼은 다음 문서로 리다이렉트해요.

### 설정

이 버튼은 필요한 CAN 도구와 패키지를 설치해요. CAN 아스날을 처음 실행할 때 자동으로 실행되므로 필요하지 않을 수도 있어요.

### 업데이트

이 버튼은 설치된 CAN 도구와 패키지를 업데이트해요.

## 설정

<img src="nethunter-canarsenal2.jpg" width="500">

설정은 CAN 아스날 툴셋을 구성하는 데 사용돼요.

## 인터페이스

<img src="nethunter-canarsenal3.jpg" width="500">

인터페이스 섹션은 CAN 인터페이스를 구성하는 데 사용돼요.

### ldattach

기기를 연결해요. /dev/rfcomm0 (블루투스)를 기본값으로 설정해요.

***ldattach - 사용 명령어:***

원하는 대로 수정할 수 있어요.

```bash
ldattach --debug --speed 38400 --eightbits --noparity --onestopbit --iflag -ICRNL,INLCR,-IXOFF 29 /dev/rfcomm0
```

### slcand

시리얼 CAN 기기용 데몬(daemon, 백그라운드 서비스)이에요.

***slcand - 사용 명령어:***

원하는 대로 수정할 수 있어요.

```bash
slcand -s6 -t sw -S 200000 /dev/ttyUSB0
```

### slcan_attach

시리얼 CAN 기기를 연결해요.

***slcan_attach - 사용 명령어:***

```bash
slcan_attach -s6 -o /dev/ttyUSB0
```

### RFCOMM 바인드

블루투스 CAN 어댑터 사용을 위해서예요. 블루투스를 기기에 바인드하려면 실행하세요.

***RFCOMM 바인드 - 설정 전제 조건:***

설정에서 "Target" MAC 주소를 설정하세요.

> 참고: RFCOMM이 지원되어야 하며, 이것이 작동하려면 블루투스 아스날에서 서비스를 먼저 활성화해야 해요.
> 이것을 사용하기 전에 bluetoothctl로 블루투스 기기를 페어링하고 신뢰하세요.

***RFCOMM 바인드 - 사용 명령어:***

```bash
rfcomm bind <Target MAC Address>
```

### socketcand

CAN 인터페이스를 브리지하는 데몬이에요.

***socketcand - 설정 전제 조건:***

설정에서 "CAN Interface"를 설정하세요.

***socketcand - 사용 명령어:***

```bash
socketcand -v -l wlan0 -i <CAN Interface>
```

### CAN 인터페이스

***CAN 인터페이스 시작 - 설정 전제 조건:***

설정에서 "CAN Interface", "MTU"를 설정하고 인터페이스에서 "CAN Type"을 설정하세요.

> CAN 또는 SLCAN 인터페이스용 어댑터를 사용하는 경우 "ldattach", "slcand", "slcan_attach", "rfcomm bind" 또는 "socketcand"를 설정해야 할 수도 있어요

***CAN 인터페이스 시작 - 사용 명령어:***

> 참고: 현재는 한 번에 하나의 인터페이스만 시작할 수 있어요. 다음 릴리스에서 다시 작성될 예정이에요. 하나 이상을 시작해야 하는 경우 수동으로 시작해야 해요.

CAN 타입이 CAN으로 설정된 경우

```bash
sudo ip link set <CAN Interface> bitrate <Selected Bitrate>
sudo ip link set <CAN Interface> mtu <MTU>
sudo ip link set <CAN Interface> up 
```

CAN 타입이 VCAN으로 설정된 경우

```bash
sudo ip link add dev <CAN Interface> type vcan
sudo ip link set <CAN Interface> mtu <MTU>
sudo ip link set <CAN Interface> up 
```

CAN 타입이 SLCAN으로 설정된 경우

```bash
sudo ip link set <CAN Interface> mtu <MTU>
sudo ip link set <CAN Interface> up 
```

***CAN 인터페이스 중지 - 설정 전제 조건:***

설정에서 "CAN Interface"를 설정하세요

***CAN 인터페이스 중지 - 사용 명령어:***

CAN 타입이 CAN 또는 SLCAN으로 설정된 경우

```bash
sudo ip link set <CAN Interface> down
```

CAN 타입이 VCAN으로 설정된 경우

```bash
sudo ip link set <CAN Interface> down && sudo ip link delete <CAN Interface>
```

## 도구

<img src="nethunter-canarsenal4.jpg" width="500">


### Can-Utils: CanGen

CAN 버스 트래픽을 생성하는 데 사용돼요.


***CanGen - 설정 전제 조건:*** 

원하는 CAN 인터페이스가 시작되고 설정에서 설정되어야 해요.


***CanGen - 사용 명령어:***

```bash
cangen <CAN Interface> -v
```


### Can-Utils: CanSniffer

CAN 버스 트래픽을 스니핑(sniffing, 패킷 감청)하는 데 사용돼요.


***CanSniffer - 설정 전제 조건:*** 

원하는 CAN 인터페이스가 시작되고 설정에서 설정되어야 해요.


***CanSniffer - 사용 명령어:***

```bash
cansniffer <CAN Interface>
```


### Can-Utils: CanDump

CAN 버스 트래픽을 출력 파일로 덤프하는 데 사용돼요.


***CanDump - 설정 전제 조건:*** 

원하는 CAN 인터페이스가 시작되고 설정에서 "Output" 경로와 함께 설정되어야 해요. 


***CanDump - 사용 명령어:***

```bash
candump <CAN Inteface> -f <Output Log>
```


### Can-Utils: CanSend

특정 시퀀스를 CAN 버스로 재생하는 데 사용돼요.


***CanSend - 설정 전제 조건:*** 

원하는 CAN 인터페이스가 시작되고 설정에서 "Sequence"와 함께 설정되어야 해요. 

***CanSend - 사용 명령어:***

```bash
cansend <CAN Interface> <Sequence>
```

### Can-Utils: CanPlayer

로그 파일에서 덤프된 시퀀스를 CAN 버스로 재생하는 데 사용돼요.


***CanPlayer - 설정 전제 조건:*** 

원하는 CAN 인터페이스가 시작되고 설정에서 "Input" 경로와 함께 설정되어야 해요. 

> CAN 인터페이스는 입력 로그에서 가져오므로 인터페이스가 동일한지 확인하세요. (vcan0로 덤프했다면 vcan0로 재생해야 해요)

***CanPlayer - 사용 명령어 :***

```bash
canplayer -I <Input Log>
```


### 커스텀 스크립트: SequenceFinder

<a href="https://raw.githubusercontent.com/V0lk3n/NetHunter-CANArsenal/refs/heads/main/sequence_finder.sh">여기서 소스 코드를 볼 수 있어요.</a>

로그 파일에서 원하는 동작의 정확한 시퀀스를 찾는 데 사용돼요.

>이 커스텀 스크립트는 head와 tail을 사용하여 로그 파일을 자동으로 분할해요. 원하는 동작의 정확한 시퀀스를 찾을 때까지 CanPlayer를 사용하여 사용자 입력과 함께 루프에서 재생해요. 마지막으로 CanSend를 사용하여 재생해요.


***SequenceFinder - 설정 전제 조건:*** 

원하는 CAN 인터페이스가 시작되고 설정에서 "Input" 경로와 함께 설정되어야 해요.

> CAN 인터페이스는 입력 로그에서 가져오므로 인터페이스가 동일한지 확인하세요. (vcan0로 덤프했다면 vcan0로 재생해야 해요)


***SequenceFinder - 사용 명령어:***

```bash
/opt/car_hacking/sequence_finder.sh <Input Log>
```


### Freediag

자동차를 진단하는 데 사용돼요.


***Freediag - 사용 명령어:***

```bash
sudo -u kali freediag
```


### Freediag: DiagTest

DiagTest는 코드 경로를 실행하는 데 사용되는 Freediag의 독립 실행형 프로그램이에요.


***DiagTest - 사용 명령어:***

```bash
sudo -u kali diag_test
```

## USB-CAN

<img src="nethunter-canarsenal5.jpg" width="500">


주로 CAN USB 분석기를 사용하여 시퀀스를 덤프하고 전송하는 데 사용돼요.

***USB-CAN 덤프 - 설정 전제 조건:***

설정에서 "USB Device"를 설정하세요.

USB-CAN에서 "CAN Speed"와 "Baudrate"를 설정하세요. 선택적으로 디버그 매개변수를 추가하세요.

> CAN USB 어댑터가 기기에 연결되어 있어야 하고 새로고침 버튼을 눌러서 연결된 어댑터로 USB 기기를 설정하세요.


***USB-CAN 덤프 - 사용 명령어 :***

```bash
canusb -d <USB Device> -s <USB CAN Speed> -b <USB Baudrate> <Optional Debug Parameters>
```


***USB-CAN 전송 - 설정 전제 조건:***

설정에서 "USB Device"를 설정하세요.

USB-CAN에서 "CAN Speed"와 "Baudrate"를 설정하세요. CAN 버스로 전송하려는 "ID"와 "Data"를 설정하세요. 선택적으로 디버그 및 슬립 매개변수를 추가하세요.

> CAN USB 어댑터가 기기에 연결되어 있어야 하고 새로고침 버튼을 눌러서 연결된 어댑터로 USB 기기를 설정하세요.


***USB-CAN 전송 - 사용 명령어 :***

```bash
canusb -d <USB Device> -s <USB CAN Speed> -b <USB Baudrate> <ID> <Data> <Optional Debug/Sleep Parameters>
```

## Cannelloni

<img src="nethunter-canarsenal6.jpg" width="500">


이더넷을 통해 CAN 버스에서 두 대의 머신과 통신하는 데 사용돼요.

***Cannelloni - 설정 전제 조건:*** 

원하는 CAN 인터페이스가 설정에서 설정되어야 해요.

Cannelloni에서 "RHOST", "RPORT" 및 "LPORT"를 설정해야 해요.

> 두 기기 모두 이더넷 케이블로 연결되어야 해요.


***Cannelloni - 사용 명령어 :***

```bash
sudo cannelloni -I <CAN Interface> -R <RHOST> -r <RPORT> -l <LPORT>
```


## 로깅

<img src="nethunter-canarsenal7.jpg" width="500">


### Asc2Log

can-utils 모음에서 Asc2Log는 ASC 파일 형식을 클래식 LOG로 변환하는 데 사용돼요.


***Asc2Log - 설정 전제 조건:*** 

설정에서 "Input"과 "Output" 경로를 설정하세요. 


***Asc2Log - 사용 명령어 :***

```bash
asc2log -I <Input Log> -O <Output File>
```


### Log2Asc

can-utils 모음에서 Log2Asc는 덤프된 LOG 파일을 ASC 형식으로 변환하는 데 사용돼요.


***Log2Asc - 설정 전제 조건:*** 

원하는 CAN 인터페이스가 시작되고 설정에서 "Input", "Output" 경로와 함께 설정되어야 해요. 


***Log2Asc - 사용 명령어 :***

```bash
log2asc -I <Input Log> -O <Output File> <CAN Interface>
```


## 커스텀 명령어

<img src="nethunter-canarsenal8.jpg" width="500">

제공된 명령어와 일치하지 않는 특정 명령어를 실행해야 하는 경우에 사용돼요.

## 리소스

***도구 문서***
* <a href="https://github.com/linux-can/can-utils">can-utils</a>
* <a href="https://freediag.sourceforge.io/">freediag</a>
* <a href="https://github.com/kobolt/usb-can">usb-can</a>
* <a href="https://github.com/mguentner/cannelloni">cannelloni</a>


***가이드***
* <a href="https://www.offsec.com/blog/introduction-to-car-hacking-the-can-bus/?utm_source=kali&utm_medium=web&utm_campaign=docs">자동차 해킹 입문: CAN 버스</a>


## 크레딧

* <a href="https://github.com/fenugrec">Fenugrec</a> - freediag
* <a href="https://gitlab.com/kimoc0der">Kimocoder</a> - 도움과 지원
* <a href="https://github.com/kobolt">Kobolt</a> - usb-can
* <a href="https://github.com/linux-can">Linux-Can</a> - can-utils와 socketcand
* <a href="https://github.com/mguentner">Mguentner</a> - cannelloni
* <a href="https://gitlab.com/V0lk3n">V0lk3n</a> - 넷헌터 CAN 아스날 탭
* <a href="https://gitlab.com/yesimxev">Yesimxev</a> - 도움과 지원
