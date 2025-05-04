---
title: Kali Linux 네트워크 서비스 정책
description:
icon:
weight:
author: ["g0tmi1k",]
번역: ["xenix4845"]
---
{{% notice info %}}
이 문서에서 말하는 '우리'는 ROKFOSS 프로젝트가 아닌 Kali Linux와 Offsec을 의미합니다.
{{% /notice %}}

Kali Linux는 침투 테스트용 도구 모음이며, 때로는 **적대적 환경**에서 사용될 수 있습니다. 그에 따라 Kali Linux는 네트워크 서비스(외부 접속을 대기하는 데몬)를 일반적인 리눅스 배포판과 **다르게** 다룹니다. 즉, **기본 상태에서는 외부에서 들어오는 요청을 수신(listen)하는 서비스를 단 하나도 자동 실행하지 않습니다.**  이렇게 하여 설치 직후 노출 범위를 최소화합니다.

### 기본 서비스 비활성화(Disallow) 정책

Kali Linux는 **부팅 후에도 서비스가 자동으로 살아나지 못하도록(disallow) 막는 것을 기본 정책**으로 삼습니다.  
예를 들어 TCP 3142 포트에서 프록시 서버를 구동하는 `apt-cacher-ng` 패키지를 설치하면 다음과 같은 메시지를 볼 수 있습니다.

```console
kali@kali:~$ sudo apt install -y apt-cacher-ng
[...]
Setting up apt-cacher-ng (0.7.11-1) ...
update-rc.d: We have no instructions for the apt-cacher-ng init script.
update-rc.d: It looks like a network service, we disable it.
[...]
kali@kali:~$
```

`update-rc.d`(SysV init 기반 부팅 스크립트 관리 도구)가 **서비스를 “네트워크 서비스”로 판단하여, 부팅 시 자동 시작을 차단(disable)했음을** 알 수 있습니다.

### 기본 정책 무시(서비스 영구 활성화) 방법

특정 상황에서는 서비스를 부팅 때마다 자동으로 실행되게 하고 싶을 수 있습니다.  
이 경우 `systemctl enable <서비스>` 명령으로 **서비스 지속(persist) 설정**을 켤 수 있습니다.

```console
kali@kali:~$ sudo systemctl enable apt-cacher-ng
Synchronizing state of apt-cacher-ng.service with SysV service script with /lib/systemd/systemd-sysv-install.
Executing: /lib/systemd/systemd-sysv-install enable apt-cacher-ng
insserv: warning: current start runlevel(s) (empty) of script `apt-cacher-ng' overrides LSB defaults (2 3 4 5).
insserv: warning: current stop runlevel(s) (0 1 2 3 4 5 6) of script `apt-cacher-ng' overrides LSB defaults (0 1 6).
```

### 서비스 허용(allowlist)·차단(blocklist) 목록

어떤 서비스가 부팅 시 자동 실행 가능한지 여부는 **`/usr/sbin/update-rc.d`** 파일 끝부분의 **허용·차단 목록**에서 확인·수정할 수 있습니다.

```console
kali@kali:~$ tail -95 /usr/sbin/update-rc.d | more
[...]
__DATA__
#
# List of blocklisted init scripts
#
apache2 disabled
avahi-daemon disabled
bluetooth disabled
cups disabled
dictd disabled
ssh disabled
[...]
#
# List of allowlisted init scripts
#
acpid enabled
acpi-fakekey enabled
acpi-support enabled
alsa-utils enabled
anacron enabled
[...]
```

- **blocklist(차단 목록)** : 부팅 시 자동 실행이 금지된 서비스  
- **allowlist(허용 목록)** : 부팅 시 자동 실행이 허가된 서비스  

필요에 따라 이 목록을 편집해 서비스의 자동 실행 여부를 명시적으로 지정할 수 있습니다.