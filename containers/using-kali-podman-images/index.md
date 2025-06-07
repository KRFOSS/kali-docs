---
title: 포드맨 이미지를 사용하여 칼리 리눅스 사용하기
description: 
icon: 
weight: 
author: ["gamb1t"]
번역: ["xjnkr","xenix4845"]
---
Podman(포드맨)은 다양한 시스템에서 [설치 방법](https://podman.io/getting-started/installation)에 대한 매우 좋은 문서를 제공합니다. 우리는 공식 문서를 따르는 것을 권장하지만, 데비안 기반 시스템에서는 매우 간단한 명령어로 설치할 수 있습니다:

```console
kali@kali:~$ sudo apt update && sudo apt install -y podman
[...]
kali@kali:~$
```

{{% notice info %}} 
칼리 리눅스 이미지는 [컨테이너 단축 이름 목록](https://github.com/containers/shortnames)에 포함되어 있습니다. 이를 통해 전체 이미지 이름 `docker.io/kalilinux/kali-rolling` 대신 `kali-rolling`만 호출하는 기능을 사용할 수 있습니다. 이는 호스트 시스템이`/etc/containers/registries.conf.d/shortnames.conf`에 최신 단축 이름 목록을 제공하는 경우에 작동합니다. 우리는 이를 제공하는 칼리 리눅스를 사용하고 있으므로 이 기능을 활용할 수 있습니다. 
{{% /notice %}}

칼리 리눅스 포드맨 이미지를 사용하려면 다음 명령어를 실행합니다:

```console
kali@kali:~$ podman pull kali-rolling
kali@kali:~$
kali@kali:~$ podman run --tty --interactive kali-rolling
┌──(root㉿7df5f0dbe6b7)-[/]
└─#
┌──(root㉿7df5f0dbe6b7)-[/]
└─# exit
kali@kali:~$
```

주의: 이 방식은 `systemctl`과 같은 항목에 접근할 수 있는 systemd 기능을 제공하지 않습니다.

또한 주의하세요, **이미지에는 "기본" [메타패키지](/general-use/metapackages/)가 포함되어 있지 않습니다**. `apt update && apt -y install kali-linux-headless` 명령어를 실행하여 설치해야 합니다.

종료된 컨테이너를 다시 시작하려면 다음을 수행합니다:

```console
kali@kali:~$ podman ps -a
CONTAINER ID  IMAGE                                    COMMAND     CREATED        STATUS                   PORTS       NAMES
7df5f0dbe6b7  docker.io/kalilinux/kali-rolling:latest  /bin/bash   2 seconds ago  Exited (0) 1 second ago              cool_tharp
kali@kali:~$
kali@kali:~$ podman start 7df5f0dbe6b7
kali@kali:~$
```

다음 명령어를 실행한 후 포드맨 컨테이너에 연결되지만, 프롬프트를 완전히 보려면 Return 키를 한 번 눌러야 합니다:

```console
kali@kali:~$ podman attach 7df5f0dbe6b7
┌──(root㉿7df5f0dbe6b7)-[/]
└─#
```

이렇게 하면 초기 `podman run` 명령어 또는 마지막 `podman start`와 `podman attach` 시퀀스 실행 후 남겨둔 상태 그대로 컨테이너가 다시 시작됩니다.

마지막으로, 컨테이너 사용이 끝났다면 다음 명령어로 제거할 수 있습니다:

```console
kali@kali:~$ podman rm 7df5f0dbe6b7
7df5f0dbe6b7
kali@kali:~$
```
