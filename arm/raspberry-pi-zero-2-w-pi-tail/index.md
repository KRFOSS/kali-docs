---
title: Raspberry Pi-Tail Zero 2 W
description:
icon:
weight:
author: ["steev",]
번역: ["xenix4845"]
---

# Pi-Tail

![pi-tail-logo](images/pi-tail-logo.png)

- 테더링에 최적화된 라즈베리파이 2 Zero W용 Kali
- 블루투스 및 Wi-Fi 테더링을 위한 간단한 원케이블 솔루션
- 처음부터 이미지 설치, 구성, 연결, 부팅까지 2분 내로 가능
- 스마트폰에 ConnectBot과 VNC 뷰어만 설치하면 됨
- USB 이더넷과 대용량 저장소 간 자동 전환

![Pi-Tail](images/pi-tail-demo.jpg)

- - -

## 빠른 설치 및 사용법:

1. [여기서 이미지 다운로드](https://http.krfoss.org/)하여 마이크로-SD 카드에 쓰기
2. 카드를 라즈베리 파이 Zero 2 W에 삽입
3. 연결: OTG 어댑터를 스마트폰에, 표준 케이블을 Pi-Tail(전원)에 연결하여 Pi-Tail에 전원 공급
4. 스마트폰에서:
   1. SSID = "sepultura", 비밀번호 = "R4t4m4h4tt4"로 핫스팟 활성화
   2. Hacker's Keyboard, ConnectBot 및 VNC 뷰어(선택사항) 설치
   3. 192.168.43.254에 연결, 사용자 root/toor
   4. "mon0up" 실행하여 Wi-Fi 인터페이스를 모니터 모드로 설정(사용 중에도 가능)
   5. "airodump -i mon0", "kismet -c mon0", "wifite -i mon0" 또는 원하는 도구 실행
5. 마음껏 활용하세요

선택사항:
- Pi-Tail에서 "vncserver" 실행, 스마트폰에서 VNC 뷰어 실행 후
127.0.0.1:5901에 연결하세요  
참고: 처음 연결할 때는 몇 분 정도 기다려야 합니다. 다음 번에는 더 빨라질 것입니다. 하지만 명령줄 사용이 여전히 훨씬 빠릅니다.
SSH를 통한 VNC 연결 터널링에 대한 자세한 정보는 [이 스레드](https://whitedome.com.au/re4son/topic/vnc/)를 확인하세요.

더 많은 정보와 테더링 옵션은 /boot/Pi-Tail.README 및 /boot/Pi-Tail.HOWTO를 참조하세요  

- - -

## 작동 원리:

Pi-Tail은 Kali Linux를 실행하며, 스마트폰은 전원 공급, 화면, 키보드 및 마우스 역할을 합니다. 멋진 팀워크입니다.

네트워크 구성은 오프라인에서 준비할 수 있어 완벽한 현장 동반자가 됩니다.
마이크로-SD 카드에 이미지를 쓰고, /boot/interfaces와 /boot/wpa_supplicant.conf를 테더링 요구사항에 맞게 조정하고, 카드를 라즈베리파이에 삽입한 뒤 모바일에 연결하기만 하면 됩니다. 블루투스 테더링의 경우, /boot/pi-tailbt.conf에 스마트폰의 MAC 주소를 추가하기만 하면 됩니다.

Pi-Tail은 부팅 중 이더넷 가젯 모드가 활성화되어 있는지 확인하고, 그렇지 않으면 자동으로 대용량 저장소 가젯 모드로 전환합니다. 즉, 모바일 폰에 USB 스틱처럼 나타납니다. 이제 휴대폰으로 네트워크 구성을 편집할 수 있습니다.

부팅 과정 중 Pi-Tail은 네트워크 구성을 가져와서 시스템 파티션에 복사합니다. 유효한 구성은 오프라인 문제 해결을 위해 /boot/interfaces.active와 /boot/wpa_supplicant.active로 다시 복사됩니다.

휴대폰에서 "Wi-Fi 핫스팟" 또는 "USB 테더링"을 활성화하면 Pi-Tail이 자동으로 연결됩니다. 블루투스는 반대 방식으로 작동합니다: 부팅 후 3분 이내에 Pi-Tail과 페어링해야 합니다.

모바일에서 ConnectBot을 열고 Pi-Tail에 연결하세요. 포트 포워딩을 구성하고 VNC 뷰어를 실행하면 이동 중에도 Kali Linux의 모든 기능을 즐길 수 있습니다.

비밀번호:  
ssh: `root` / `toor`
vnc: `toortoor`
  
기본 Wi-Fi:
예제 구성 파일을 사용할 수 있습니다. 스마트폰의 SSID를 "sepultura", 비밀번호를 "R4t4m4h4tt4"로 변경하면 Pi-Tail이 바로 테더링됩니다.

더 많은 정보는 [/boot/Pi-Tail.README](https://github.com/Re4son/RPi-Tweaks/blob/master/pi-tail/Pi-Tail.README)와 [/boot/Pi-Tail.HOWTO](https://github.com/Re4son/RPi-Tweaks/blob/master/pi-tail/Pi-Tail.HOWTO)에서 확인하세요  

- - -

문제, 질문, 피드백이 있으신가요? [포럼](https://forums.kali.org/)에서 만나요!

한국에서는 [ROKFOSS 커뮤니티](https://chat.krfoss.org)에서 한국 사용자들과 정보를 교류할 수 있어요!