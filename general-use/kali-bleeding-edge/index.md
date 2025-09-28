---
title: Kali Bleeding Edge로 최신 출시되지 않은 기능과 버그 수정 받기
description:
icon:
weight: 62
author: ["rhertzog",]
번역: ["xenix4845"]
---

## 소개

`kali-bleeding-edge`는 APT 구성에서 활성화할 수 있는 [Kali 저장소](/docs/general-use/kali-branches/)의 이름으로, 업스트림 git 저장소에 있는 최신 버전의 소스 코드로 빌드된 패키지에 접근할 수 있게 해줘요.

## 사용 사례

### 특정 도구의 최신 버전 보장

저장소에 300개 이상의 도구가 있어 모든 도구를 항상 최신 git 버전으로 유지하는 것은 거의 불가능해요 - 공식 릴리스만으로도 이미 힘든 일이니까요. 하지만 정말 최신 버전이 필요한 경우가 있어요. 이런 상황을 생각해보세요:

내일 보안 감사가 있고, 도구들을 준비하고 있어요. 칼리 리눅스를 부팅하고, `sudo apt update && sudo apt full-upgrade`를 실행하여 *$your_favorite_tool*의 최신 안정 버전을 실행하고 있는지 확인해요. 업데이트 후, 그것이 지난주 버전으로 업데이트되었다는 것을 알게 돼요. 그러나 지난주에 *$your_favorite_tool*의 git 저장소에 정말 멋진 업데이트가 추가되었고, 다가오는 평가를 위해 이러한 새로운 기능이 정말 필요해요. 어떻게 해야 할까요?

홈 디렉토리에서 업스트림 저장소를 `git clone`하고 거기서 작업할 수 있지만, 그러면 Kali와의 통합이 손실돼요. 또한 의존성도 수동으로 처리해야 해요. 아니요, 가장 스마트한 해결책은 업스트림 git 저장소를 지속적으로 처리해 kali-bleeding-edge 저장소를 채우는 우리의 Kali Bot의 끊임없는 작업에 의존하는 거예요.

### 업스트림 작성자가 커밋한 버그 수정 확인

*$your_favorite_tool*을 열심히 사용하는 중에 버그를 발견했어요. 좋은 오픈 소스 시민으로서, GitHub에서 업스트림 프로젝트를 확인했고, 버그가 아직 보고되지 않았음을 확인한 후 문제를 재현하는 방법에 대한 상세한 보고서를 보냈어요.

이 덕분에 업스트림 개발자가 문제를 식별하고, 가능한 수정을 커밋한 후 최신 git 버전으로 문제가 해결되었는지 확인해달라고 요청했어요.

당신은 이 도구와 오픈소스 철학을 좋아하지만, 개발자가 아니어서 이를 위해 git 버전의 도구를 빌드하고 싶지는 않아요.

다행히도, kali-bleeding-edge 저장소를 기억하고 [pkg.kali.org](https://pkg.kali.org/)에서 빠르게 확인한 결과 Kali 봇이 이미 업데이트된 패키지를 준비했다는 것을 알게 됐어요. 신속하게 설치하고 업스트림 개발자에게 원하는 확인(또는 추가 정보)으로 답변할 수 있어요.

## 주의: 출혈에 주의하세요

"Kali **bleeding edge**"라고 불리는 이유가 있어요. 가련한 Kali 봇이 최선을 다하지만, 그 AI는 새로운 의존성, 이전 버전과 호환되지 않는 변경 사항 및 업스트림 개발자가 README에 남긴 기타 지침을 감지할 만큼 항상 똑똑하지는 않아요. 그래서 일반적으로 패키지를 빌드할 수는 있지만, 업데이트된 패키지가 작동한다는 보장은 없어요.

일부 업스트림 개발자들은 리팩토링이나 기타 주요 변경 작업을 하는 동안 의도적으로 도구를 손상시키기도 해요. 그들은 사용자들에게 공식 릴리스만 사용하도록 권장해요. 이런 경우, kali-bleeding-edge 패키지는 무용지물이거나 적극적으로 해를 끼칠 수 있어요(미완성 코드가 도구에서 관리하는 일부 데이터를 손상시킬 수 있어요).

따라서 패키지를 kali-bleeding-edge 버전으로 업그레이드하기로 결정하기 전에 업스트림 git 저장소에 도착한 변경 사항을 살펴보는 것이 좋아요.

## 저장소 활성화 방법

좋아요. 경고를 받았고 kali-bleeding-edge를 합리적인 방식으로 사용하겠다고 약속했어요. 이 저장소는 어떻게 활성화하나요? 간단해요:

```console
kali@kali:~$ sudo tee /etc/apt/sources.list.d/kali-bleeding-edge.list <<EOF
deb http://http.kali.org/kali kali-bleeding-edge main contrib non-free non-free-firmware
EOF
kali@kali:~$ sudo apt update
[...]
```

나중에 저장소를 비활성화하고 싶다면, 방금 생성한 파일을 제거하는 것만큼 간단해요:

```console
kali@kali:~$ sudo rm -f /etc/apt/sources.list.d/kali-bleeding-edge.list
```

## kali-bleeding-edge에서 패키지 설치

저장소가 활성화되면, `apt install <package>/kali-bleeding-edge`로 kali-bleeding-edge에서 패키지를 설치하는 것은 간단해요. 여기 gitleaks의 예가 있어요:

```console
kali@kali:~$ sudo apt install gitleaks/kali-bleeding-edge
Reading package lists... Done
Building dependency tree       
Reading state information... Done
Selected version '7.4.0+git20210412.1.6f5ad9d-0kali1~jan+nus1' (http.kali.org [amd64]) for 'gitleaks'
The following packages will be upgraded:
  gitleaks
1 upgraded, 0 newly installed, 0 to remove and 283 not upgraded.
Need to get 2504 kB of archives.
After this operation, 0 B of additional disk space will be used.
Get:1 http://kali.download/kali kali-bleeding-edge/main amd64 gitleaks amd64 7.4.0+git20210412.1.6f5ad9d-0kali1~jan+nus1 [2504 kB]
Fetched 2504 kB in 2s (1257 kB/s)   
(Reading database ... 106991 files and directories currently installed.)
Preparing to unpack .../gitleaks_7.4.0+git20210412.1.6f5ad9d-0kali1~jan+nus1_amd64.deb ...
Unpacking gitleaks (7.4.0+git20210412.1.6f5ad9d-0kali1~jan+nus1) over (7.4.0-0kali1) ...
Setting up gitleaks (7.4.0+git20210412.1.6f5ad9d-0kali1~jan+nus1) ...
Processing triggers for kali-menu (2021.1.4) ...
```

버전 체계가 약간 특이하다는 점을 주목하세요. 마지막 업스트림 릴리스 버전으로 시작하고 `+git<date>.<number>.<commit>`이 뒤따라요(여기서 `<date>`는 마지막 업스트림 커밋 날짜, `<number>`는 같은 날 여러 버전을 위한 단순 증분, `<commit>`은 짧은 커밋 식별자). Debian 수정 버전에는 `~jan+nus<X>`가 포함되어 있어요. 이는 [Janitor Bot](https://salsa.debian.org/jelmer/debian-janitor)(예전에 **칼리 봇**을 구동하던)에 의해 생성된 "새 업스트림 스냅샷"(`nus`)임을 나타내요. 물결표(~)는 이것이 동일한 버전의 후속 수동 릴리스보다 낮게 정렬되도록 해요.

kali-bleeding-edge에서 패키지를 설치하면, kali-bleeding-edge 버전이 kali-rolling 버전보다 새로운 한 `apt`가 계속해서 이 저장소에서 해당 패키지 업데이트를 가져온다는 점을 알아두세요.

## 안정적인 버전으로 돌아가기

패키지의 안정적인 버전으로 돌아가고 싶다면, 같은 방식으로 작동하지만, 대상 배포판을 kali-rolling으로 설정해야 해요:

```console
kali@kali:~$ $ sudo apt install gitleaks/kali-rolling
Reading package lists... Done
Building dependency tree       
Reading state information... Done
Selected version '7.4.0-0kali1' (kali-rolling [amd64]) for 'gitleaks'
The following packages will be DOWNGRADED:
  gitleaks
0 upgraded, 0 newly installed, 1 downgraded, 0 to remove and 282 not upgraded.
Need to get 2504 kB of archives.
After this operation, 0 B of additional disk space will be used.
Do you want to continue? [Y/n] 
Get:1 http://kali.download/kali kali-rolling/main amd64 gitleaks amd64 7.4.0-0kali1 [2504 kB]
Fetched 2504 kB in 2s (1060 kB/s)   
dpkg: warning: downgrading gitleaks from 7.4.0+git20210412.1.6f5ad9d-0kali1~jan+nus1 to 7.4.0-0kali1
(Reading database ... 106747 files and directories currently installed.)
Preparing to unpack .../gitleaks_7.4.0-0kali1_amd64.deb ...
Unpacking gitleaks (7.4.0-0kali1) over (7.4.0+git20210412.1.6f5ad9d-0kali1~jan+nus1) ...
Setting up gitleaks (7.4.0-0kali1) ...
Processing triggers for kali-menu (2021.1.4) ...
```
