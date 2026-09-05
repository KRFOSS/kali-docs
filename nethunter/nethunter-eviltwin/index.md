---
title: 넷헌터 EvilTwin
description: 핸드셰이크 검증을 사용하는 가짜 AP 포털
icon:
weight:
author: ["dr.rootsu",]
번역: ["xenix4845"]
---

[EvilTwin](https://github.com/dr1408/eviltwin) 공격 모듈은 대상 네트워크의 핸드셰이크를 캡처하고, 핸드셰이크 검증을 이용해 비밀번호를 수집할 수 있도록 캡티브 포털이 있는 가짜 액세스 포인트를 만들어요.

대상 네트워크를 흉내 내는 캡티브 포털과 함께 가짜 액세스 포인트를 실행할 수 있어요. 대상 SSID와 모니터 인터페이스 이름 같은 설정을 원하는 대로 지정할 수 있어요. 이 모듈은 `wlan0`에서 가상 AP를 만들거나, 두 번째 외장 어댑터를 사용해서 가짜 AP를 송출하는 방식을 모두 지원해요. 인터넷 공유는 자동으로 감지되며 직접 지정할 수도 있어요.

![](https://gitlab.com/kalilinux/documentation/kali-docs/-/raw/4d7a9598a74f7d2553916338bd2f4b5d6206171e/nethunter/nethunter-eviltwin/nethunter-eviltwin.png)
*그림 1: EvilTwin 모듈의 메인 인터페이스*

설정을 마치고 **Start** 버튼을 누르면 공격이 시작돼요. 모듈은 다음 작업을 수행해요:

1. **핸드셰이크 캡처** - 클라이언트의 연결을 해제하고 WPA 핸드셰이크를 캡처해요.
2. **AP 시작** - 대상 SSID를 사용하는 가짜 AP를 만들어요.
3. **포털 제공** - 인증 정보를 수집할 수 있도록 캡티브 포털을 표시해요.
4. **공격 모니터링** - 연결 상태와 비밀번호 입력 시도를 실시간으로 기록해요.

![](https://gitlab.com/kalilinux/documentation/kali-docs/-/raw/4d7a9598a74f7d2553916338bd2f4b5d6206171e/nethunter/nethunter-eviltwin/nethunter-eviltwin-output.png)
*그림 2: 로그 출력과 함께 실행 중인 EvilTwin 공격*

### 기능

- Wi-Fi 네트워크 스캔
- 30초 타임아웃을 사용하는 클라이언트 감지
- 연결 해제 공격을 이용한 WPA 핸드셰이크 캡처
- 가상 AP 생성 또는 외장 어댑터 지원
- 캡티브 포털을 통한 비밀번호 수집
- 오프라인 크래킹을 위한 핸드셰이크 저장
- UI에서 실시간 공격 로그 확인
- 2.4GHz 및 5GHz 네트워크 지원

### 요구사항

- 모니터 모드를 지원하는 외장 Wi-Fi 어댑터
- 루트 권한

### 기여자

- [yesimxev](https://gitlab.com/yesimxev) - 모듈 개선 및 지원
- [Justxd22](https://github.com/Justxd22) - 핸드셰이크 검증 방식
