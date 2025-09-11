---
title: 넷헌터 CARsenal : 시뮬레이터
description: SocketCAN용 계기판 시뮬레이터(ICSim) 및 통합 진단 서비스 시뮬레이터(UDSim)
icon:
weight:
author: ["v0lk3n",]
번역: ["kmw0410"]
---

<p style="text-align: center"><img src="../assets/simulator.gif" width="350" alt="CARsenal Simulator"></p>

> 시뮬레이터가 실행되면, ICSim/UDSim을 플로팅 모드로 만들어 더 쉽게 제어할 수 있으며, 또한 컨트롤 웹뷰를 활성화하거나 비활성화할 수도 있어요.

### 어떻게 작동하나요?

시뮬레이터를 시작할 때, kdex나 다른 프로그램이 실행 중일 경우를 문제를 피하기 위해 디스플레이 3~5를 사용해요.

- 디스플레이 3 : ICSim
- 디스플레이 4 : Controls
- 디스플레이 5 : UDSim

그런 다음 각 디스플레이에서 가상 프레임버퍼(Xvfb)를 시작하고, 윈도우 매니저로 fluxbox를 실행하여, x11vnc를 VNC 서버로 시작하세요.

설정이 완료되면, 각 VNC 디스플레이에서 시뮬레이터를 실행하고 브라우저에서 접근할 수 있도록 noVNC를 시작하세요.

마지막으로, 넷헌터 앱이 noVNC의 웹뷰를 불러와 화면을 제공할 거에요.


### ICSim

ICSim 문서는 <a href="https://github.com/zombieCraig/ICSim" target="_blank">여기</a>에서 확인할 수 있어요.

ICSim는 <a href="https://raw.githubusercontent.com/V0lk3n/NetHunter-CARsenal/refs/heads/main/icsim_service.sh">다음 스크립트</a>를 통해 시작하거나 종료할 수 있어요.

### UDSim

UDSim 문서는 <a href="https://github.com/zombieCraig/UDSim" target="_blank">여기</a>에서 확인할 수 있어요.

UDSim는 <a href="https://raw.githubusercontent.com/V0lk3n/NetHunter-CARsenal/refs/heads/main/udsim_service.sh">다음 스크립트</a>를 통해 시작하거나 종료할 수 있어요.
