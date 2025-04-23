---
title: Fixing PostgreSQL 'collation version mismatch'
description:
icon:
weight: 5000
author: ["daniruiz"]
---

After a system upgrade, when running tools such as Metasploit, GVM, or Bloodhound, you may encounter PostgreSQL error messages. These errors prevent the tools from functioning properly, citing **"collation version mismatch"** errors such as the following:

```plain
WARNING:  database "postgres" has a collation version mismatch
DETAIL:  The database was created using collation version 2.40, but the operating system provides version 2.41.
HINT:  Rebuild all objects in this database that use the default collation and run ALTER DATABASE postgres REFRESH COLLATION VERSION, or build PostgreSQL with the right library version.

```

![](images/postgresql-collation-mismatch-errors.png)

This errors can be fixed by running the following command in Kali's terminal:

```console
┌──(kali㉿kali)-[~]
└─$ sudo runuser -u postgres -- psql -c 'ALTER DATABASE postgres REFRESH COLLATION VERSION; ALTER DATABASE template1 REFRESH COLLATION VERSION;'
```
