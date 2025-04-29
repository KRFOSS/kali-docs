---
title: Kali Linux 오픈 소스 정책
description:
icon:
weight:
author: ["g0tmi1k",]
---

Kali Linux는 메인 섹션에 수천 개의 자유 소프트웨어 [패키지](https://pkg.kali.org/)를 집대성한 리눅스 배포판입니다. Debian 파생판인 만큼, Kali Linux의 모든 핵심 소프트웨어는 [Debian 자유 소프트웨어 지침](https://www.debian.org/social_contract#guidelines)을 준수합니다.

### 예외: non-free(비자유) 섹션

위 원칙의 예외로, Kali Linux의 **non-free** 섹션에는 오픈 소스가 아닌 몇몇 도구가 포함되어 있습니다. 이들 도구는 해당 벤더와 [OffSec](https://www.offsec.com/?utm_source=kali&utm_medium=web&utm_campaign=docs) 간에 체결된 기본 또는 별도 라이선스 계약에 따라 재배포가 허가되었습니다.

파생 배포판을 만들고자 한다면, **Kali 전용 non-free 패키지**를 포함하기 전에 반드시 _각 패키지의 라이선스_를 검토해야 합니다. 단, Debian에서 가져온 non-free 패키지는 자유롭게 재배포할 수 있으니 안심하셔도 됩니다.

### GNU GPL 적용 사항

더 중요한 점으로, Kali Linux 인프라에서 별도로 개발된 요소나 포함된 소프트웨어와의 통합 작업은 모두 [GNU GPL](http://www.gnu.org/licenses/gpl.html) 라이선스 하에 배포됩니다.

### 라이선스 확인 방법

특정 소프트웨어의 라이선스를 자세히 알고 싶다면 소스 패키지의 `debian/copyright` 파일을 확인하시거나, 이미 설치한 경우 `/usr/share/doc/<패키지명>/copyright` 파일을 살펴보시면 됩니다.
