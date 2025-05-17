---
title: Kali Linux 메타패키지
description:
icon:
weight: 5
author: ["gamb1t", "g0tmi1k", "daniruiz",]
번역: ["xenix4845"]
---

# 메타패키지란 무엇인가요

[메타패키지](/tools/kali-meta/)는 다른 패키지에 대한 종속성 목록으로 생성되어 한 번에 많은 패키지를 설치하는 데 사용돼요. Kali Linux는 이를 몇 가지 방법으로 활용해요. 한 가지 방법은 사용자가 전체 Kali 목록에서 설치하고 싶은 패키지 수를 결정할 수 있도록 하는 거예요. Linux를 사용하기에 충분한 정도만 필요한가요? 침투 테스트를 수행할 정도로 충분히 원하나요? 아니면 Kali에서 사용 가능한 거의 모든 패키지가 필요한가요?

메타패키지를 설치하려면, 먼저 시스템을 업데이트할 거예요. 필수는 아니지만, 메타패키지가 예상치 못한 부작용 없이 설치될 수 있도록 하기 위해 이 단계를 강력히 권장해요. Kali 업데이트 절차는 [Kali 업데이트](/docs/general-use/updating-kali/) 페이지에 자세히 설명되어 있지만, 간단히 말하면 두 개의 명령어로 요약돼요:

```console
kali@kali:~$ sudo apt update
kali@kali:~$
kali@kali:~$ sudo apt full-upgrade -y
kali@kali:~$
```

위 단계는 업데이트해야 하는 패키지 수에 따라 시간이 걸릴 수 있어요. 완료되면, 메타패키지(이 예제에서는 `kali-linux-default`) 설치는 간단히 하나의 명령어를 실행하는 문제일 뿐이에요:

```console
kali@kali:~$ sudo apt install -y kali-linux-default
kali@kali:~$
```

또는 대안으로 `kali-tweaks`를 사용하여 메타패키지 그룹을 설치할 수 있어요. 먼저 다음 명령을 실행해요:

```console
kali@kali:~$ kali-tweaks
```

여기서 "메타패키지" 탭으로 이동할 거예요. 원하는 메타패키지를 선택한 다음 "적용"을 누른 후 "확인"을 누르고 마지막으로 비밀번호를 입력하면 돼요.

## 시스템

- `kali-linux-core`: 기본 Kali Linux 시스템 – 항상 포함되는 핵심 항목
- `kali-linux-headless`: GUI가 필요 없는 기본 설치
- `kali-linux-default`: "기본" 데스크톱 이미지에는 이러한 도구가 포함돼요
- `kali-linux-arm`: ARM 장치에 적합한 모든 도구
- `kali-linux-nethunter`: Kali NetHunter의 일부로 사용되는 도구

## 데스크톱 환경/윈도우 매니저

- `kali-desktop-core`: GUI 이미지에 필요한 모든 핵심 도구
- `kali-desktop-e17`: Enlightenment (WM)
- `kali-desktop-gnome`: GNOME (DE)
- `kali-desktop-i3`: i3 (WM)
- `kali-desktop-kde`: KDE (DE)
- `kali-desktop-lxde`: LXDE (WM)
- `kali-desktop-mate`: MATE (DE)
- `kali-desktop-xfce`: Xfce (WM)

## 도구

- `kali-tools-gpu`: GPU 하드웨어 액세스의 이점을 얻는 도구
- `kali-tools-hardware`: 하드웨어 해킹 도구
- `kali-tools-crypto-stego`: 암호화 및 스테가노그래피 기반 도구
- `kali-tools-fuzzing`: 프로토콜 퍼징용
- `kali-tools-802-11`: 802.11 (일반적으로 "Wi-Fi"로 알려짐)
- `kali-tools-bluetooth`: 블루투스 장치를 대상으로 하는 도구
- `kali-tools-rfid`: 무선 주파수 식별 도구
- `kali-tools-sdr`: 소프트웨어 정의 라디오 도구
- `kali-tools-voip`: 인터넷 전화 도구
- `kali-tools-windows-resources`: Windows 호스트에서 실행할 수 있는 리소스
- `kali-linux-labs`: 학습 및 연습을 위한 환경

## 메뉴

- `kali-tools-information-gathering`: 오픈 소스 인텔리전스(OSINT) 및 정보 수집에 사용
- `kali-tools-vulnerability`: 취약점 평가 도구
- `kali-tools-web`: 웹 애플리케이션 공격 수행을 위한 설계
- `kali-tools-database`: 데이터베이스 공격을 중심으로
- `kali-tools-passwords`: 온라인 및 오프라인 비밀번호 크래킹 공격에 유용
- `kali-tools-wireless`: 802.11, 블루투스, RFID 및 SDR 등 무선 프로토콜 기반의 모든 도구
- `kali-tools-reverse-engineering`: 바이너리 역공학 분석용
- `kali-tools-exploitation`: 일반적으로 익스플로잇에 사용
- `kali-tools-social-engineering`: 사회 공학 기법을 위한 목적
- `kali-tools-sniffing-spoofing`: 스니핑 및 스푸핑을 위한 모든 도구
- `kali-tools-post-exploitation`: 익스플로잇 후 단계에 대한 기술
- `kali-tools-forensics`: 포렌식 도구 – 라이브 및 오프라인
- `kali-tools-reporting`: 보고서 작성 도구

## 기타

- `kali-linux-large`: 이전에 사용하던 이미지용 기본 도구
- `kali-linux-everything`: 여기에 나열된 모든 메타패키지 및 도구
- `kali-desktop-live`: 이미지에서 부팅할 때 라이브 세션 중에 사용
