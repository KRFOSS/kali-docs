---
title: Kali 2023.2에서 소리가 안 나올 때
description:
icon:
weight: 5000
author: ["arnaudr", "gamb1t",]
번역: ["xenix4845"]
---

## 배경

Kali Linux 2023.2 버전부터는 XFCE와 GNOME 데스크톱 환경 모두에서 오디오를 처리하기 위해 [PipeWire](https://pipewire.org/)(파이프와이어)를 사용해요. 그 전에는 [PulseAudio](https://www.freedesktop.org/wiki/Software/PulseAudio/)(펄스오디오)라는 다른 사운드 서버를 사용했었어요. (참고: Kali의 KDE 데스크톱을 사용하시는 분들은 여전히 PulseAudio를 사용해요)

PipeWire는 호환성 레이어(서비스 이름: `pipewire-pulse`)를 제공하기 때문에 이 변화는 원활해야 해요. 따라서 PulseAudio용으로 설계된 기존 애플리케이션들은 아무 일도 없었던 것처럼, 변화를 전혀 알아채지 못한 채로 계속 작동해야 해요.

Kali 2023.2로 업그레이드한 후 오디오 문제가 발생한다면: 인터넷에서 많이 찾을 수 있는 조언과는 달리, **절대로 `pipewire-pulse` 패키지를 제거하지 마세요**! 이것은 예전 버전의 Kali Linux에서는 맞는 해결책이었지만, 이제는 인터넷 곳곳에 복사/붙여넣기 되고 있어요. 하지만 이제 우리가 PipeWire로 전환했기 때문에 이 패키지를 제거하면 오히려 역효과가 나고, 시스템이 더 망가질 수 있어요. 제발 그러지 마세요.

## PipeWire로 업그레이드하기

Kali를 업그레이드하는 사용자라면, 평소처럼 시스템을 업그레이드하면 됩니다. 아래 두 명령어를 실행하세요:

```console
kali@kali:~$ sudo apt update
kali@kali:~$
kali@kali:~$ sudo apt full-upgrade -y
kali@kali:~$
```

이렇게 하면 PipeWire가 자동으로 설치돼요. 재부팅 후에 적용되니까, a) PipeWire가 실행 중인지, b) 오디오가 작동하는지 확인하려면 _바로_ 재부팅하는 게 좋아요!

만약 이후에도 소리가 나지 않는다면, 다음으로 시도해볼 수 있는 것은 아래 명령어로 `pipewire-audio` 메타패키지를 설치하는 거예요:

```console
kali@kali:~$ sudo apt install --mark-auto -y pipewire-audio
```

그런 다음 다시 재부팅하세요.

## Intel SOF 오디오 장치용 펌웨어 누락

이 문제는 실제 하드웨어에 Kali를 설치한 경우에만 해당돼요. 일부 최신 인텔 사운드카드는 작동하기 위해 펌웨어가 필요한데, 이 펌웨어가 시스템에 설치되지 않았을 수 있어요. 확실하지 않다면 설치해보세요. 아무런 해도 끼치지 않아요!:

```console
kali@kali:~$ sudo apt update
kali@kali:~$
kali@kali:~$ sudo apt install -y firmware-sof-signed
kali@kali:~$
```

그런 다음 시스템을 재부팅 해주세요.

## VMware Workstation에서의 문제

VMware Workstation에서 오디오가 지직거리는 소리를 내거나 깜빡이는 현상이 보고되었어요. 이 짧은 가이드를 따라하면 해결할 수 있는 것 같아요: <https://gitlab.freedesktop.org/pipewire/pipewire/-/wikis/Troubleshooting#stuttering-audio-in-virtual-machine>.
