---
title: VMware 도구 설치하기 (게스트 도구)
description:
icon:
weight: 300
author: ["g0tmi1k",]
번역: ["xenix4845"]
---

"게스트 도구"를 설치하면 VMware VM에서 더 나은 사용자 경험을 제공해요. 이 때문에 [칼리 리눅스 2019.3](https://kali.org/blog/kali-linux-2019-3-release/)부터 [설정 과정](https://gitlab.com/kalilinux/build-scripts/live-build-config/-/blob/master/simple-cdd/profiles/offline.downloads) 중에 **칼리 리눅스가 VM 내부에 있는지 감지**해야 해요. 그렇다면 **추가 도구를 자동으로 설치**해요(VMware의 경우 `open-vm-tools`와 `open-vm-tools-desktop`). 게스트 도구는 [칼리 리눅스 2021.3](https://kali.org/blog/kali-linux-2021-3-release/)부터 라이브 이미지에도 사전 설치되어 있어요.

2015년 9월부터 **VMware는 게스트 머신용 VMware Tools 패키지 대신 배포판별 [open-vm-tools](https://packages.debian.org/testing/open-vm-tools) (Open-VM-Tools) 사용을 [권장](https://blogs.vmware.com/vsphere/2015/09/open-vm-tools-ovt-the-future-of-vmware-tools-for-linux.html)하고** 있어요.

## Open-VM-Tools

([미리 만들어진 VMware 이미지](/get-kali/#kali-virtual-machines)를 사용하는 대신) [칼리 리눅스의 자체 VMware 설치를 생성](/docs/virtualization/install-vmware-guest-vm/)하기로 결정했고, (뭔가 잘못되어서) `open-vm-tools`의 수동 재설치를 강제하고 싶다면, 먼저 [완전히 업데이트](/docs/general-use/updating-kali/)되었는지 확인한 다음 다음을 입력하세요:

```console
kali@kali:~$ sudo apt update
[...]
kali@kali:~$
kali@kali:~$ sudo apt install -y --reinstall open-vm-tools-desktop fuse
[...]
kali@kali:~$
kali@kali:~$ sudo reboot -f
kali@kali:~$
```

## Open-VM-Tools 사용 시 공유 폴더 지원 추가하기

안타깝게도 공유 폴더는 바로 작동하지 않을 거예요. 몇 가지 추가 스크립트가 필요해요. 이들은 `kali-tweaks`로 쉽게 설치할 수 있어요:

```console
kali@kali:~$ kali-tweaks
```

칼리 조정 메뉴에서 *가상화*를 선택한 다음 *VMware용 추가 패키지 및 스크립트 설치*를 선택하세요. 축하해요, 이제 도구 상자에 두 개의 추가 도구가 생겼어요!

첫 번째는 **VMware 공유 폴더를 마운트**하는 작은 스크립트예요. 다음으로 호출하세요:

```console
kali@kali:~$ sudo mount-shared-folders
```

운이 좋다면 `/mnt/hgfs/`를 확인했을 때 공유 폴더들을 볼 수 있을 거예요.

두 번째 스크립트는 **VM 도구를 다시 시작**하는 헬퍼예요. 실제로 OVT가 올바르게 작동하지 않는 경우가 드물지 않아요(예: 호스트 OS와 게스트 VM 간의 복사/붙여넣기가 작동하지 않는 경우). 이런 경우 이 스크립트를 실행하면 문제를 해결하는 데 도움이 될 수 있어요:

```console
kali@kali:~$ sudo restart-vm-tools
```

- - -

칼리 리눅스의 이전 버전에 대해서는 [이전 가이드](/docs/virtualization/install-vmware-guest-tools-legacy/)를 참조하세요.
