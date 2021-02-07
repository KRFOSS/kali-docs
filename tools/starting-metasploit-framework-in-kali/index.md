msfdb

---
title: Metasploit Framework
description:
icon:
type: post
weight:
author: ["g0tmi1k",]
---

In keeping with the [Kali Linux Network Services Policy](/docs/policy/kali-linux-network-service-policies/), no network services, _including_ database services, run on boot as a default, so there are a couple of steps that need to be taken in order to get [Metasploit](https://www.metasploit.com/) up and running with database support.

## Quick way

You an have everything up and running, by starting the **[PostgreSQL](https://www.postgresql.org/)** service and set it up just by doing:

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

_You can even take it one step further by doing `sudo msfdb run` and it will do the same as the above, as well as start `msfconsole` afterwards_

## MSFDB

To help interactive with various parts of the Metasploit configuration there is `msfdb`:

```console
kali@kali:~$ sudo msfdb

Manage the metasploit framework database

  msfdb init     # start and initialize the database
  msfdb reinit   # delete and reinitialize the database
  msfdb delete   # delete database and stop using it
  msfdb start    # start the database
  msfdb stop     # stop the database
  msfdb status   # check service status
  msfdb run      # start the database and run msfconsole

kali@kali:~$
```

_Please note: this is a [different version](https://github.com/rapid7/metasploit-framework/issues/11369) of `msfdb` that is shipped with the default project_

## Start the Kali PostgreSQL Service
Start the Kali PostgreSQL Service

Metasploit uses **[PostgreSQL](https://www.postgresql.org/)** as its database so it needs to be launched first:

```console
kali@kali:~$ sudo msfdb start
[+] Starting database
kali@kali:~$
```

You can verify that **PostgreSQL** is running by checking the output of `ss -ant` and making sure that port 5432 is listening, or using `sudo msfdb status`:

```plaintext
kali@kali:~ sudo msfdb status
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

## Initialize the Metasploit PostgreSQL Database

With **PostgreSQL** up and running, we next need to create and initialize the **msf** database.

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

## Launch msfconsole in Kali

Now that the **PostgreSQL** service is up and running and the database is initialized, you can launch **msfconsole** and verify database connectivity with the **db_status** command as shown below.

```console
kali@kali:~$ msfconsole -q
msf6 >
msf6 > db_status
[*] Connected to msf. Connection type: postgresql.
msf6 >
```
