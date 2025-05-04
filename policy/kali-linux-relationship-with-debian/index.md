---
title: Kali와 Debian의 관계
description:
icon:
weight:
author: ["g0tmi1k",]
번역: ["xenix4845"]
---

Kali Linux 배포판은 [Debian Testing](https://www.debian.org/releases/testing/)을 기반으로 합니다. 따라서 Kali의 패키지 대부분은 Debian 저장소에서 그대로 가져옵니다. 경우에 따라 사용자 경험을 개선하거나 필요한 버그 수정을 반영하기 위해 Debian Unstable 또는 Debian Experimental에서 더 최신의 패키지를 가져올 때도 있습니다.

### 포크된 패키지

Kali만의 고유한 기능을 구현하기 위해, 몇몇 패키지는 **포크(fork : 원본 저장소를 복제·분기하여 별도 개발하는 것)** 하여 사용합니다. Kali 개발팀은 이러한 포크의 수를 최소화하고자, 가능할 때마다 **업스트림(upstream : 원본 프로젝트)** 패키지에 직접 기능을 통합하거나 필요한 **훅(hook : 확장 기능을 끼워 넣을 수 있는 지점)** 을 추가해, 추가 수정 없이도 원하는 기능을 쉽게 활성화할 수 있도록 노력합니다.

Kali가 포크한 각 패키지는 `debian` 브랜치(branch : 한 저장소 안에서 독립적으로 관리되는 작업 줄)를 포함한 [Git 저장소](https://gitlab.com/kalilinux)에서 관리됩니다. 덕분에 해당 패키지의 `master` 브랜치에서  
`git merge debian` (merge : 다른 브랜치의 변경 사항을 현재 브랜치에 병합하는 명령) 한 줄이면 포크된 패키지를 손쉽게 최신 상태로 업데이트할 수 있습니다.

### 추가 패키지

이 밖에도 Kali는 침투 테스트와 보안 감사를 위해 특별히 제작된 [추가 패키지](https://pkg.kali.org/)를 다수 포함합니다. 이들 중 대부분은 [Debian 자유 소프트웨어 지침](https://www.debian.org/social_contract#guidelines)에 따라 “자유 소프트웨어”에 해당합니다. Kali는 이러한 패키지를 Debian에 환원하고, Debian 내에서 직접 유지할 수 있도록 기여하는 것을 목표로 합니다.

이를 위해 Kali의 패키징 작업은 [Debian 정책](https://www.debian.org/doc/debian-policy/)을 준수하며, Debian에서 권장하는 모범 사례를 따르도록 힘쓰고 있습니다.
