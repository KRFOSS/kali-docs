---
title: Raspberry Pi Zero W P4wnP1 A.L.O.A
description:
icon:
weight:
author: ["steev",]
번역: ["xenix4845"]
---

## 소개

Raspberry Pi Zero W P4wnP1 A.L.O.A. (**A** **L**ittle **O**ffensive **A**pplication) 이미지는 Kali Linux의 고도로 커스터마이즈된 버전입니다. 라즈베리 파이를 컴퓨터에 연결하여 명령을 보내거나 직접 상호작용 없이도 네트워킹을 사용할 수 있으며, 물론 직접 상호작용도 가능합니다!

P4wnP1 A.L.O.A 소프트웨어는 플러그 앤 플레이 USB 장치 에뮬레이션과 같은 [원래 P4wnP1](https://p4wnp1.readthedocs.io/en/latest/)이 가지고 있던 다양한 [기능](#features)을 포함하고 있습니다. 수정된 [Nexmon](https://github.com/seemoo-lab/nexmon) 펌웨어를 통한 Wi-Fi(KARMA 공격 가능), 블루투스 지원, Wi-Fi 은닉 채널을 제공하며, 모니터 모드도 포함되어 있지만 **공식 지원되지는 않습니다**. 또한 페이로드를 위한 [DuckyScript](https://github.com/hak5darren/USB-Rubber-Ducky/wiki/Duckyscript)와 유사하지만 JavaScript 기반인 HIDScript를 추가했습니다.

{{% notice info %}}
현재 P4wnP1 A.L.O.A. 이미지는 Raspberry Pi Zero W만 지원하며, Zero 2 W는 **지원하지 않습니다**.
<!-- Raspberry Pi Zero 2 W용 Nexmon 지원이 있지만, Wi-Fi 은닉 채널 지원과 함께 작동하지 않습니다. -->
{{% /notice %}}

## 빠른 설치 및 사용법

1. [다운로드](/get-kali/#kali-arm) 영역에서 `Kali Linux Raspberry Pi Zero W P4wnP1 ALOA` 이미지를 다운로드 _및 검증_하세요. 이미지 검증 과정은 [Kali Linux 다운로드](/docs/introduction/download-official-kali-linux-images/)에서 더 자세히 설명합니다.

2. **[dd](https://manpages.debian.org/testing/coreutils/dd.1.en.html)** 유틸리티를 사용하여 이 파일을 microSD 카드에 쓰세요. 예시에서는 `/dev/sdX`에 위치한 microSD를 사용합니다. **_필요에 따라 변경하세요._**

{{% notice info %}}
이 과정은 microSD 카드의 모든 데이터를 지웁니다. 잘못된 저장 장치를 선택하면 컴퓨터의 하드 디스크가 지워질 수 있습니다.
{{% /notice %}}

```console
$ xzcat kali-linux-2025.1-raspberry-pi-zero-w-p4wnp1-aloa-armel.img.xz | sudo dd of=/dev/sdX bs=4M status=progress
```

이 과정은 PC 성능, microSD 카드 속도 및 Kali Linux 이미지 크기에 따라 시간이 걸릴 수 있습니다.

3. _dd_ 작업이 완료되면 microSD 카드를 Raspberry Pi Zero W에 삽입하세요.

4. 다른 컴퓨터에서 P4wnP1 A.L.O.A.의 기본 무선 네트워크 `💥🖥💥 Ⓟ➃ⓌⓃ🅟❶`에 비밀번호 `MaMe82-P4wnP1`로 연결하세요.

5. P4wnP1 A.L.O.A. 무선 네트워크에 연결되면 다음 방법으로 시스템에 접속할 수 있습니다:

- 웹 인터페이스 (<http://172.24.0.1:8000>)
- SSH를 사용한 명령줄 (`ssh kali@172.24.0.1`), 그 다음 `P4wnP1_cli` 명령 실행
- 로컬에서 P4wnP1 A.L.O.A. CLI 소프트웨어를 컴파일하고, 명령과 함께 `host` 전달

{{% notice info %}}
이 Kali Linux 이미지의 중요한 커스터마이제이션 중 하나는 `root`와 `kali` 사용자 모두 SSH로 로그인할 수 있다는 점입니다.
`root` 사용자는 [기본 비밀번호](/docs/introduction/default-credentials/)인 `toor`를 사용합니다.
{{% /notice %}}

6. 마음껏 활용하세요

## 기능

MaMe82의 P4wnP1 A.L.O.A.는 Raspberry Pi Zero W를 유연하고 저비용의 펜테스팅, 레드 팀 및 물리적 관여를 위한 플랫폼으로 변환하는 프레임워크입니다... 또는 "A Little Offensive Appliance"(약간의 공격적 어플라이언스)로 변환합니다.

P4wnP1 A.L.O.A.는 다음을 목적으로 하지 않습니다:

- "무기화된" 도구가 되는 것
- 무슨 일이 일어나는지 또는 어떤 위험이 수반되는지 이해하지 않고도 누구나 수행할 수 있는 RTR 페이로드 제공

P4wnP1 A.L.O.A.는 다음을 목적으로 합니다:

- 유연하고, 저비용이며, 주머니 크기의 플랫폼이 되는 것
- 여기 설명된 것과 같은 작업을 가능하게 하는 도구가 되는 것
- 완성된 정적 솔루션을 제공하지 않고, 펜테스트나 레드팀 참여 중에 일반적으로 사용되는 모든 종류의 USB 관련 작업을 프로토타이핑, 테스팅 및 수행하는 것을 지원하는 것

P4wnP1 A.L.O.A.는 주어진 구성요소를 활용하여 다음과 같은 작업을 수행하는 구성을 제공합니다:

- HID 은닉 채널을 통해 키스트로크 주입을 기반으로 스테이지2를 다운로드하기 위한 인메모리 클라이언트 코드를 전달하기 위해 Windows 호스트에 대한 드라이브바이 공격
- P4wnP1이 USB 호스트에 연결되는 즉시 키스트로크 주입 시작 (HIDScript를 실행하는 TriggerAction)
- 키스트로크 주입이 시작되는 즉시 HID 은닉 채널을 통해 Wi-Fi 은닉 채널 클라이언트 에이전트를 전달하는 스테이저 시작 (외부 서버를 시작하는 bash 스크립트를 실행하는 TriggerAction)
- 필요할 때 Wi-Fi 은닉 채널 서버 시작 (동일한 TriggerAction 및 BashScript)
- USB 키보드(키스트로크 주입 허용)와 추가 원시 HID 장치(스테이지2 전달을 위한 은닉 채널 역할)를 제공하는 USB 설정 배포 - USB 설정은 설정 템플릿에 저장됨
- Wi-Fi 은닉 채널 서버의 CLI 프런트엔드와 상호작용할 수 있도록 P4wnP1에 원격 액세스를 허용하는 Wi-Fi 설정 배포 - Wi-Fi 설정은 설정 템플릿에 저장됨
- 필요한 모든 구성을 한 번에 배포하는 단일 진입점 제공 (적절한 Wi-Fi 설정, 적절한 USB 설정 및 HIDScript를 시작하는 데 필요한 TriggerActions로 구성된 마스터 템플릿)

최신 정보는 프로젝트의 [README](https://github.com/RoganDawes/P4wnP1_aloa/blob/master/README.md)에서 확인할 수 있습니다.

[기본 비밀번호](/docs/introduction/default-credentials/)를 다시 확인하려면:

- SSH:
   - `root`/`toor`
   - `kali`/`kali`

- Wi-Fi:
   - SSID: `💥🖥💥 Ⓟ➃ⓌⓃ🅟❶`
   - PSK: `MaMe82-P4wnP1`

- - -

문제, 질문, 피드백이 있으신가요? [Kali 공식 포럼](https://forums.kali.org/) 또는 [ROKFOSS 커뮤니티](https://chat.krfoss.org)에서 만나요
