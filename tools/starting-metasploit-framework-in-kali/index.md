---
title: Metasploit Framework
description:
icon:
date: 2020-01-13
type: post
weight: 100
author: ["g0tmi1k",]
tags: ["",]
keywords: ["",]
og_description:
---

In keeping with the [Kali Linux Network Services Policy](/docs/policy/kali-linux-network-service-policies/), no network services, _including_ database services, run on boot as a default, so there are a couple of steps that need to be taken in order to get [Metasploit](https://www.metasploit.com/) up and running with database support.

## Start the Kali PostgreSQL Service

Metasploit uses **[PostgreSQL](http://www.postgresql.org/)** as its database so it needs to be launched first.

```
sudo service postgresql start
```

You can verify that **PostgreSQL** is running by checking the output of **ss -ant** and making sure that port 5432 is listening.

```
State Recv-Q Send-Q Local Address:Port Peer Address:Port
LISTEN 0 128 :::22 :::*
LISTEN 0 128 *:22 *:*
LISTEN 0 128 127.0.0.1:5432 *:*
LISTEN 0 128 ::1:5432 :::*
```

## Initialise the Metasploit PostgreSQL Database

With **PostgreSQL** up and running, we next need to create and initialize the **msf** database.

```
sudo msfdb init
```

## Launch msfconsole in Kali

Now that the **PostgreSQL** service is up and running and the database is initialized, you can launch **msfconsole** and verify database connectivity with the **db_status** command as shown below.

```
msfconsole
```

```
msf > db_status
[*] postgresql connected to msf3
msf >
```
