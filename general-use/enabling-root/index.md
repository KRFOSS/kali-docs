---
title: 루트 계정 활성화하기
description:
icon:
date: 2021-03-30
weight:
author: ["gamb1t",]
keywords: ["",]
번역: ["xenix4845"]
og_description:
---

## 영구적 사용과 임시적 사용

수퍼유저, 즉 루트 권한을 장시간 사용해야 하는 경우가 있어요. 이런 경우에는 간단히 `sudo su` 명령어(현재 사용자의 비밀번호를 요구함), 칼리 메뉴에서 루트 터미널 아이콘 선택, 또는 `su -` 명령어(루트 사용자의 비밀번호를 요구함)를 사용하여 루트 계정에 쉽게 접근할 수 있어요. 작업이 끝나면 `exit` 명령어나 CTRL+D를 눌러 권한이 상승된 셸에서 나갈 수 있어요.

하지만 여러 세션에 걸쳐 권한 상승 없이 루트 계정을 사용하고 싶은 경우도 있어요. 이러한 상황에서는 보안상의 이유로 기본적으로 비활성화되어 있는 루트 계정을 완전히 활성화하기 위해 패키지를 설치하고 몇 가지 수정을 해야 해요.

## 루트 계정 활성화하기

먼저 루트 비밀번호를 설정해야 하는데, 이는 현재 사용자의 비밀번호([이 경우 `kali`](/docs/introduction/default-credentials/))와 다르게 설정해야 해요. 다음과 같이 설정할 수 있어요:

```console
kali@kali:~$ sudo passwd
[sudo] password for kali:
New password:
Retype new password:
passwd: password updated successfully
kali@kali:~$
```

{{% notice info %}}
비밀번호 입력 시 화면에 출력되지 않지만, 키 입력은 정상적으로 등록된다는 점을 참고하세요.
{{% /notice %}}

다음으로 결정해야 할 사항은 SSH를 통해 루트를 사용할지, 또는 설치된 데스크톱 환경의 로그인 프롬프트를 통해 사용할지 여부예요.

### SSH를 위한 루트 활성화

`/etc/ssh/sshd_config` 파일을 살펴보면 **PermitRootLogin** 행이 있어요. 우리의 사용 사례에 맞게 이 행을 변경해야 해요:

```console
kali@kali:~$ grep PermitRootLogin /etc/ssh/sshd_config
#PermitRootLogin prohibit-password
# the setting of "PermitRootLogin without-password".
kali@kali:~$
kali@kali:~$ man sshd_config | grep -C 1 prohibit-password
     PermitRootLogin
             Specifies whether root can log in using ssh(1).  The argument must be yes, prohibit-password, forced-commands-only, or no.  The default
             is prohibit-password.

             If this option is set to prohibit-password (or its deprecated alias, without-password), password and keyboard-interactive authentication
             are disabled for root.
kali@kali:~$
kali@kali:~$ sudo systemctl restart ssh
kali@kali:~$
```

루트 계정에 대한 SSH 키 기반 로그인을 설정했다면, 해당 행의 주석을 제거하고 계속 진행하면 돼요. 그렇지 않으면 **PermitRootLogin**을 **yes**로 변경해서 비밀번호를 입력할 수 있게 해야 해요.

### GNOME 및 KDE 로그인을 위한 루트 활성화

먼저 `kali-root-login`을 설치해서 GNOME GDM3 및 KDE 로그인 프롬프트를 통해 루트 계정에 로그인할 수 있도록 여러 구성 파일을 변경해야 해요. 다른 데스크톱 환경을 사용하는 경우에는 이 단계가 필요하지 않아요:

```console
kali@kali:~$ sudo apt -y install kali-root-login
Reading package lists... Done
Building dependency tree
Reading state information... Done
The following NEW packages will be installed:
  kali-root-login
0 upgraded, 1 newly installed, 0 to remove and 1516 not upgraded.
Need to get 6,776 B of archives.
After this operation, 33.8 kB of additional disk space will be used.
Get:1 http://kali.download/kali kali-rolling/main amd64 kali-root-login all 2019.4.0 [6,776 B]
Fetched 6,776 B in 1s (10.9 kB/s)
Selecting previously unselected package kali-root-login.
(Reading database ... 333464 files and directories currently installed.)
Preparing to unpack .../kali-root-login_2019.4.0_all.deb ...
Adding 'diversion of /etc/gdm3/daemon.conf to /etc/gdm3/daemon.conf.original by kali-root-login'
Adding 'diversion of /etc/pam.d/gdm-password to /etc/pam.d/gdm-password.original by kali-root-login'
Adding 'diversion of /etc/pam.d/gdm-autologin to /etc/pam.d/gdm-autologin.original by kali-root-login'
Adding 'diversion of /etc/pam.d/lightdm-autologin to /etc/pam.d/lightdm-autologin.original by kali-root-login'
Adding 'diversion of /etc/pam.d/sddm to /etc/pam.d/sddm.original by kali-root-login'
Adding 'diversion of /etc/sddm.conf to /etc/sddm.conf.original by kali-root-login'
Unpacking kali-root-login (2019.4.0) ...
Setting up kali-root-login (2019.4.0) ...
Installing /usr/share/kali-root-login/daemon.conf as /etc/gdm3/daemon.conf
Installing /usr/share/kali-root-login/gdm-password as /etc/pam.d/gdm-password
Installing /usr/share/kali-root-login/gdm-autologin as /etc/pam.d/gdm-autologin
Installing /usr/share/kali-root-login/lightdm-autologin as /etc/pam.d/lightdm-autologin
Installing /usr/share/kali-root-login/sddm as /etc/pam.d/sddm
Installing /usr/share/kali-root-login/sddm.conf as /etc/sddm.conf
kali@kali:~$
```

이제 비루트 사용자 계정에서 로그아웃하고 앞서 설정한 비밀번호를 사용하여 루트로 로그인할 수 있어요.
