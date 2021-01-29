---
title: Installing Docker on Kali Linux
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

To install Docker on Kali you need to remember that there is already a package named "docker", therefore Docker has to be installed under a different name. If you install `docker` you will not end up with the container version. The version we will be installing is named `docker.io`. All commands are the same however, so running `docker` on the command line will be the appropriate command.

```
kali@kali:~$ sudo apt update
kali@kali:~$
kali@kali:~$ sudo apt install -y docker.io
kali@kali:~$
kali@kali:~$ sudo systemctl enable docker --now
kali@kali:~$
kali@kali:~$ docker
kali@kali:~$
```

You can now get started with using docker, with `sudo`. If you want to add yourself to the docker group to use `docker` without `sudo`, an additional step is needed:

```
kali@kali:~$ sudo usermod -aG docker $USER
kali@kali:~$
```

If you would like to use a Kali Docker image, we have a doc page for that [here](/docs/containers/using-kali-docker-images/).

##### Installing docker-ce on Kali Linux

`docker-ce` can be installed from Docker repository using Debian `buster` codename.

Add Docker repository to your `sources.list`

```markdown
printf "%s\n" "deb [arch=amd64] https://download.docker.com/linux/debian buster stable" |\
sudo tee /etc/apt/sources.list.d/docker-ce.list
```
Import the gpg key:

```markdown
curl -fsSL https://download.docker.com/linux/debian/gpg | sudo apt-key add -
```
Fingerprint  checking:

```markdown
sudo apt-key fingerprint 0EBFCD88
```

Install the latest version of `docker-ce`:

```markdown
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io
```

##### References

[Install Docker Engine on Debian](https://docs.docker.com/engine/install/debian/)
