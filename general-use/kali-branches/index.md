---
title: Kali 브랜치
description:
icon:
weight: 60
author: ["gamb1t", "g0tmi1k", "LindirQuenya",]
번역: ["xenix4845"]
---

## 브랜치란 무엇인가요?

브랜치는 소프트웨어의 대체 버전으로, 이 경우에는 Kali OS의 대체 버전이에요. Kali Linux는 여러 브랜치를 가지고 있어 사용자가 패키지의 업데이트 상태를 어떻게 유지할지 결정할 수 있어요. Kali Linux는 브랜치 사용을 포함하여 [여러 면에서 Debian과 유사](/docs/policy/kali-linux-relationship-with-debian/)해요.

한 번에 여러 브랜치를 활성화할 수 있어요. 그러나 브랜치를 전환하면 패키지가 다른 버전에 있거나 특정 상황에서 사용할 수 없거나 불안정할 수 있기 때문에 문제가 발생할 수 있어요.

브랜치를 전환하는 방법은 [네트워크 소스](/docs/general-use/kali-linux-sources-list-repositories/) 페이지를 참조하세요. 여러 브랜치 사용 예제는 [NVIDIA GPU 드라이버](/docs/general-use/install-nvidia-drivers-on-kali-linux/) 가이드를 확인하세요.

## Kali 브랜치

첫째로 *주요 브랜치*는 가장 자주 사용되고 가장 안정적인 브랜치예요. 이들은 종종 "안전"하다고 여겨져요.

- **kali-rolling**은 대부분이 사용해야 하는 기본 브랜치예요. 의심스러운 패키지가 안정적인지 확인한 후 `kali-dev`에서 가져오고 `kali-rolling-only`의 패키지와 결합하여 지속적으로 업데이트돼요. `debian-testing`의 버그로 인해 가끔 패키지 버그가 여기에 들어올 수 있어요.
- **kali-last-snapshot**은 사용자가 소프트웨어 제어에 대해 더 표준적인 느낌을 원할 때 사용할 수 있는 Kali의 브랜치예요. 새로운 릴리스마다 코드를 동결하고 `kali-rolling`을 `kali-last-snapshot`에 병합하는데, 이때 사용자는 [버전이 지정된 릴리스](/releases/) 간의 모든 업데이트를 받게 돼요(예: 2020.3 -> 2020.4). 패키지가 업데이트되지 않고(다음 릴리스까지 "포인트 릴리스"이므로) 릴리스 테스트를 거치기 때문에 더 안정적인 경우가 많아요. 이것이 "가장 안전한" 옵션이에요.

이 브랜치들 각각은 Kali OS의 완전한 배포판을 제공해요. 둘 중 하나만 사용해야 하며, 둘 다 동시에 활성화하는 것은 의미가 없어요.

다음은 *부분 브랜치*로, `kali-rolling` 브랜치에 *추가*하여 사용하도록 설계되었어요. 일반적으로 kali-rolling에서 찾을 수 있는 패키지의 최신 버전을 제공하며, 때로는 추가 패키지도 제공할 수 있어요. 이 브랜치들은 Kali의 완전한 배포판을 포함하지 않으므로 단독으로 사용할 수 없어요.

일반 사용자는 매우 특별한 경우를 제외하고는 이러한 브랜치가 필요하지 않을 가능성이 높다는 점에 유의하세요. 부분 브랜치는 다음과 같아요:

- **kali-experimental**은 개발 중인 패키지를 위한 준비 영역이에요.
- **kali-bleeding-edge**는 [업스트림 git 저장소에서 자동으로 업데이트](/blog/bleeding-edge-kali-repositories/)되는 패키지를 포함해요. 이 브랜치는 매우 불안정할 가능성이 있어요.

### 개발

- **kali-dev**는 Kali Linux의 개발 버전이에요. `kali-dev-only`, `kali-debian-picks` 및 `debian-testing` 세 가지 다른 브랜치를 결합하여 생성돼요. 주로 Debian의 업데이트와 Kali Linux에서 유지 관리하는 변경 사항을 병합하는 데 사용돼요.
- **kali-dev-only**는 Kali 특화 패키지가 있는 개발 배포판이에요. 이 브랜치는 자동으로 `kali-dev`에 병합돼요.
- **kali-rolling-only**는 `kali-rolling`에 빠르게 도달해야 하는 패키지를 위한 저장소예요.

### 다른 브랜치 지원에 사용되는 브랜치

- **kali-debian-picks**는 `debian-experimental`과 `debian-unstable`에서 선별한 패키지를 포함해요. 자동으로 `kali-dev`에 병합돼요.
- **[debian-testing](https://wiki.debian.org/DebianTesting)**은 Debian의 테스팅 배포판 미러예요. 이것은 `kali-dev`를 구축하는 데 사용돼요.
- **[debian-experimental](https://wiki.debian.org/DebianExperimental)**과 **[debian-unstable](https://wiki.debian.org/DebianUnstable)**은 선별하고 싶은 특정 패키지를 위한 부분 미러예요.

## 매핑

아래는 브랜치 간의 관계를 보여주는 다이어그램이에요:

```plaintext
debian-experimental -> debian-unstable -> debian-testing -> kali-dev -> kali-rolling -> kali-last-snapshot
      |                      |                              ^   ^         ^       |
      v                      v                              |   |         |       |
      ---------------------------------> kali-debian-picks -|   |         |       ----> kali-bleeding-edge
                                                                |         |                    ^
kali-experimental -> kali-dev-only -----------------------------|         |                    |
                                                                          |                 Upstream
kali-rolling-only --------------------------------------------------------|
```

## Debian과의 관계

[Debian](https://www.debian.org/releases/)에는 세 가지 주요 옵션이 있어요:

- [Stable](https://www.debian.org/releases/stable/)
- [Testing](https://www.debian.org/releases/testing/)
- [Unstable](https://www.debian.org/releases/unstable/)

**Stable**은 "안전한" Debian 브랜치예요. 약 두 달마다 "[포인트 릴리스](https://wiki.debian.org/DebianReleases/PointReleases)"로 업데이트되는데, 이는 주로 보안 업데이트일 뿐이에요. 패키지는 잠재적인 비호환성과 불안정성 때문에 이 기간 동안 일반적으로 버전 업그레이드를 받지 않아요. 이것은 **kali-last-snapshot**의 Debian 동등물이에요.

**Testing**은 Debian "롤링" 배포판과 가장 가까운 것으로, "롤링"은 패키지 업데이트가 사용 가능해지는 즉시 배포된다는 것을 의미해요. Kali는 2016년 1월부터 이 브랜치를 **kali-rolling**의 시작점으로 사용하고 있어요.

**Unstable**은 Debian 패키지 개발 직후에 있어요. 패키지가 생성되었지만 완전히 테스트되지 않았어요. Kali는 롤링 배포판이기 때문에 이에 해당하는 것이 없어요.

Kali가 Debian과 어떻게 관련되는지에 대한 자세한 내용은 해당 주제에 관한 [정책 페이지](/docs/policy/kali-linux-relationship-with-debian/)를 참조하세요.

### kali-rolling 저장소

kali-dev와 달리, kali-rolling은 포함된 모든 패키지의 설치 가능성을 보장하는 도구로 관리되기 때문에 품질이 더 좋을 것으로 예상돼요. 이 도구는 kali-dev에서 업데이트된 패키지를 선택하고 설치 가능하다고 확인된 경우에만 kali-rolling에 복사해요. 그러나 이러한 검사에는 기능 테스트가 포함되지 않는다는 점에 유의하세요. 패키지 종속성으로 해결되지 않는 다른 문제로 인해 여전히 손상된 소프트웨어가 포함될 수 있어요. **Kali Rolling은 대부분의 사용자가 사용해야 하는 기본 저장소예요**. 또한 Kali 특화 패키지에 대한 문제를 [bugs.kali.org](https://bugs.kali.org/)에 보고할 수 있어요. "Product version"에서 "kali-dev" 버전을 선택해야 해요.

Kali Rolling 사용자는 [/etc/apt/sources.list](/docs/general-use/kali-linux-sources-list-repositories/)에 다음 항목이 있어야 해요:

```plaintext
deb http://http.kali.org/kali kali-rolling main contrib non-free non-free-firmware
```

### kali-dev 저장소

**경고: kali-dev는 모든 Kali 미러에서 누구에게나 공개적으로 접근할 수 있지만, 이 배포판은 정기적으로 손상되기 때문에 최종 사용자가 사용해서는 안 돼요**.

이 저장소는 실제로 Debian의 Testing 배포판에 kali-dev-only 저장소에서 사용할 수 있는 모든 kali 특화 패키지가 강제로 주입된 것이에요. Kali 패키지는 Debian 패키지보다 우선시돼요. Testing이 변경될 때 일부 Kali 패키지를 업데이트해야 하는 경우가 있지만, 이것이 즉시 이루어지지 않을 수 있어요. 이 기간 동안 kali-dev가 손상될 가능성이 높아요. 이 저장소는 Kali 개발자가 업데이트된 패키지를 푸시하는 곳이며 kali-rolling을 만드는 데 사용되는 기반이에요.
