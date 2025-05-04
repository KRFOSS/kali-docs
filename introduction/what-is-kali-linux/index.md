---
title: 칼리 리눅스란 무엇인가요?
description:
icon:
weight: 10
author: ["g0tmi1k"]
번역: ["xenix4845"]
---

### 칼리 리눅스에 대하여

[칼리 리눅스](https://kali.org) _([백트랙 리눅스(BackTrack Linux)](https://www.backtrack-linux.org/)로 알려졌던)_ 는 사용자가 고급 침투 테스팅(모의해킹)과 보안 감사를 수행할 수 있게 해주는 [오픈소스](/docs/policy/kali-linux-open-source-policy/), [데비안 기반 리눅스](/docs/policy/kali-linux-relationship-with-debian/) 배포판이에요. 다양한 플랫폼에서 실행되며 정보 보안 전문가와 취미로 하시는 분들 모두가 무료로 이용할 수 있어요.

이 배포판은 [수백 가지 도구, 설정 및 스크립트](/features/)를 [업계에 특화된 수정사항](/docs/policy/penetration-testing-tools-policy/)과 함께 제공해서, 사용자가 관련 없는 작업에 시간을 쓰지 않고 컴퓨터 포렌식(디지털 증거 분석), 리버스 엔지니어링(역공학), 취약점 탐지 같은 작업에 집중할 수 있게 해줘요.

이 배포판은 특별히 경험 있는 침투 테스터의 요구에 맞게 만들어졌기 때문에, 이 사이트의 모든 문서는 일반적인 리눅스 운영 체제에 대한 사전 지식과 친숙함을 전제로 해요. 칼리를 특별하게 만드는 요소에 대한 자세한 내용은 [내가 칼리 리눅스를 사용해야 할까요?](/docs/introduction/should-i-use-kali-linux/)를 참고하세요.

- _칼리 리눅스의 특징을 더 자세히 알고 싶다면 다음 페이지를 확인하세요: [칼리 리눅스 개요](https://kali.org/features/)_


### 칼리 리눅스의 특징들

<!--
Tool count:
- https://pkg.kali.org/derivative/kali-roll/
- https://pkg.kali.org/teams/kali-developers/
- https://gitlab.com/kalilinux/packages/ + archived
-->

- **무료이며 앞으로도 계속 그럴 거예요:** 칼리 리눅스는 [완전히 무료](/docs/policy/kali-linux-open-source-policy/)이며 앞으로도 항상 그럴 거예요. 칼리 리눅스에 돈을 지불할 필요가 절대 없어요.
- **오픈소스 Git 트리를 제공해요:** 칼리 리눅스 팀은 오픈소스 개발 모델에 전념하고 있으며, [개발 트리](https://gitlab.com/kalilinux)는 모든 사람이 볼 수 있어요. 칼리 리눅스에 들어가는 모든 소스 코드는 자신의 특정 요구에 맞게 [패키지](https://pkg.kali.org/)를 수정하거나 재빌드하고 싶은 사람이라면 누구나 이용할 수 있어요.
- **파일 시스템 계층 표준을 준수해요:** 이 배포판은 [파일 시스템 계층 표준(FHS)](https://www.pathname.com/fhs/)을 따르기 때문에, 리눅스 사용자들은 바이너리, 지원 파일, 라이브러리 등을 쉽게 찾을 수 있어요.
- **다양한 장치를 폭넓게 지원해요:** 칼리는 USB 기반 장치를 포함해 다양한 하드웨어와 가능한 한 많은 무선 장치를 지원해요.
- **주입(패킷 인젝션)을 위해 패치된 맞춤 커널을 포함해요:** 침투 테스터로서, 개발팀은 종종 무선 평가를 수행해야 하므로, 우리 커널에는 최신 주입 패치가 포함되어 있어요.
- **안전한 환경에서 개발되었어요:** [칼리 리눅스 팀](https://kali.org/about-us/)은 소수의 개인으로 구성되어 있으며, 이들만이 패키지를 제출하고 저장소와 상호 작용할 수 있는 신뢰를 받고 있어요. 배포판에 대한 모든 변경은 여러 보안 프로토콜로 이루어져요.
- **GPG 서명된 패키지와 저장소를 가지고 있어요:** 칼리 리눅스의 모든 패키지는 그것을 빌드하고 제출한 개별 개발자에 의해 서명되며, 저장소도 추가로 패키지에 서명해요.
- **다국어 지원 기능을 제공해요:** 침투 도구들은 영어로 작성되는 경향이 있지만, 우리는 칼리가 진정한 다국어 지원을 포함하도록 보장했어요. 이를 통해 더 많은 사용자가 모국어로 작업하고 필요한 도구를 찾을 수 있어요.
- **완전히 맞춤 설정이 가능해요:** 우리는 모든 사람이 우리의 디자인 결정에 동의하지 않을 수 있다는 것을 충분히 이해하고 있어서, 모험을 좋아하는 사용자들이 커널까지 포함해 자신의 취향에 맞게 [칼리 리눅스를 맞춤 설정](/docs/development/live-build-a-custom-kali-iso/)하는 것을 가능한 한 쉽게 만들었어요.
- **ARMEL 및 ARMHF 지원:** [라즈베리 파이](/docs/arm/raspberry-pi/)와 [비글본 블랙](/docs/arm/beaglebone-black/) 같은 ARM 기반 싱글 보드 시스템이 점점 더 저렴해지고 침투 테스터들 사이에서 인기를 얻고 있어서, 우리 팀은 [칼리의 ARM 지원](/docs/introduction/kali-on-arm-a-bit-of-history/)이 가능한 한 강력해야 한다는 것을 깨달았어요. 이 배포판은 [ARMEL과 ARMHF](https://ko.wikipedia.org/wiki/ARM_%EC%95%84%ED%82%A4%ED%85%8D%EC%B2%98) 시스템 모두에 대해 완전히 작동하는 설치를 지원하며, [다양한 ARM 장치](/docs/arm/)에서 사용할 수 있어요. ARM 저장소는 메인 배포판에 통합되어 있어 ARM용 도구가 배포판의 나머지 부분과 함께 업데이트돼요.

{{% notice info %}}
**ARMEL과 ARMHF의 차이점**: 둘 다 ARM 프로세서 아키텍처를 지원하는 리눅스 배포판 용어입니다. ARMEL(ARM EABI Little-Endian)은 오래된 ARM 프로세서를 위한 것으로, 주로 소프트웨어로 부동 소수점 연산을 처리합니다. ARMHF(ARM Hard-Float)는 더 최신 ARM 프로세서를 위한 것으로, 하드웨어 부동 소수점 유닛을 사용해 더 빠른 성능을 제공합니다. 라즈베리 파이와 같은 최신 ARM 장치는 대부분 ARMHF를 사용합니다.
{{% /notice %}}

