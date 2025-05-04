---
title: Raspberry Pi-Tail Zero W
description:
icon:
weight:
author: ["re4son",]
번역: ["xenix4845"]
---

# Pi-Tail

![pi-tail-logo](images/pi-tail-logo.png)

- 테더링에 최적화된 Kali-Pi0
- 블루투스 및 Wi-Fi 테더링을 위한 간단한 원케이블 솔루션
- 처음부터 이미지 설치, 구성, 연결, 부팅까지 2분 내로 가능
- 스마트폰에 ConnectBot과 VNC 뷰어만 설치하면 됨
- USB 이더넷과 대용량 저장소 간 자동 전환

![Pi-Tail](images/pi-tail-demo.jpg)

- - -

## 빠른 설치 및 사용법:

1. [여기서 이미지 다운로드](https://http.krfoss.org/)하여 마이크로-SD 카드에 쓰기
2. 카드를 라즈베리 파이 Zero W에 삽입
3. 연결: OTG 어댑터를 스마트폰에, 표준 케이블을 Pi-Tail(전원)에 연결하여 Pi-Tail에 전원 공급
4. 스마트폰에서:
   1. SSID = "`sepultura`", 비밀번호 = "`R4t4m4h4tt4`"로 핫스팟 활성화
   2. Hacker's Keyboard, ConnectBot 및 VNC 뷰어(선택사항) 설치
   3. `192.168.43.254`에 연결, 사용자 `kali`/`kali`
   4. "`sudo mon0up`" 실행하여 Wi-Fi 인터페이스를 모니터 모드로 설정(사용 중에도 가능)
   5. "`airodump -i mon0`", "`kismet -c mon0`", "`wifite -i mon0`" 또는 원하는 도구 실행
5. 마음껏 활용하세요

선택사항:
- Pi-Tail에서 "`vncserver`" 실행, 스마트폰에서 VNC 뷰어 실행 후 127.0.0.1:5901에 연결하세요
참고: 처음 연결할 때는 몇 분 정도 기다려야 합니다. 다음 번에는 더 빨라질 것입니다. 하지만 명령줄 사용이 여전히 훨씬 빠릅니다.
SSH를 통한 VNC 연결 터널링에 대한 자세한 정보는 [이 스레드](https://whitedome.com.au/re4son/topic/vnc/)를 확인하세요.

더 많은 정보와 테더링 옵션은 `/boot/Pi-Tail.README` 및 `/boot/Pi-Tail.HOWTO`를 참조하세요

- - -

## 작동 원리:

Pi-Tail은 Kali Linux를 실행하며, 스마트폰은 전원 공급, 화면, 키보드 및 마우스 역할을 합니다. 멋진 팀워크입니다.

네트워크 구성은 오프라인에서 준비할 수 있어 완벽한 현장 동반자가 됩니다.
마이크로-SD 카드에 이미지를 쓰고, `/boot/interfaces 및 /boot/wpa_supplicant.conf`를 테더링 요구사항에 맞게 조정하고, 카드를 라즈베리파이에 삽입한 뒤 모바일에 연결하기만 하면 됩니다. 블루투스 테더링의 경우, `/boot/pi-tailbt.conf`에 스마트폰의 MAC 주소를 추가하기만 하면 됩니다.

Pi-Tail은 부팅 중 이더넷 가젯 모드가 활성화되어 있는지 확인하고, 그렇지 않으면 자동으로 대용량 저장소 가젯 모드로 전환합니다. 즉, 모바일 폰에 USB 스틱처럼 나타납니다. 이제 휴대폰으로 네트워크 구성을 편집할 수 있습니다.

부팅 과정 중 Pi-Tail은 네트워크 구성을 가져와서 시스템 파티션에 복사합니다. 유효한 구성은 오프라인 문제 해결을 위해 /boot/interfaces.active와 /boot/wpa_supplicant.active로 다시 복사됩니다.

휴대폰에서 "Wi-Fi 핫스팟" 또는 "USB 테더링"을 활성화하면 Pi-Tail이 자동으로 연결됩니다. 블루투스는 반대 방식으로 작동합니다: 부팅 후 3분 이내에 Pi-Tail과 페어링해야 합니다.

모바일에서 ConnectBot을 열고 Pi-Tail에 연결하세요. 포트 포워딩을 구성하고 VNC 뷰어를 실행하면 이동 중에도 Kali Linux의 모든 기능을 즐길 수 있습니다.

비밀번호:  
- ssh: `kali` / `kali`
- vnc: `toortoor`
  
기본 Wi-Fi:
예제 구성 파일을 사용할 수 있습니다. 스마트폰의 SSID를 "`sepultura`", 비밀번호를 "`R4t4m4h4tt4`"로 변경하면 Pi-Tail이 바로 테더링됩니다.

더 많은 정보는 [/boot/Pi-Tail.README](https://github.com/Re4son/RPi-Tweaks/blob/master/pi-tail/Pi-Tail.README)와 [/boot/Pi-Tail.HOWTO](https://github.com/Re4son/RPi-Tweaks/blob/master/pi-tail/Pi-Tail.HOWTO)에서 확인하세요  

## 문제 해결

SSH를 통해 Pi에 연결할 수 없고 안드로이드 폰을 핫스팟으로 사용하는 기본 설정을 사용 중이라면,
다른 기기에서 해당 핫스팟에 연결하여 받은 IP 구성을 확인하세요.
완전히 다른 IP 할당을 볼 수 있습니다. 예:

```
ip: 192.168.186.xxx
subnet mask: 255.255.255.0
gateway: 192.168.186.103
```

이런 경우 Pi의 전원을 끄고, 컴퓨터에서 마이크로 SD 카드 내용을 열어 `interfaces` 파일을 편집하세요.
`192.168.43.`을 검색하여 모두 `192.168.186.`로 바꿉니다. 여기서 `192.168.186.`은 모바일 핫스팟에 연결하여 받은 IP 주소를 기반으로 합니다.
위 예시에서는 `gateway 192.168.186.1` 항목을 받은 게이트웨이(`.103`)로 업데이트해야 합니다.

IP 주소도 `254`에서 `251`로 변경했지만 이는 필요하지 않을 수 있습니다.
변경 사항을 저장한 후 마이크로SD 카드를 Pi에 다시 넣고 부팅하세요. 데이터 전송이 가능한 포트가 아닌 전원 포트를 사용하세요.

### 대안으로 IP 주소를 변경하는 스크립트를 추가할 수도 있습니다

`interfaces` 파일의 `iface sepultura inet static` 섹션을 수정하여 모든 IP 주소를 제거하고 DHCP를 통해 주소를 가져오도록 강제한 다음, IP 주소의 마지막 옥텟을 200으로 변경하는 스크립트를 사용할 수 있습니다. 이렇게 하면 안드로이드 장치가 재부팅 후 IP 주소 지정을 변경하더라도 PiTail의 IP를 항상 알 수 있습니다.

단계:
sepultura 네트워크를 참조하는 섹션을 다음과 같이 변경하세요:
변경 전:

```sh
iface sepultura inet static
    address 192.168.186.251
    netmask 255.255.255.0
    gateway 192.168.186.103
```

**참고: 여러분의 IP 주소 값은 다를 수 있습니다**

변경 후:

```sh
iface sepultura inet dhcp
  post-up /boot/change_ip.sh
```

`interfaces` 파일이 있는 동일한 폴더에 다음 내용으로 `change_ip.sh` 스크립트를 생성하세요:

```sh
#!/bin/bash

# DHCP에서 할당한 현재 IP 주소와 게이트웨이 가져오기
IP_ADDRESS=$(ip -4 -o addr show wlan0 | awk '{print $4}')
GATEWAY=$(ip route | awk '/default via/ {print $3}')

# 처음 세 개의 옥텟 추출
BASE_IP=$(echo $IP_ADDRESS | cut -d'.' -f1-3)

# 마지막 옥텟이 200으로 설정된 새 IP 주소 할당
NEW_IP="$BASE_IP.200"

# 다른 DHCP 구성은 유지하면서 IP 주소 수정
ifconfig wlan0 $NEW_IP netmask 255.255.255.0 up
route add default gw $GATEWAY
```

내용을 저장하고 실행 가능하게 만든 후 PiTail을 다시 시작하세요.
이제 연결하기 위해 필요한 것은 `ipconfig`를 사용하여 핫스팟의 IP를 확인하는 것뿐입니다.
PiTail의 마지막 옥텟이 `200`이므로 핫스팟의 IP가 `192.168.67.123`이었다면 PiTail의 IP는 `192.168.67.200`이 됩니다.

- - -

문제, 질문, 피드백이 있으신가요? [Kali 공식 포럼](https://forums.kali.org/) 또는 [ROKFOSS 커뮤니티](https://chat.krfoss.org)에서 만나요
