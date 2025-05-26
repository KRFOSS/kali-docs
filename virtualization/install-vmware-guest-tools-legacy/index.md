---
title: 칼리 게스트용 VMware 도구 (레거시)
description:
icon:
archived: "true"
weight: 300
author: ["g0tmi1k",]
번역: ["xenix4845"]
---

**이 페이지는 구버전이에요**. **최신 버전**은 여기에서 찾을 수 있어요: [VMware 게스트 도구 설치하기](/docs/virtualization/install-vmware-guest-tools/).

- - -

2015년 9월부터 **VMware는 게스트 머신용 VMware Tools 패키지 대신 (이 가이드처럼) 배포판별 [open-vm-tools](https://packages.debian.org/testing/open-vm-tools) (OVT) 사용을 [권장](https://blogs.vmware.com/vsphere/2015/09/open-vm-tools-ovt-the-future-of-vmware-tools-for-linux.html)하고** 있어요.

이 날짜 기준 최신 버전의 VMwareTools는 여러 경고와 함께 [우리 커널](https://pkg.kali.org/pkg/linux)에 대해 컴파일돼요. 설치를 용이하게 하기 위해 VMwareTools 패치 세트를 활용해요.

## 준비하기

먼저 필요한 패키지를 설치하고 Git 저장소의 로컬 복사본을 만들어야 해요:

```console
kali@kali:~$ sudo apt update
[...]
kali@kali:~$
kali@kali:~$ sudo apt install -y git gcc make linux-headers-$( uname -r )
[...]
kali@kali:~$
kali@kali:~$ sudo git clone https://github.com/rasa/vmware-tools-patches.git /opt/vmware-tools-patches/
[...]
kali@kali:~$
```

## 패치 적용 및 설치하기

다음으로 VMware 창의 적절한 메뉴에서 "**VMware Tools 설치**"를 클릭하여 VMware 도구 ISO를 마운트하세요.

VMware Tools ISO가 가상 머신에 연결되면 설치 프로그램을 **downloads** 디렉토리에 복사한 다음 Git 저장소에서 설치 프로그램 스크립트를 실행하세요:

```console
kali@kali:~$ sudo mkdir -p /media/cdrom/
kali@kali:~$
kali@kali:~$ sudo mount /dev/cdrom /media/cdrom/
kali@kali:~$
kali@kali:~$ cp -f /media/cdrom/VMwareTools-*.tar.gz ~/downloads/
kali@kali:~$
kali@kali:~$ cd /opt/vmware-tools-patches/
kali@kali:/opt/vmware-tools-patches$ ./untar-and-patch-and-compile.sh
kali@kali:/opt/vmware-tools-patches$
```

그 후 칼리 리눅스를 재부팅하면 다시 시작될 때 VMware Tools가 작동하고 있을 거예요.
