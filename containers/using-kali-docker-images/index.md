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
root@e4ae79503654:/
root@e4ae79503654:/ exit
kali@kali:~$
```

Please note, that this does not allow for systemd functionality, which would allow access to items such as `systemctl`. There are ways to get systemd to work with Docker, however they include modifying the Dockerfile and `docker run` flags. At this time this will not be covered.

Please also note, **all the images below do not come with the "default" [metapackage](/docs/general-use/metapackages/).** You will need to `apt update && apt -y install kali-linux-headless`.
