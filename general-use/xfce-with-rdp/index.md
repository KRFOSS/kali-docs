---
title: Xfce와 RDP 설정하기
description:
icon:
weight:
author: ["gamb1t",]
번역: ["xenix4845"]
---

칼리 리눅스는 다양한 장치와 시스템에서 지원돼요. 이러한 시스템 중 일부에서는 기본적인 설치만 가능하며 WSL이나 Docker와 같이 GUI에 직접 접근하지 못할 수도 있어요. Kali에서 GUI에 접근하는 간단한 방법 중 하나는 Xfce를 설치하고 RDP를 설정하는 것이에요. 이 작업은 수동으로 하거나 [여기](https://gitlab.com/kalilinux/recipes/kali-scripts/-/blob/main/xfce4.sh)에서 제공되는 스크립트를 사용하여 수행할 수 있으며, 아래에서 확인할 수 있어요:

```plaintext
#!/bin/sh
echo "[i] Updating and upgrading Kali (this will take a while)"
apt-get update
apt-get full-upgrade -y

echo "[i] Installing Xfce4 & xrdp (this will take a while as well)"
apt-get install -y kali-desktop-xfce xorg xrdp xorgxrdp

echo "[i] Configuring xrdp to listen to port 3390 (but not starting the service)"
sed -i 's/port=3389/port=3390/g' /etc/xrdp/xrdp.ini
```

Xfce와 RDP 설정을 시작하기 전에, Kali가 있는 특정 시스템의 차이점을 먼저 인식해야 해요. 첫 번째는 Docker예요. Docker에서 이 설정을 사용하려면 다음과 같은 실행 명령을 제공해야 해요:

`docker run -p 3390:3390 --expose=3390 --tty --interactive kalilinux/kali-rolling /bin/bash`

종료된 컨테이너를 다시 시작하는 방법과 같은 Docker의 추가 사용법은 [Kali Docker 이미지 사용하기](/docs/containers/using-kali-docker-images/)를 읽어보세요.

AWS의 경우, 머신을 설정할 때 IP가 적절한 포트에 접근할 수 있도록 해야 해요.

스크립트를 사용하려면 다음과 같이 하세요:

```console
# Docker를 사용하는 경우, 계속하기 전에 먼저 다음 명령을 실행하세요:
root@182156129:/$ apt update && DEBIAN_FRONTEND=noninteractive apt install -y wget kali-linux-headless

kali@kali:~$ wget https://gitlab.com/kalilinux/recipes/kali-scripts/-/raw/main/xfce4.sh
kali@kali:~$
kali@kali:~$ chmod +x xfce4.sh
kali@kali:~$
kali@kali:~$ sudo ./xfce4.sh
kali@kali:~$
```

수동으로 설정하면 어떤 구성이 수행되는지에 대해 더 많은 제어가 가능하지만, 시간이 조금 더 걸려요.

WSL을 사용하는 경우, xrdp와 xfce가 연결되도록 dbus-x11을 다음으로 설치해야 해요:

```console
kali@kali:~$ sudo apt install -y dbus-x11
kali@kali:~$
```

Xfce와 RDP를 설정한 후, 서비스를 시작해야 해요:

```console
# AWS를 사용하는 경우
kali@kali:~$ sudo systemctl enable xrdp --now
kali@kali:~$

# WSL 또는 Docker를 사용하는 경우
kali@kali:~$ sudo /etc/init.d/xrdp start
kali@kali:~$
```

AWS의 경우, 연결하기 전에 기본 'kali' 계정의 비밀번호를 변경해야 해요. 이는 다음 명령으로 수행할 수 있어요:

```console
kali@kali:~$ echo kali:kali | sudo chpasswd
kali@kali:~$
```

Docker를 사용하는 경우, 새 사용자를 만들어야 해요. adduser를 사용하여 이 작업을 수행할 수 있어요:

```console
kali@kali:~$ adduser kali
[...]
kali@kali:~$
```

그런 다음 RDP 클라이언트를 사용하여 해당 시스템에 연결할 수 있어요. 사용 중인 포트에 유의하세요. 스크립트를 사용했다면 포트는 3390이 될 거에요. WSL과 Docker의 경우, Windows 시스템에서 연결하려는 IP는 127.0.0.1:3390이 될 거에요(또는 별도의 컴퓨터에서 호스트 시스템 IP). AWS의 경우, IP는 SSH를 통해 연결할 때와 동일해요.

{{% notice info %}}

연결을 시도할 때 `Authentication Required to Create Managed Color Device` 오류가 발생할 수 있어요. 이 문제를 해결하려면 다음을 수행하세요.

{{% /notice %}}

```console
kali@kali:~$ cat <<EOF | sudo tee /etc/polkit-1/localauthority/50-local.d/45-allow-colord.pkla
[Allow Colord all Users]
Identity=unix-user:*
Action=org.freedesktop.color-manager.create-device;org.freedesktop.color-manager.create-profile;org.freedesktop.color-manager.delete-device;org.freedesktop.color-manager.delete-profile;org.freedesktop.color-manager.modify-device;org.freedesktop.color-manager.modify-profile
ResultAny=no
ResultInactive=no
ResultActive=yes
EOF
kali@kali:~$
```
