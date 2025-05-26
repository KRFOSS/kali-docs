---
title: VirtualBox 게스트 확장 설치하기 (게스트 도구)
description:
icon:
weight: 310
author: ["g0tmi1k",]
번역: ["xenix4845"]
---

"게스트 확장"을 설치하면 VirtualBox VM에서 더 나은 사용자 경험을 제공해요 _(예: 적절한 마우스 및 화면 통합과 폴더 공유)_. 이 때문에 칼리 리눅스 2019.3부터 [설정 과정](https://gitlab.com/kalilinux/build-scripts/live-build-config/-/blob/master/simple-cdd/profiles/offline.downloads) 중에 **칼리 리눅스가 VM 내부에 있는지 감지**해야 해요. 그렇다면 **추가 도구를 자동으로 설치**해요(VirtualBox의 경우 `virtualbox-guest-x11`). 게스트 확장은 칼리 리눅스 2021.3부터 라이브 이미지에도 사전 설치되어 있어요.

호환성 업데이트와 핵심 애플리케이션 및 게스트 확장의 향상된 안정성을 포함한 개선사항을 활용하려면 **VirtualBox 4.2.xx 이상**을 사용해야 해요.

## virtualbox-guest-x11

([미리 만들어진 VirtualBox 이미지](/get-kali/#kali-virtual-machines)를 사용하는 대신) [칼리 리눅스의 자체 VirtualBox 설치를 생성](/docs/virtualization/install-virtualbox-guest-vm/)하기로 결정했고, (뭔가 잘못되어서) `virtualbox-guest-x11`의 수동 재설치를 강제하고 싶다면, 먼저 [완전히 업데이트](/docs/general-use/updating-kali/)되었는지 확인한 다음 다음을 입력하세요:

```console
kali@kali:~$ sudo apt update
[...]
kali@kali:~$
kali@kali:~$ sudo apt install -y --reinstall virtualbox-guest-x11
[...]
kali@kali:~$
kali@kali:~$ sudo reboot -f
kali@kali:~$
```

- - -

칼리 리눅스의 이전 버전에 대해서는 [이전 가이드](/docs/virtualization/install-virtualbox-guest-additions-legacy/)를 참조하세요.
