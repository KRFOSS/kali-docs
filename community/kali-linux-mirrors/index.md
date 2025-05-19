---
title: 공식 칼리 리눅스 미러
description:
icon:
weight:
author: ["g0tmi1k","xenix4845"]
번역: ["xenix4845"]
---

## 공식 저장소 사용하기

{{% notice info %}}
ROKFOSS 프로젝트에서는 칼리 리눅스 분산미러를 운영하고 있어요. 자세한 사항은 [분산미러](https://http.krfoss.org/) 사이트에서 확인하실 수 있어요. 
{{% /notice %}}

칼리 리눅스 배포판은 전 세계에 미러링된 두 개의 [저장소](/docs/general-use/kali-linux-sources-list-repositories/)가 있습니다:

- [http.kali.org](http://http.kali.org/) ([미러 목록](http://http.kali.org/README?mirrorlist)): 주요 패키지 저장소;
- [cdimage.kali.org](http://cdimage.kali.org/) ([미러 목록](http://cdimage.kali.org/README?mirrorlist)): 사전 빌드된 칼리 ISO 이미지 저장소.

위에 나열된 기본 호스트를 사용하면 지리적으로 가까운 미러 사이트로 자동 연결되며, 이 미러 사이트는 항상 최신 상태를 유지합니다. 수동으로 미러를 선택하고 싶다면, 각 호스트 이름 옆에 있는 **미러 목록** 링크를 클릭하고 원하는 미러를 선택하세요. 그런 다음 선택한 값에 따라 `/etc/apt/sources.list` 파일을 적절히 편집해야 합니다.

{{% notice info %}}
중요! <a href="/docs/general-use/kali-linux-sources-list-repositories/"/>/etc/apt/sources.list</a> 파일에 추가 저장소를 추가하지 마세요.<br />
<br />
추가할 경우 칼리 설치가 손상될 가능성이 높습니다.
{{% /notice %}}
