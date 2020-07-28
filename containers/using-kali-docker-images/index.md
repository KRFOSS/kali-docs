---
title: Using Kali Linux Docker Images
description:
icon:
date: 2020-06-19
type: post
weight:
author: ["gamb1t",]
tags: ["",]
keywords: ["",]
og_description:
---
To use the Kali Linux Docker image, we will do the following commands:

```
kali@kali:~$ docker pull kalilinux/kali-rolling
kali@kali:~$
kali@kali:~$ docker run -ti kalilinux/kali-rolling /bin/bash
root@e4ae79503654:/
root@e4ae79503654:/ exit
kali@kali:~$
```
Please note that this does not allow for systemd functionality. There are ways to get systemd to work with Docker, however they include modifying the Dockerfile and `docker run` flags. At this time this will not be covered.
