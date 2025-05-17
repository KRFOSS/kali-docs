---
title: 데스크톱 환경 전환하기
description:
icon:
weight:
author: ["gamb1t",]
번역: ["xenix4845"]
---

설치 중에 사용자는 원하는 데스크톱 환경을 선택할 수 있어요. 그러나 공식 VM을 사용할 때는 이것이 불가능해요. 이러한 경우나 다른 여러 상황에서 사용자는 데스크톱 환경을 변경하고 싶을 수 있어요.

시작하기 위해 먼저 시스템을 업데이트하고 해당 DE에 대한 `kali-desktop-*` [메타패키지](/docs/general-use/metapackages/)를 설치한 다음, 기본 x-session-manager를 앞으로 사용할 것으로 업데이트할 거에요. KDE를 설치할 때 어떤 로그인 관리자를 사용할지 물어볼 거에요. KDE가 Xfce와 상호작용하는 방식 때문에 KDE를 대체해야 하므로 "sddm"을 선택할 거에요:

```console
kali@kali:~$ sudo apt update
kali@kali:~$
kali@kali:~$ sudo apt install -y kali-desktop-kde
kali@kali:~$
kali@kali:~$ sudo update-alternatives --config x-session-manager
kali@kali:~$
```

KDE를 설치하기로 선택하면, 몇 가지 발생할 수 있는 충돌을 기억해야 해요. 다른 DE와 함께 KDE를 설치할 _수는_ 있지만, 현재 패키지가 구성된 방식으로 인해 몇 가지 구성 충돌이 있을 거에요. 예를 들어, KDE와 Xfce가 모두 설치되어 있으면 Xfce에서 커서를 선택할 수 없게 돼요.

이 문제를 해결하기 위해 Xfce를 제거하고 KDE만 설치할 거에요. 또한 KDE와 함께 다른 DE를 설치하는 것은 권장하지 않아요. 이는 KDE에만 적용된다는 점을 기억하세요. Xfce와 GNOME은 충돌 없이 동시에 설치할 수 있어요:

`kali-desktop-*` 패키지는 _시스템 보호_ 표시가 되어 있으므로, 이를 제거하기 위해서는 apt 매개변수 `--allow-remove-essential`을 추가해야 해요.

```console
kali@kali:~$ sudo apt purge --autoremove --allow-remove-essential kali-desktop-xfce
kali@kali:~$
```

이제 시스템을 재부팅하고 모든 변경 사항이 제대로 적용되었는지 확인할 거에요.
