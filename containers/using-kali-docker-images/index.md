---
title: 칼리 리눅스 도커 이미지 사용하기
description: 
icon: 
weight: 
author: ["gamb1t"]
번역: ["ryuyijun"]
---
[칼리 리눅스 도커 이미지](/containers/official-kalilinux-docker-images/)를 사용하기 위해 다음 명령어를 실행합니다:

```console
kali@kali:~$ docker pull docker.io/kalilinux/kali-rolling
kali@kali:~$
kali@kali:~$ docker run --tty --interactive kalilinux/kali-rolling
┌──(root㉿e4ae79503654)-[/]
└─#
┌──(root㉿e4ae79503654)-[/]
└─# exit
kali@kali:~$
```

주의: 이 방식은 `systemctl`과 같은 항목에 접근할 수 있는 systemd 기능을 제공하지 않습니다. 도커에서 systemd를 작동시키는 방법이 있지만, Dockerfile과 `docker run` 플래그를 수정해야 합니다. 현재 이 내용은 다루지 않을 예정입니다.

또한 주의하세요, **아래의 모든 이미지에는 "기본" [메타패키지](/general-use/metapackages/)가 포함되어 있지 않습니다**. `apt update && apt -y install kali-linux-headless` 명령어를 실행하여 설치해야 합니다.

종료된 컨테이너를 다시 시작하려면 다음을 수행합니다:

```console
kali@kali:~$ docker container list --all
CONTAINER ID   IMAGE                    COMMAND       CREATED         STATUS                          PORTS     NAMES
d36922fa21e8   kalilinux/kali-rolling   "/bin/bash"   2 minutes ago   Exited (0) About a minute ago             lucid_heyrovsky
kali@kali:~$
kali@kali:~$ docker start d36922fa21e8
kali@kali:~$
```

다음 명령어를 실행한 후 도커 컨테이너에 연결되지만, 프롬프트를 완전히 보려면 Return 키를 한 번 눌러야 합니다:

```console
kali@kali:~$ docker attach d36922fa21e8
┌──(root㉿d36922fa21e8)-[/]
└─#
```

이렇게 하면 초기 `docker run` 명령어 또는 마지막 `docker start`와 `docker attach` 시퀀스 실행 후 남겨둔 상태 그대로 컨테이너가 다시 시작됩니다.

마지막으로, 컨테이너 사용이 끝났다면 다음 명령어로 제거할 수 있습니다:

```console
kali@kali:~$ docker rm d36922fa21e8
d36922fa21e8
kali@kali:~$
```
