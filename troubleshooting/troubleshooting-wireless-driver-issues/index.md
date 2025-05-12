---
title: 무선 드라이버 문제 해결하기
description:
icon:
weight:
author: ["g0tmi1k",]
번역: ["xenix4845"]
---

리눅스에서 무선 드라이버 문제를 해결하는 건 무엇을 확인해야 할지 모른다면 정말 답답한 경험이 될 수 있어요. 이 글은 여러분이 무선 문제를 해결하는 데 필요한 정보를 찾는 데 도움이 되는 일반적인 가이드라인으로 사용하세요. 무선 드라이버 정보에 대한 가장 자세한 출처는 [aircrack-ng 문서](http://www.aircrack-ng.org/documentation.html)예요.

{{% notice info %}}
저희에게 보고되는 무선 관련 문제의 90%는 사람들이 Aircrack-ng 문서를 읽지 않아서 생기는 거예요. 카드를 모니터 모드(무선 패킷 감시 모드)로 전환하기 전에 airmon-ng check kill 명령어를 실행해야 해요.
{{% /notice %}}

{{% notice info %}}
오류 메시지를 주의 깊게 읽어보세요. 대부분은 문제가 무엇인지, 어떻게 해결할 수 있는지 알려주고 있어요. 도움이 되지 않는다면 구글링을 해보세요.
{{% /notice %}}

### 1. 인터페이스가 없을 때

- 기본적인 질문이지만: 정말 무선 카드가 맞나요? (여러 번 봤던 케이스예요)
- 기기가 연결되어 있나요?
- **lsusb**나 **lspci** 명령어로 기기가 표시되나요? (폰은 예외) pci ids와 usb ids를 업데이트해보는 것도 좋아요
- **dmesg** 명령어 결과에 드라이버 로딩이나 실패에 대한 정보가 있나요?
- Kali를 VM(가상머신)에서 실행 중인가요? 그렇다면 USB 카드가 아닌 이상 사용할 수 없어요(VMware/VirtualBox/QEMU는 모든 PCI 장치를 가상화해요). VM에 연결되어 있나요?
- **dmesg**에 아무것도 없고 VM에서 실행 중이 아니라면 최신 _compat-wireless_(호환 무선 드라이버)를 시도해볼 수 있어요(때로는 펌웨어도 필요해요) -> Linux-Wireless 드라이버를 확인해보세요

### 2. 인터페이스는 있지만 아무것도 할 수 없을 때

- 오류 메시지를 읽어보세요
- 오류 메시지가 없다면 **dmesg | tail** 명령어를 실행해보세요. 대부분 무엇이 잘못됐는지 알려줄 거예요
- 펌웨어가 없을 수 있어요
- rfkill, 하드웨어 스위치, BIOS 옵션을 확인해보세요

### 3. 모니터 모드가 안 될 때

- STA 드라이버(Ralink, Broadcom)와 다른 제조사에서 제공하는 대부분의 드라이버는 모니터 모드를 지원하지 않아요
- ndiswrapper는 모니터 모드를 지원하지 않으며 앞으로도 지원하지 않을 거예요
- Airodump-ng/Wireshark에서 패킷이 표시되지 않을 때: rfkill, 하드웨어 스위치, BIOS 옵션을 확인해보세요

### 4. 패킷 주입(Injection)

- aireplay-ng -9로 테스트해보세요 (airmon-ng로 카드가 모니터 모드인지 확인하세요)
- Airmon-ng가 칩셋 정보를 표시하지 않는 경우: 크게 문제가 되지 않아요. 단지 카드에서 해당 정보를 가져오지 못한 것뿐이고, 카드의 기능에는 영향을 미치지 않아요
- 모니터 모드는 되지만 주입이 안 될 때: rfkill, 하드웨어 스위치, BIOS 옵션을 확인해보세요
- 네트워크 매니저가 때로는 Aircrack 도구와 충돌할 수 있어요. **airmon-ng check kill** 명령어를 실행해서 이런 프로세스를 종료하세요

### 추가 링크

- [내 카드가 Aircrack-ng와 호환될까요?](https://aircrack-ng.blogspot.com/2012/10/will-my-card-work-with-aircrack-ng.html)
- [Compat-wireless](https://aircrack-ng.blogspot.com/2012/03/compat-wireless.html)
