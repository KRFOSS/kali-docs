---
title: 칼리 리눅스에 도커 설치하기
description: 
icon: 
weight: 
author: ["gamb1t", "elreydetoda", "gad3r",]
번역: ["xjnkr",]
---

칼리에 Docker를 설치하려면 이미 “docker”라는 이름의 패키지가 있으므로 다른 이름으로 Docker를 설치해야 합니다. 도커를 설치하면 컨테이너 버전으로 끝나지 않습니다. 설치할 버전의 이름은 `docker.io`입니다. 그러나 모든 명령은 동일하므로 명령줄에서 docker를 실행하면 적절한 명령어가 됩니다:

```console
kali@kali:~$ sudo apt update
kali@kali:~$
kali@kali:~$ sudo apt install -y docker.io
kali@kali:~$
kali@kali:~$ sudo systemctl enable docker --now
kali@kali:~$
kali@kali:~$ docker
kali@kali:~$
```

이제 `sudo`를 사용하여 Docker를 사용할 수 있습니다. 만약 `sudo` 없이 `docker` 명령어를 사용하고 싶다면, 자신을 `docker` 그룹에 추가하는 추가 단계가 필요합니다:

```console
kali@kali:~$ sudo usermod -aG docker $USER
kali@kali:~$
```

마지막으로 해야 할 일은 **로그아웃한 후 다시 로그인하는 것**입니다.

칼리 도커 이미지를 사용하고 싶다면, 관련 문서를 [여기](/containers/using-kali-docker-images/)에서 확인할 수 있습니다.

##### 칼리 리눅스에 docker-ce 설치하기

`docker-ce`는 Docker 공식 저장소에서 설치할 수 있습니다. 이때 주의할 점은, [칼리 리눅스는 Debian을 기반으로 하고 있다는 것](/policy/kali-linux-relationship-with-debian/)입니다. 따라서 칼리 리눅스가 [롤링 릴리스 배포판](/general-use/kali-branches/)이긴 하지만, 설치 시에는 [데비안의 현재 안정(stable) 버전](https://www.debian.org/releases/stable/)을 기준으로 해야 합니다.  
작성 시점(2023년 12월) 기준으로는 "bookworm"이 해당 버전입니다.

```console
kali@kali:~$ echo "deb [arch=amd64 signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/debian bookworm stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list 
```

아래의 명령어로 gpg key 를 설치합니다:

```console
kali@kali:~$ curl -fsSL https://download.docker.com/linux/debian/gpg |
  sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
```

가장 최신버전의 docker-ce를 설치합니다

```console
kali@kali:~$ sudo apt update
kali@kali:~$ sudo apt install -y docker-ce docker-ce-cli containerd.io
```

##### 참고

[데비안에 도커 엔진 설치하기](https://docs.docker.com/engine/install/debian/)
