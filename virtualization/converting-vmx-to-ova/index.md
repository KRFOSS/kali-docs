---
title: VMX에서 OVA로 변환하기
description:
icon:
weight: 400
author: ["gamb1t",]
번역: ["kmw0410"]
---

VMware에는 VMware 제품들에서 사용하는 **VMX 포맷**이 있습니다. 일반적으로 사용되는 포맷은 **OVF**입니다, 이는 개방향 표준이므로 (**OVA**는 OVF로 이지미나 단일 파일로 압축됨) 두 형식 간에 변환이 필요한 경우가 있습니다.

VMware의 VMX에서 OVA로 변환하려면 다음과 같이 하세요, 우리는 [ovftool](https://code.vmware.com/web/tool/4.4.0/ovf)를 사용할 것입니다. 이미 [VMware workstation](/docs/virtualization/install-vmware-host/) 이나 VMware fusion 이 사전 설치 되어있는 경우, 이미 가지고 있을지도 모릅니다 (또는 무료로 독립 실행형 프로그램을 다운로드할 수 있습니다). 기본 위치는 다음과 같습니다:

- Linux: `/usr/bin/ovftool`
- OS X/macOS: `/Applications/VMware Fusion.app/Contents/Library/VMware OVF Tool/ovftool`
- Windows: `C:\Program Files (x86)\VMware\VMware Workstation\OVFTool\ovftool.exe`

- - -

우리는 [공식 Kali Linux VMware 이미지](/get-kali/#kali-virtual-machines)를 변환하는 데 사용할 것입니다. 시작하려면 압축을 풀고 그 안에 있는 vmx 파일에 접근하세요:

```console
kali@kali:~$ 7z x kali-linux-2025.1-vmware-amd64.7z
[...]
kali@kali:~$
kali@kali:~$ ls kali-linux-*-vmware-amd64.vmwarevm/*vmx
kali-linux-2025.1-vmware-amd64.vmwarevm/kali-linux-2025.1-vmware-amd64.vmx
kali@kali:~$
```

- - -

다음을 따라 변환을 시작할 수 있습니다:

```console
kali@kali:~$ ovftool kali-linux-*-vmware-amd64.vmwarevm/*vmx kali-linux-rolling-amd64.ova
Opening VMX source: kali-linux-2025.1-vmware-amd64.vmwarevm/kali-linux-2025.1-vmware-amd64.vmx
Opening OVA target: kali-linux-rolling-amd64.ova
Writing OVA package: kali-linux-rolling-amd64.ova
[...]
Transfer Completed
Completed successfully
kali@kali:~$
kali@kali:~$ file kali-linux-rolloing.ova
kali-linux-rolloing.ova: POSIX tar archive
kali@kali:~$ ls -lah kali-linux-rolloing.ova
-rw-r--r-- 1 kali kali 3.4G Nov 10 23:18 kali-linux-rolloing.ova
kali@kali:~$
```

- - -

그게 다입니다!

OVA 파일은 필요에 따라 ESXi로 옮겨서 사용하거나 _(내장 업로드 기능이 작동하지 않는 경우)_ VirtualBox로 마이그레이션하세요 _(필요한 경우 [게스트 추가 기능](/docs/virtualization/install-virtualbox-guest-additions/)을 설치해야 할 수도 있습니다)_.
