---
title: 넷헌터 CARsenal : 도구
description: can-utils 스위트의 강력한 기능을 확인하고, Freediag으로 차량을 진단하며, Cannelloni를 사용해 두 기기 간에 CAN 프레임을 전송하세요.
icon:
weight:
author: ["v0lk3n",]
번역: ["kmw0410"]
---

<p style="text-align: center"><img src="../assets/tools.gif" width="350" alt="CARsenal Tools"></p>

> 설정을 구성하면 명령어가 업데이트되며, 주황색 버튼을 길게 눌러 명령어를 편집할 수도 있어요.

### 도구 : 제공되는 도구

- <a href="https://github.com/linux-can/can-utils" target="_blank">can-utils</a> : SocketCAN 사용자 공간 유틸리티와 도구 모음.
    - <a href="https://manpages.debian.org/trixie/can-utils/cangen.1.en.html" target="_blank">cangen</a> : 테스트용 CAN 프레임 생성기.
    - <a href="https://manpages.debian.org/trixie/can-utils/cansniffer.1.en.html" target="_blank">cansniffer</a> : 변동하는 CAN 내용을 시각화.
    - <a href="https://manpages.debian.org/trixie/can-utils/candump.1.en.html" target="_blank">candump</a> : CAN 버스 트래픽 덤프.
    - <a href="https://manpages.debian.org/trixie/can-utils/cansend.1.en.html" target="_blank">cansend</a> : CAN_RAW 소켓을 통해 CAN 프레임 전송.
    - <a href="https://manpages.debian.org/trixie/can-utils/canplayer.1.en.html" target="_blank">canplayer</a> : 압축된 CAN 프레임 로그파일을 CAN 장치에 재생.
    - <a href="https://manpages.debian.org/trixie/can-utils/asc2log.1.en.html" target="_blank">asc2log</a> : ASC 로그파일을 압축 CAN 프레임 로그파일로 변환.
    - <a href="https://manpages.debian.org/trixie/can-utils/log2asc.1.en.html" target="_blank">log2asc</a> : 압축 CAN 프레임 로그파일을 ASC 로그파일로 변환.

- <a href="https://github.com/fenugrec/freediag" target="_blank">freediag</a> : 차량 진단 시스템 접근.
    - <a href="https://github.com/fenugrec/freediag" target="_blank">diagtest</a> : Freediag의 독립 실행 프로그램, 코드 경로 테스트용.

- <a href="https://github.com/mguentner/cannelloni" target="_blank">cannelloni</a> : UDP, TCP, SCTP를 사용해 두 기기 간 CAN 프레임 전송.

- <a href="https://raw.githubusercontent.com/V0lk3n/NetHunter-CarArsenal/refs/heads/main/sequence_finder.sh">sequence_finder</a> : 로그파일을 분할하고, CanPlayer로 재생해 원하는 동작의 정확한 시퀀스를 찾아 CanSend로 재생하는 맞춤 스크립트.
