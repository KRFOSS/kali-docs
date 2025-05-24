---
title: 베이그런트 안의 칼리 (게스트 VM)
description:
icon:
weight: 200
author: ["gamb1t",]
번역: ["kmw0410"]
---

베이그런트는 그들의 [웹사이트](https://www.vagrantup.com/)를 따르면, "단일 워크플로우에서 가상 머신 환경을 구축하고 관리하기 위한 도구"에요. 이 모든 것은 가상 머신(VM)을 사용자의 필요에 맞게 조정할 수 있는 많은 구성 옵션이 포함된 다일 구성 파일을 통해 제어됩니다.

#### 시스템 준비

베이그런트를 제대로 사용하려면 두 가지가 필요해요. 베이그런트 자체와 지원되는 하이퍼바이저에요. 칼리 리눅스용 베이그런트 파일은 버추얼 박스와 VMware, 두 가지를 지원해요.

먼저 [베이그런트](https://www.vagrantup.com/downloads)를 다운로드하세요.

윈도우를 사용 중인 경우, 이전 링크를 따라가 적절한 버전을 다운로드 해야해요. (설정을 열고 정보로 들어가서 64비트를 사용 중인 경우 amd64를, 686을 사용 중인 경우 32비트를 다운로드하세요) 이 방법은 macOS에서도 동일하게 작동해요. 베이그런트를 다운로드하고 설치를 완료하세요.

칼리 리눅스와 같은 데비안 기반을 사용 중이라면, `베이그런트` 패키지를 다운로드할 수 있어요:

```console
kali@kali:~$ sudo apt search vagrant
Sorting... Done
Full Text Search... Done
[...]
vagrant/kali-dev,kali-dev,kali-rolling,kali-rolling,now 2.2.19+dfsg-1 all [installed]
  Tool for building and distributing virtualized development environments

vagrant-cachier/kali-dev,kali-dev,kali-rolling,kali-rolling 1.2.1-3.1 all
  share a common package cache among similar VM instances

vagrant-hostmanager/kali-dev,kali-dev,kali-rolling,kali-rolling 1.8.9-1.1 all
  Vagrant plugin for managing /etc/hosts on guests and host

vagrant-libvirt/kali-dev,kali-dev,kali-rolling,kali-rolling,now 0.8.0-1 all [installed,automatic]
  Vagrant plugin that adds an Libvirt provider to Vagrant

vagrant-lxc/kali-dev,kali-dev,kali-rolling,kali-rolling 1.4.3-2 all
  Linux Containers provider for Vagrant

vagrant-mutate/kali-dev,kali-dev,kali-rolling,kali-rolling 1.2.0-4.1 all
  convert vagrant boxes to work with different providers

vagrant-sshfs/kali-dev,kali-dev,kali-rolling,kali-rolling 1.3.6-1 all
  vagrant plugin that adds synced folder support with sshfs
kali@kali:~$
```

그렇지 않으면, 베이그런트의 다운로드 페이지에 있는 지침을 따라야 해요.

다음으로 하이퍼바이저를 다운로드 해야해요. 이 가이드의 목적을 위해 무료 [버추얼 박스](https://www.virtualbox.org/wiki/Downloads)를 다운로드 할게요. 윈도우나 macOS를 사용하는 경우라면 다운로드 링크를 클릭하고 설정을 완료할 수 있어요. 그렇지 않은 경우 [리눅스 호스트](https://www.virtualbox.org/wiki/Linux_Downloads) 페이지에서 배포판을 찾을 수 있어요. 칼리 리눅스를 사용하는 경우에는 이미 [문서](/virtualization/install-virtualbox-host/)를 참고할 수 있어요.

#### 베이그런트 사용하기

이제 하이퍼바이저와 베이그런트가 설치되었어요, 첫 번째 구성 파일을 가져올게요.

먼저 빈 폴더/디렉토리에서 명령행을 실행해야 해요. 이 가이드에서는 칼리 리눅스 호스트 시스템을 사용할 예정이지만, `vagrant`로 시작하는 명령어들은 어떤 호스트를 사용하든 동일해요:

```console
kali@kali:~/vagrant$ vagrant init kalilinux/rolling
A `Vagrantfile` has been placed in this directory. You are now
ready to `vagrant up` your first virtual environment! Please read
the comments in the Vagrantfile as well as documentation on
`vagrantup.com` for more information on using Vagrant.

kali@kali:~/vagrant$
kali@kali:~/vagrant$ cat Vagrantfile | grep -v '#'

Vagrant.configure("2") do |config|

  config.vm.box = "kalilinux/rolling"

end

kali@kali:~/vagrant$
```

매우 간단한 구성 파일처럼 보이지만, 이것으로 최신 버전의 칼리 리눅스 VM을 시작할 수 있으며 다운로드와 시작 후에는 약 10GB의 공간을 차지하게 돼요.

머신을 시작하고, 명령어를 따라서 실행하세요:

```console
kali@kali:~/vagrant$ vagrant up
Bringing machine 'default' up with 'virtualbox' provider...
==> default: Box 'kalilinux/rolling' could not be found. Attempting to find and install...
    default: Box Provider: virtualbox
    default: Box Version: >= 0
==> default: Loading metadata for box 'kalilinux/rolling'
    default: URL: https://vagrantcloud.com/kalilinux/rolling
==> default: Adding box 'kalilinux/rolling' (v2025.1.1) for provider: virtualbox
    default: Downloading: https://vagrantcloud.com/kalilinux/boxes/rolling/versions/2025.1.1/providers/virtualbox.box
==> default: Successfully added box 'kalilinux/rolling' (v2025.1.1) for 'virtualbox'!
[...]
==> default: Machine booted and ready!
==> default: Checking for guest additions in VM...
==> default: Mounting shared folders...
    default: /vagrant => /home/morales/vagrant

kali@kali:~/vagrant$

kali@kali:~/vagrant$ vagrant ssh
Linux kali 5.16.0-kali7-amd64 #1 SMP PREEMPT Debian 5.16.18-1kali1 (2022-04-01) x86_64

The programs included with the Kali GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Kali GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
kali@kali:~$
kali@kali:~$ exit

kali@kali:~/vagrant$

kali@kali:~/vagrant$ vagrant halt
==> default: Attempting graceful shutdown of VM...

kali@kali:~/vagrant$
```

구성 파일 조정을 원할 경우 다음과 같이 하면 돼요:

```plaintext
# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.box = "kalilinux/rolling"

  # Create a forwarded port
  config.vm.network "forwarded_port", guest: 80, host: 8080

  # Create a private network. In VirtualBox, this is a Host-Only network
  config.vm.network "private_network", ip: "192.168.33.10"

  # VirtualBox specific settings
  config.vm.provider "virtualbox" do |vb|
    # Hide the VirtualBox GUI when booting the machine
    vb.gui = false

    # Customize the amount of memory on the VM:
    vb.memory = "4096"
  end

  # Provision the machine with a shell script
  config.vm.provision "shell", inline: <<-EOF
    sudo apt update
    sudo apt install -y crowbar
  EOF
end
```

그런 다음 아래 명령어를 실행하여 구동 중인 베이그런트 인스턴스에 적용할 수 있어요:

```console
kali@kali:~$ vagrant reload
kali@kali:~$
```

VM을 다시 프로피저닝하려면, 일반적으로 머신이 처음 부팅될 때만 실행되므로, 다음 중 하나의 명령어만 사용할 수 있어요:

```console
$ vagrant provision  # provision the powered on VM
$ vagrant up --provision  # when VM is powered off, power it on then provision
$ vagrant reload --provision  # reboot the VM then provision
```

[베이그런트 문서](https://www.vagrantup.com/docs/vagrantfile/machine_settings)에서 더 많은 구성 옵션을 확인할 수 있어요.