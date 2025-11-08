---
title: 칼리에 VMware 설치하기 (호스트)
description:
icon:
weight: 100
author: ["g0tmi1k", "arszilla"]
번역: ["kmw0410"]
---

칼리 리눅스에서 VMware 워크스테이션 또는 플레이어를 설치할 수 있으며, 칼리 리눅스에서 가상 머신(VM)을 사용할 수 있습니다. 그러나 칼리 리눅스를 **가상 머신으로** 사용하려면 [칼리 리눅스 게스트 VMware](/docs/virtualization/install-vmware-guest-vm/)가 필요할 거에요.

VM은 훌륭하며 사용하는 이유는 여러 가지가 있어요. 첫번째로 여러 운영 체제를 동시에 실행할 수 있는 존재 중 하나에요. 호스트를 "손대지 않고" 게스트 VM과 상호 작용할 수 있어요. 또 다른 방법은 무언가가 제대로 진행되고 있을 때 스냅샷을 찍는 거에요. 문제가 생기면 되돌리세요.

VMware 워크스테이션과 & 퓨전은 상용 소프트웨어에요. (VMware 플레이어는 무료지만 기능이 제한적) 다양한 무료 또는 오픈소스 해결책(예: [VirtualBox](/docs/virtualization/install-virtualbox-host/), QEMU, 가상 관리자가 있는 KVM/Xen)이 있어요.

---

### 준비 사항

VMware을 설치를 시도하기 전에, 칼리 리눅스의 버전이 [최신](/docs/general-use/updating-kali/)인지 확인하세요. 필요한 경우 머신을 재부팅하세요:

```console
kali@kali:~$ sudo apt update
[...]
kali@kali:~$
kali@kali:~$ sudo apt full-upgrade -y
[...]
kali@kali:~$
kali@kali:~$ [ -f /var/run/reboot-required ] && sudo reboot -f
kali@kali:~$
```

---

### 다운로드

시작하려면 VMware 다운로드가 필요해요. [VMware 다운로드 페이지](https://www.vmware.com/uk/products/workstation-pro/)로 들어가세요. 이 글을 쓰는 시점을 기준으로, 최신 버전은 `15.5.1-15018445`에요.

또는, 명령어 방법을 사용할 수도 있어요:

```console
kali@kali:~$ sudo apt install -y curl
[...]
kali@kali:~$
kali@kali:~$ curl -A "Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0" \
  -o Downloads/vmware.bundle \
  -L https://www.vmware.com/go/getworkstation-linux
kali@kali:~$
kali@kali:~$ file Downloads/vmware.bundle
Downloads/vmware.bundle: a bash script executable (binary data)
kali@kali:~$
kali@kali:~$ ls -lah Downloads/vmware.bundle 
-rw-r--r-- 1 kali kali 514M Oct  3 02:13 Downloads/vmware.bundle
kali@kali:~$
```

모든 것이 최신 상태가 되면 실행할 준비가 됐어요. 파일을 실행할 수 있는지 확인 후 호출하세요:

```console
kali@kali:~$ chmod +x ~/Downloads/vmware.bundle
kali@kali:~$
kali@kali:~$ sudo ~/Downloads/vmware.bundle
Extracting VMware Installer...done.
Installing VMware Workstation 17.0.2
    Configuring...
[######################################################################] 100%
Installation was successful.
kali@kali:~$
```

이제 **VMware 워크스테이션 17.0.2**가 설치된 것을 볼 수 있어요. 버전 번호는 나중에 필요할 수도 있어요.

설치 프로그램이 설치되면 `vmware`을 실행하여 설정을 계속 할 수 있어요:

```console
kali@kali:~$ vmware
kali@kali:~$
```

이 단계에서는 일반적으로 클릭만 하면 진행할 수 있어요.

---

### 설정

첫 부분은 VMware 커널 모듈일 수 있어요.

![](vmware-01.png)

`vmware`을 슈퍼유저 권한을 실행하지 않았다면, 비밀번호를 입력하라는 메시지가 표시될 수도 있어요.

![](vmware-02.png)

이 단계에서 설치가 제대로 이루어지지 않을 수 있으며 다음과 같은 오류 메시지가 표시될 수 있어요: `Unable to install all modules. See log /tmp/vmware-kali/vmware-*.log for details. (Exit code 1)`. 이는 주로 칼리의 커널이 VMware가 예상하는 버전보다 최신인 경우에 발생해요.

로그를 확인하면 문제를 해결하는 데 도움을 줄 수 있고 **이 글에 마지막에 있는 가이드**, `vmware-host-modules`도 참고할 수 있어요.

법적 동의 수락이 필요해요.

![](vmware-03.png)

VMware에서 업데이트를 확인하도록 설정할 수 있어요.

![](vmware-04.png)

"VMware 고객 경험 개선 프로그램"에 가입할 수 있어요.

![](vmware-05.png)

현재 사용자명을 입력하세요.

![](vmware-06.png)

공유 VM의 위치를 입력하세요 (각 사용자 소유의 가상 머신마다 다름).

![](vmware-07.png)

HTTPS 접속을 위해 포트 번호를 입력하세요

![](vmware-08.png)

제품 키를 보유하고 있다면 입력하고, 아니라면 30일 평가판으로 제공돼요.
![](vmware-09.png)

설치 파일을 슈퍼유저 권한으로 실행하지 않았다면 다시 한 번 권한을 요청할 수 있어요.

![](vmware-10.png)

마지막 화면은 다음과 같은 모습이여야 해요.

![](vmware-11.png)

이제 원한다면, 칼리 리눅스에서 [VMware VM에 칼리 리눅스 설치](/docs/virtualization/install-vmware-guest-vm/)를 할 수 있어요.

---

### 문제 해결

#### libaio 누락

`vmware`을 실행할 때 다음과 같은 문제가 발생할 수 있어요.

[libaio1](https://packages.debian.org/testing/libaio1) 패키지 설치를 시도해 보세요:

```console
kali@kali:~$ vmware
[AppLoader] Use shipped Linux kernel AIO access library.
An up-to-date "libaio" or "libaio1" package from your system is preferred.
kali@kali:~$
kali@kali:~$ sudo apt install -y libaio1
[...]
kali@kali:~$
```

그 후 `vmware`을 다시 실행하면 문제가 해결될 거에요.

---

#### 누락된 패키지

때때로 일이 잘 풀리지 않을 수도 있어요. VMware가 설치되지 않는 데에는 여러가지 이유가 있을 수 있으며 가장 먼저 확인해봐야 할 것은 모든 패키지가 설치되어 있는지 확인해 보는 거에요:

```console
kali@kali:~$ sudo apt install -y build-essential linux-headers-$( uname -r ) vlan libaio1
[...]
kali@kali:~$
```

`vmware`을 다시 실행하고 설정이 계속 가능한지 확인하세요.

---

#### 커널 버전이 너무 최신인 경우

일반적인 문제는 VMware 설치 파일이 최신 커널을 지원하지 않기 때문이에요, 이는 칼리 리눅스가 [롤링 배포판](/docs/general-use/kali-branches/)으로 자주 업데이트를 받기 때문에 발생할 수 있어요. 이런 경우 VMware 모듈을 패치하여 문제를 해결할 수 있어요:

```console
kali@kali:~$ sudo apt install -y git
[...]
kali@kali:~$
kali@kali:~$ sudo git clone \
  -b workstation-$( grep player.product.version /etc/vmware/config | sed '/.*\"\(.*\)\".*/ s//\1/g' ) \
  https://github.com/mkubecek/vmware-host-modules.git \
  /opt/vmware-host-modules/
[...]
kali@kali:~$
kali@kali:~$ cd /opt/vmware-host-modules/
kali@kali:/opt/vmware-host-modules$ sudo make
kali@kali:/opt/vmware-host-modules$
kali@kali:/opt/vmware-host-modules$ grep -q pte_offset_map ./vmmon-only/include/pgtbl.h && \
  sudo sed -i 's/pte_offset_map/pte_offset_kernel/' ./vmmon-only/include/pgtbl.h
kali@kali:/opt/vmware-host-modules$
kali@kali:/opt/vmware-host-modules$ sudo make install
kali@kali:/opt/vmware-host-modules$
```

이제 `vmware`를 실행하여 VMware을 설치해 보세요.

계속 문제를 겪고 있다면, 칼리 리눅스를 재부팅 후 마지막으로 시도해 보세요.

---

#### vmware-host-modules + 커널 업데이트

VMware에는 다양한 커널 모듈이 포함되어 있으므로, 칼리 리눅스의 커널이 업데이트될 때마다 최신 상태로 유지하고 다시 패치해야 해요. 이는 [다음 가이드](https://docs.fedoraproject.org/en-US/quick-docs/how-to-use-vmware/)의 단계를 따르세요:

```console
kali@kali:~$ sudo tee /etc/kernel/install.d/99-vmmodules.install << EOF
#!/bin/bash

export LANG=C

COMMAND="\$1"
KERNEL_VERSION="\${2:-\$( /usr/bin/uname -r )}"
BOOT_DIR_ABS="\$3"
KERNEL_IMAGE="\$4"

VMWARE_VERSION=\$(/usr/bin/grep player.product.version /etc/vmware/config | /usr/bin/sed '/.*\"\(.*\)\".*/ s//\1/g')

ret=0

{
    [ -z "\${VMWARE_VERSION}" ] && exit 0

    /usr/bin/git clone -b workstation-"\${VMWARE_VERSION}" https://github.com/mkubecek/vmware-host-modules.git /opt/vmware-host-modules-"\${VMWARE_VERSION}"/
    cd /opt/vmware-host-modules-"\${VMWARE_VERSION}"/

    /usr/bin/make VM_UNAME="\${KERNEL_VERSION}"
    /usr/bin/make install VM_UNAME="\${KERNEL_VERSION}"

    ((ret+=\$?))

} || {
    echo "Unknown error occurred."
    ret=1

}

exit \${ret}
EOF
kali@kali:~$
```

---

#### VMware을 계속 시작할 수 없나요? vmware-modconfi

VMware가 제대로 작동하지 않으면, 다음 명령어를 사용하여 좀 더 자세히 살펴볼 수 있어요:

```console
kali@kali:~$ sudo vmware-modconfig --console --install-all
[...]
kali@kali:~$
kali@kali:~$ sudo vmware-modconfig --console --install-all 2>&1 | grep error
[...]
kali@kali:~$
```

출력을 확인하면 정확한 문제를 확인하거나 최소한 인터넷에서 검색할 단서를 얻을 수 있어요.

---

#### 가상 머신의 전원을 켤 수 없음

VM의 전원을 키려고 시도 할 때, 다음과 같은 문제가 발생할 수도 있어요.

- `Failed to initialize monitor device`
- `Could not open /dev/vmmon: No such file or directory. Please make sure that kernel module 'vmmon' is loaded`
- `Unable to change virtual machine power state: Transport (VMDB) error -14: Pipe connection has been broken.`

가장 빠른 해결책은 칼리 리눅스를 재부팅 후 다시 시도하는 것이에요.
