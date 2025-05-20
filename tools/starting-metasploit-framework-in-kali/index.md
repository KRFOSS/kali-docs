---
title: Metasploit 프레임워크
description:
icon:
weight:
author: ["g0tmi1k",]
번역: ["ryuyijun"]
---

[Kali Linux 네트워크 서비스 정책](/policy/kali-linux-network-service-policy/)에 따라, 데이터베이스 서비스를 포함한 모든 네트워크 서비스는 기본적으로 부팅 시 실행되지 않습니다. 따라서 [Metasploit](https://www.metasploit.com/)을 데이터베이스 지원과 함께 실행하기 위해서는 몇 가지 단계를 거쳐야 합니다.

## 빠른 방법

**[PostgreSQL](https://www.postgresql.org/)** 서비스를 시작하고 다음과 같이 설정하면 모든 것을 실행할 수 있습니다:

```console
kali@kali:~$ sudo msfdb init
[+] Starting database
[+] Creating database user 'msf'
[+] Creating databases 'msf'
[+] Creating databases 'msf_test'
[+] Creating configuration file '/usr/share/metasploit-framework/config/database.yml'
[+] Creating initial database schema
kali@kali:~$
```

_더 나아가 `sudo msfdb run`을 실행하면 위의 작업을 수행하고 이후에 자동으로 `msfconsole`을 시작합니다._

## MSFDB

Metasploit 설정의 다양한 부분을 상호 작용하는 데 도움이 되는 `msfdb` 명령어가 있습니다:

```console
kali@kali:~$ sudo msfdb

Manage the metasploit framework database

  msfdb init     # 데이터베이스 시작 및 초기화
  msfdb reinit   # 데이터베이스 삭제 후 재초기화
  msfdb delete   # 데이터베이스 삭제 및 사용 중지
  msfdb start    # 데이터베이스 시작
  msfdb stop     # 데이터베이스 중지
  msfdb status   # 서비스 상태 확인
  msfdb run      # 데이터베이스 시작 및 msfconsole 실행

kali@kali:~$
```

_참고: 이것은 기본 프로젝트와 함께 제공되는 [다른 버전](https://github.com/rapid7/metasploit-framework/issues/11369)의 `msfdb`입니다_

## Kali PostgreSQL 서비스 시작
Kali PostgreSQL 서비스 시작하기

Metasploit은 데이터베이스로 **[PostgreSQL](https://www.postgresql.org/)**을 사용하므로 먼저 시작해야 합니다:

```console
kali@kali:~$ sudo msfdb start
[+] Starting database
kali@kali:~$
```

**PostgreSQL**이 실행 중인지 확인하려면 `ss -ant` 명령어의 출력을 확인하여 포트 5432가 수신 중인지 확인하거나 `sudo msfdb status`를 사용하면 됩니다:

```plaintext
kali@kali:~$ sudo msfdb status
● postgresql.service - PostgreSQL RDBMS
     Loaded: loaded (/lib/systemd/system/postgresql.service; disabled; vendor preset: disabled)
     Active: active (exited) since Sun 2021-02-07 02:15:42 EST; 4s ago
    Process: 157089 ExecStart=/bin/true (code=exited, status=0/SUCCESS)
   Main PID: 157089 (code=exited, status=0/SUCCESS)

Feb 07 02:15:42 kali systemd[1]: Starting PostgreSQL RDBMS...
Feb 07 02:15:42 kali systemd[1]: Finished PostgreSQL RDBMS.

COMMAND     PID     USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
postgres 157071 postgres    5u  IPv6 647182      0t0  TCP localhost:5432 (LISTEN)
postgres 157071 postgres    6u  IPv4 647183      0t0  TCP localhost:5432 (LISTEN)


UID          PID    PPID  C STIME TTY      STAT   TIME CMD
postgres  157071       1  1 02:15 ?        Ss     0:00 /usr/lib/postgresql/13/bin/postgres -D /var/lib/postgresql/13/main -c config_file=/etc/postgresql/13/main/postgresql.con

[i] No configuration file found
kali@kali:~$
```

## Metasploit PostgreSQL 데이터베이스 초기화

**PostgreSQL**이 실행 중이면, 다음으로 **msf** 데이터베이스를 생성하고 초기화해야 합니다:

```console
kali@kali:~$ sudo msfdb init
[i] Database already started
[+] Creating database user 'msf'
[+] Creating databases 'msf'
[+] Creating databases 'msf_test'
[+] Creating configuration file '/usr/share/metasploit-framework/config/database.yml'
[+] Creating initial database schema
kali@kali:~$
```

## Kali에서 msfconsole 실행

이제 **PostgreSQL** 서비스가 실행 중이고 데이터베이스가 초기화되었으므로, **msfconsole**을 실행하고 아래와 같이 **db_status** 명령어로 데이터베이스 연결을 확인할 수 있습니다:

```console
kali@kali:~$ msfconsole -q
msf6 >
msf6 > db_status
[*] Connected to msf. Connection type: postgresql.
msf6 >
```