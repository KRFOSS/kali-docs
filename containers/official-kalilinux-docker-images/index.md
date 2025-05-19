---
title: 공식 칼리 리눅스 도커 이미지
description: 
icon: 
weight: 
author:
  - rhertzog
번역:
  - ryuyijun
---
Kali는 [Docker Hub](https://hub.docker.com/u/kalilinux/)에서 일주일에 한 번 업데이트되는 공식 Kali Docker 이미지를 제공합니다. 따라서 Kali에서 제공하는 이미지를 기반으로 자신만의 Kali 컨테이너를 쉽게 구축할 수 있습니다. 각기 다른 [소스 브랜치](/general-use/kali-branches/)를 사용하는 다양한 Kali Linux 버전을 제공하여 사용자의 필요에 맞는 다양한 이미지를 제공합니다.

주의: **아래의 모든 이미지에는 "기본" [메타패키지](/general-use/metapackages/)가 포함되어 있지 않습니다**. `apt update && apt -y install kali-linux-headless` 명령어를 실행하여 설치해야 합니다.

다음은 사용 가능한 다양한 이미지에 대한 간단한 review입니다(자세한 정보는 [브랜치](/general-use/kali-branches/) 페이지를 참조하세요). 먼저 일반적으로 사용하기에 적합한 이미지들입니다:

- **[kalilinux/kali-rolling](https://hub.docker.com/r/kalilinux/kali-rolling)은 주로 사용해야 할 메인 이미지**입니다. 기본 이미지와 마찬가지로 지속적으로 업데이트되는 `kali-rolling` 패키지 저장소를 추적합니다.
- [kalilinux/kali-last-release](https://hub.docker.com/r/kalilinux/kali-last-release)는 `kali-last-snapshot` 저장소에서 빌드되며, 마지막 버전 릴리스(예: 2019.4, 2020.1 등)를 추적하고 [다음 릴리스](/releases/)까지 업데이트되지 않습니다.

그리고 매우 특별한 경우를 제외하고는 필요하지 않을 이미지들입니다:

- [kalilinux/kali-bleeding-edge](https://hub.docker.com/r/kalilinux/kali-bleeding-edge)는 ["kali-bleeding-edge" 저장소](/blog/bleeding-edge-kali-repositories/)가 활성화된 점을 제외하면 `kalilinux/kali-rolling`과 동일합니다.
- [kalilinux/kali-experimental](https://hub.docker.com/r/kalilinux/kali-experimental)은 `kali-experimental` 저장소가 활성화된 점을 제외하면 `kalilinux/kali-rolling`과 동일합니다. Kali 개발자가 피드백을 구하기 위해 "kali-experimental"에 업로드한 아직 준비되지 않은 업데이트를 테스트하는 데 유용할 수 있습니다.
- [kalilinux/kali-dev](https://hub.docker.com/r/kalilinux/kali-dev)는 Kali 개발자가 Debian에서 오는 업데이트와 Kali Linux에서 유지 관리하는 변경 사항을 병합하는 데 사용하는 `kali-dev` 저장소를 추적하는 이미지입니다. Kali 패키지를 재빌드(또는 테스트 재빌드)하는 데 유용할 수 있습니다.

공식 Docker 이미지를 개선하고 싶다면 [Kali GitLab](https://gitlab.com/kalilinux)의 [kali-docker 프로젝트](https://gitlab.com/kalilinux/build-scripts/kali-docker/)를 확인하세요. Kali는 Docker 이미지 빌드를 자동화하기 위해 [GitLab CI](https://gitlab.com/kalilinux/build-scripts/kali-docker/pipelines)를 사용합니다.