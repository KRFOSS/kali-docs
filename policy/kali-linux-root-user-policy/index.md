---
title: Kali Linux 루트 사용자 정책
description:
icon:
archived: "true"
weight:
author: ["g0tmi1k",]
번역: ["xenix4845"]
---

**이 페이지는 구버전입니다.** 최신 버전은 여기에서 확인하실 수 있습니다: [사용자 정책](/docs/policy/kali-linux-user-policy/).

대부분의 리눅스 배포판은 시스템을 사용할 때 **관리자 권한(루트)이 아닌 일반 사용자 계정 — 즉 비관리자·비특권 계정**으로 로그인한 뒤, 필요할 때만 `sudo` 같은 도구를 이용해 권한을 상승시키도록 권장합니다. 이는 사용자와 잠재적으로 위험한 시스템 명령 사이에 안전망을 한 겹 더 두어 사고를 줄이는 보안 수칙입니다. 특히 여러 사람이 함께 쓰는 환경에서는 한 사용자의 실수나 악의적 행동이 다른 사용자의 작업을 망칠 수 있으므로 계정마다 권한을 분리하는 것이 필수입니다.

그러나 Kali Linux는 보안 점검·감사를 위한 플랫폼으로, 루트 권한이 있어야만 제대로 동작하는 도구가 다수 포함돼 있습니다. 또한 Kali Linux 특성상 여러 사용자가 동시에 쓰는 환경에서 운용될 가능성도 매우 낮습니다.

이 때문에 Kali Linux는 설치 직후 기본 계정을 **root**로 설정하며, 별도의 일반 사용자 계정을 자동으로 만들지 않습니다. 이러한 설계는 [Kali Linux가 리눅스 초보자에게 권장되지 않는 이유](/docs/introduction/should-i-use-kali-linux/) 중 하나이기도 합니다. 경험이 적은 사용자가 루트 권한으로 작업하다 실수로 시스템을 손상시킬 위험이 크기 때문입니다.
