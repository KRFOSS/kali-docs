---
title: Using Kali Linux Podman Images
description:
icon:
weight:
author: ["gamb1t",]
---

To use the Kali Linux Podman image, we will do the following commands:

```console
kali@kali:~$ echo "unqualified-search-registries = ['registry.fedoraproject.org', 'registry.access.redhat.com', 'registry.centos.org', 'docker.io']" | sudo tee -i /etc/containers/registries.conf
kali@kali:~$
kali@kali:~$ tail /etc/containers/registries.conf
# [[registry.mirror]]
# location = "example-mirror-1.local/mirrors/foo"
# insecure = true
# # Given the above, a pull of example.com/foo/image:latest will try:
# # 1. example-mirror-0.local/mirror-for-foo/image:latest
# # 2. example-mirror-1.local/mirrors/foo/image:latest
# # 3. internal-registry-for-example.net/bar/image:latest
# # in order, and use the first one that exists.

unqualified-search-registries = ['registry.fedoraproject.org', 'registry.access.redhat.com', 'registry.centos.org', 'docker.io']
kali@kali:~$
kali@kali:~$ podman pull kalilinux/kali-rolling
kali@kali:~$
kali@kali:~$ podman run --tty --interactive kalilinux/kali-rolling /bin/bash
┌──(root㉿e4ae79503654)-[/]
└─#

┌──(root㉿e4ae79503654)-[/]
└─# exit
kali@kali:~$
```

Please note, that this does not allow for systemd functionality, which would allow access to items such as `systemctl`.

Please also note, **the images do not come with the "default" [metapackage](/docs/general-use/metapackages/)**. You will need to `apt update && apt -y install kali-linux-headless`.

To resume an exited container we will complete the following:

```console
kali@kali:~$ podman ps -a
CONTAINER ID  IMAGE                                    COMMAND     CREATED        STATUS                   PORTS       NAMES
7df5f0dbe6b7  docker.io/kalilinux/kali-rolling:latest  /bin/bash   2 seconds ago  Exited (0) 1 second ago              cool_tharp
kali@kali:~$
kali@kali:~$ podman start 7df5f0dbe6b7
kali@kali:~$
```

After you execute the following command you will attach to the Podman container, however you must press return once to fully see the prompt.

```console
kali@kali:~$ podman attach 7df5f0dbe6b7

┌──(root㉿d36922fa21e8)-[/]
└─#
```

This will resume the container in whatever state you left it after running the initial `podman run` command or the last `podman start` and `podman attach` sequence.