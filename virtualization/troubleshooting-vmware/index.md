---
title: 칼리 VMware VM 문제 해결
description:
icon:
weight: 299
author: ["arnaudr",]
번역: ["kmw0410"]
---

# KDE 데스크톱에서 복사/붙여넣기, 드래그 앤 드롭 문제 해결

VMware 가상 머신에서 칼리 KDE 데스크탑을 사용하는 사람들을 위한 거에요. 복사/붙여넣기와 드래그 앤 드롭이 즉시 작동하지 않는 것은 알려진 문제에요. 해결 방법으로, systemd 사용자 인스턴스를 비활성화하고 대신 KDE의 레거시 부팅 메커니즘을 사용할 수 있어요. 이를 위해 터미널을 열고 다음 명령어를 입력하세요:

```console
kali@kali:~$ kwriteconfig5 --file startkderc --group General --key systemdBoot false
```

확인을 위해, 명령어가 `~/.config/startkderc` 파일을 다음과 같이 생성했는지 확인해 볼 수 있어요:

```console
kali@kali:~$ cat ~/.config/startkderc
[General]
systemdBoot=false
```

그런 다음 로그아웃하고 다시 로그인하면 문제가 해결될 거에요.

이 문제는 업스트림에 보고되었으며, 자세한 내용은 다음 링크에서 확인할 수 있어요:
- <https://github.com/vmware/open-vm-tools/issues/568>
- <https://bugs.debian.org/cgi-bin/bugreport.cgi?bug=1020960>
