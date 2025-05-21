---
title: 칼리 Vagrant Vagrantfile 커스터마이징
description:
icon:
weight:
author: ["gamb1t",]
번역: ["xenix4845"]
---

Vagrant는 시작할 때 설치하려는 박스에 맞는 고유한 Vagrantfile을 만들 수 있는 아주 좋은 기능을 제공해요. 예를 들어, 칼리 Vagrant 머신을 시작할 때 이런 Vagrantfile이 생성돼요:

```console
kali@kali:~$ cat Vagrantfile | grep -v '#'

Vagrant.configure("2") do |config|

  config.vm.box = "kalilinux/rolling"








end
```

여기서 볼 수 있듯이 Vagrant는 '2'를 사용하도록 구성되어 있고 'vm.box'는 칼리예요. 완벽해요! 하지만 이게 무슨 의미일까요? '2'는 사실 우리가 실행 중인 Vagrant 버전(Vagrant 1.1 이상)을 나타내고, 'vm.box'는 단순히 우리가 칼리를 사용하고 있다는 것을 나타내요. 그럼 이걸로 뭘 할 수 있을까요? HashiCorp는 사실 Vagrantfile 구성 방법과 다양한 구성 값에 대해 설명하는 꽤 좋은 [문서](https://www.vagrantup.com/docs/vagrantfile)를 제공하고 있어서, 모든 내용을 다루지는 않을 거예요. 대신 칼리를 더 쾌적하게 사용할 수 있도록 도와주는 몇 가지 옵션에 대해 알아볼 거예요.

{{% notice info %}}
Vagrant는 가상 머신을 쉽게 생성하고 관리할 수 있게 해주는 도구로, Vagrantfile은 가상 머신의 설정을 정의하는 파일이에요.
{{% /notice %}}

## 칼리 환경을 개선하기 위한 Vagrantfile 구성

여기서는 구성 옵션에 대한 자세한 설명은 HashiCorp 문서에 더 잘 설명되어 있으므로 생략하고, 칼리에서 어떤 이점을 얻을 수 있는지에 대해 알아볼 거예요.

 - config.vm.base_address - 기본 IP 주소를 설정하는 것은 많은 상황에서 유용할 수 있어요. 특히 리버스 쉘(reverse shells)을 설정할 때 자신의 IP를 알고 있으면 도움이 돼요.

 - config.vm.hostname - 기본 호스트명 대신 새로운 호스트명을 설정할 수 있어요!

 - config.vm.provider - 여러 VM 제공자가 있는 경우 원하는 제공자를 선택하는 데 도움이 될 수 있어요. 이는 `--provider` 명령 플래그로도 수행할 수 있다는 점을 기억하세요.

 - config.vm.provision - 이 [옵션](https://www.vagrantup.com/docs/provisioning)은 Vagrant 시작 후 특정 설정을 구성하고 싶은 사람들에게 유용해요. 예를 들어, 시작 후 칼리가 최신 상태인지 확인하고 kali-linux-large가 설치되어 있는지 확인하고 싶다면 쉽게 할 수 있어요.

 - config.vm.usable_port_range - 더 넓은 포트 범위를 사용하고 싶다면 꼭 알아야 하는 옵션이에요.

 - config.ssh.forward_x11 - 특정 앱에 GUI 접근이 필요할 때 유용한 옵션이에요.
