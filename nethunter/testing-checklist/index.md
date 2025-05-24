---
title: 테스트 체크리스트
description:
icon:
weight: 40
author: ["g0tmi1k",]
번역: ["xenix4845"]
---

새로운 기기 테스트를 위한 체크리스트 시작:

- 부팅이 되나요?

- 애플리케이션이 설치되었고 작동하나요?

    - Android VNC
    - BlueNMEA
    - DriveDroid
    - Hacker's Keyboard
    - 칼리 넷헌터 애플리케이션
    - RF Analyzer
    - SuperSU (별도 설치)
    - Terminal Emulator

- 설정 > 휴대전화 정보 > 커널 버전 확인 - Kali라고 표시되나요?

- 칼리 넷헌터 애플리케이션 실행

    - HID 키보드 공격
        - 구성 파일을 업데이트/작성할 수 있나요?
        - 작동하나요?

- 칼리 터미널 쉘 실행

    - Metasploit
        - 명령어 실행: `service postgresql start && service metasploit start`
            - 사용자명/데이터베이스가 생성되었나요?
        - 명령어 실행: msfconsole
            - 인내심을 가지세요. 10분 이상 걸릴 수 있어요

    - Social Engineering Toolkit

- 외부 WiFi 지원

    - 기기 연결 후 명령어 실행: `ifconfig wlan1 up && wifite`
        - wifite가 wlan1을 보나요?
    - 명령어 실행 (wlan1이 활성화되었다고 가정): `airmon-ng start wlan1 && airodump-ng mon0`
        - 오류 확인
    - kalimenu > Wireless attacks > kismet 실행

- Kalimenu
