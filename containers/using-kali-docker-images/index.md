---
title: Using Kali Linux Docker Images
description:
icon:
weight:
author: ["gamb1t",]
---

To use the [Kali Linux Docker image](/docs/containers/official-kalilinux-docker-images/), we will do the following commands:

```console
kali@kali:~$ docker pull kalilinux/kali-rolling
kali@kali:~$
kali@kali:~$ docker run --tty --interactive kalilinux/kali-rolling /bin/bash
┌──(root㉿e4ae79503654)-[/]
└─#

┌──(root㉿e4ae79503654)-[/]
└─# exit
kali@kali:~$
```

Please note, that this does not allow for systemd functionality, which would allow access to items such as `systemctl`. There are ways to get systemd to work with Docker, however they include modifying the Dockerfile and `docker run` flags. At this time this will not be covered.

Please also note, **all the images below do not come with the "default" [metapackage](/docs/general-use/metapackages/)**. You will need to `apt update && apt -y install kali-linux-headless`.

To resume an exited container we will complete the following:

```console
kali@kali:~$ docker container list --all
CONTAINER ID   IMAGE                    COMMAND       CREATED         STATUS                          PORTS     NAMES
d36922fa21e8   kalilinux/kali-rolling   "/bin/bash"   2 minutes ago   Exited (0) About a minute ago             lucid_heyrovsky
kali@kali:~$
kali@kali:~$ docker start d36922fa21e8
kali@kali:~$
```

After you execute the following command you will attach to the Docker container, however you must press return once to fully see the prompt.

```console
kali@kali:~$ docker attach d36922fa21e8

┌──(root㉿d36922fa21e8)-[/]
└─#
```

This will resume the container in whatever state you left it after running the initial `docker run` command or the last `docker start` and `docker attach` sequence.
