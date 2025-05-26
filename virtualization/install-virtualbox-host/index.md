---
title: 칼리에 VirtualBox 설치하기 (호스트)
description:
icon:
weight: 110
author: ["g0tmi1k", "Rclev4Sec", "gad3r"]
번역: ["xenix4845"]
---

칼리 리눅스 **에** VirtualBox를 설치하여 칼리 리눅스 내부에서 가상 머신을 사용할 수 있어요. 하지만 칼리 리눅스를 VM **으로** 설치하려고 한다면 [칼리 리눅스 게스트 VirtualBox](/docs/virtualization/install-virtualbox-guest-vm/) 가이드를 원할 거예요.

VM은 훌륭해요. 사용하는 데 많은 장점이 있어요. 그 중 하나는 여러 운영 체제를 동시에 실행할 수 있다는 것이에요. 호스트 머신을 "건드리지 않고" 게스트 VM과만 상호작용할 수 있어요. 또 다른 장점은 뭔가 잘 되고 있을 때 스냅샷을 찍고, 뭔가 잘못될 때 되돌릴 수 있다는 것이에요.

VirtualBox는 무료이고 오픈 소스예요. QEMU, virt-manager가 있는 KVM/Xen 같은 다른 소프트웨어들도 몇 가지 있어요. 그리고 상업용 소프트웨어인 [VMware Workstation & Fusion](/docs/virtualization/install-vmware-host/)도 있어요(VMware Player는 무료지만 기능이 제한적이에요).

VirtualBox는 kali-rolling 저장소에서 공식적으로 사용할 수 있어요.

`virtualbox`를 설치하는 두 가지 방법이 있어요:

1. 칼리 저장소에서
2. Oracle 저장소에서

### 준비

VirtualBox 설치를 시도하기 전에 칼리 리눅스 버전이 [최신 상태](/docs/general-use/updating-kali/)인지, [apt 소스가 적절히 설정](/docs/general-use/kali-linux-sources-list-repositories/#default-network-repository-value)되어 있는지 확인하고 필요하면 머신을 재부팅하세요:

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
### 1. 칼리 리눅스 저장소에서

`virtualbox`와 `linux-headers-generic`을 설치하세요:

```console
kali@kali:~$ sudo apt update
[...]
kali@kali:~$
kali@kali:~$ sudo apt install virtualbox linux-headers-generic
[...]
kali@kali:~$
```
확장 팩은 다음을 통해 설치할 수 있어요:

```console
kali@kali:~$ sudo apt install virtualbox-ext-pack
[...]
kali@kali:~$
```

### 2. Oracle VirtualBox 서드파티 저장소에서

## 다운로드

먼저 VirtualBox의 저장소 키를 가져올 거예요:

```console
kali@kali:~$ curl -fsSL https://www.virtualbox.org/download/oracle_vbox_2016.asc | sudo gpg --dearmor -o /etc/apt/trusted.gpg.d/oracle_vbox_2016.gpg
[...]
kali@kali:~$ curl -fsSL https://www.virtualbox.org/download/oracle_vbox.asc | sudo gpg --dearmor -o /etc/apt/trusted.gpg.d/oracle_vbox.gpg
[...]
kali@kali:~$
```

- - -

그다음 VirtualBox의 저장소를 추가해요.
[칼리 리눅스의 메인 저장소](/docs/general-use/kali-linux-sources-list-repositories/)와 간섭하지 않도록 별도의 파일에 추가해요. 또한 파일이 적절히 서명될 수 있도록 키링이 어디에 있는지 명시해야 해요.
우리의 CPU 아키텍처는 amd64예요. 여러분이 다르다면 아래 예시를 변경해야 할 수도 있어요.

명심해야 할 한 가지는 [칼리 리눅스가 데비안 기반](/docs/policy/kali-linux-relationship-with-debian/)이므로 ([칼리 리눅스가 롤링 배포판](/docs/general-use/kali-branches/)임에도 불구하고) [데비안의 현재 안정 버전](https://www.debian.org/releases/stable/)을 사용해야 한다는 것이에요. 작성 시점에는 "bullseye"예요:

```console
kali@kali:~$ echo "deb [arch=amd64] https://download.virtualbox.org/virtualbox/debian bullseye contrib" | sudo tee /etc/apt/sources.list.d/virtualbox.list
kali@kali:~$
```

- - -

네트워크 저장소를 변경했으므로 캐시를 재구축해야 해요:

```console
kali@kali:~$ sudo apt update
[...]
kali@kali:~$
```

- - -

VirtualBox는 다양한 커널 모듈(`vboxdrv`, `vboxnetflt`, `vboxnetadp` 등)을 가지고 있으므로, 칼리 리눅스의 커널이 업데이트될 때 이들이 최신 상태로 유지되도록 해야 해요. 이는 [dkms](https://packages.debian.org/testing/dkms)를 사용하여 달성할 수 있어요:

```console
kali@kali:~$ sudo apt install dkms
[...]
kali@kali:~$
```

### 설정

이제 VirtualBox 자체(VirtualBox의 고급 기능을 확장하는 확장 팩과 함께)와 필요한 리눅스 헤더를 설치할 시간이에요:

```console
kali@kali:~$ sudo apt install virtualbox virtualbox-ext-pack linux-headers-generic
[...]
kali@kali:~$
```

- - -

프롬프트가 나타나면 라이선스를 읽고 동의하세요.

이제 메뉴에서 VirtualBox를 찾거나 명령줄을 통해 시작할 수 있어요:

```console
kali@kali:~$ virtualbox
kali@kali:~$
```

원한다면 이제 (칼리 리눅스에서) [VirtualBox VM에 칼리 리눅스를 설치](/docs/virtualization/install-virtualbox-guest-vm/)할 수 있어요.
