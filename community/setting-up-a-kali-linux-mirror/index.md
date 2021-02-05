---
title: Setting Up a Kali Linux Mirror
description:
icon:
type: post
weight:
author: ["g0tmi1k",]
---

## How to Set Up a Public Kali Linux Mirror

The explanations below are of interest to you if you want to contribute a publicly accessible mirror and if you want to integrate it in one of the mirror redirectors (http.kali.org and cdimage.kali.org).

If you want run a **private mirror**, see the dedicated section at the end.

### Requirements

To be an official Kali Linux mirror, you will need a web-accessible server **(http required and https if possible too)** with lots of disk space, good bandwidth, rsync, and SSH access enabled. As of early 2020, the main package repository is about 850 GB (in 2015 it was 450 GB) and the ISO images repository is about 110 GB (in 2015 it was 50 GB) but you can expect those numbers to grow regularly. A mirror site is expected to make the files available over HTTP and RSYNC so those services will need to be enabled. FTP access is optional.

**Note on "Push Mirroring"** — The Kali Linux mirroring infrastructure uses SSH-based triggers to ping the mirrors when they need to be refreshed. This currently takes place 4 times a day.

### Create a User Account for the Mirror

If you don't have yet an account dedicated for the mirrors, create such an account (here we call it "archvsync"):

```console
$ sudo adduser --disabled-password archvsync
Adding user 'archvsync' ...
...
Is the information correct? [Y/n]
```

### Create Directories for the Mirror

Create the directories that will contain the mirrors and change their owner to the dedicated user that you just created:

```console
$ sudo mkdir -p /srv/mirrors/kali{,-images}
$ sudo chown archvsync:archvsync /srv/mirrors/kali{,-images}
```

### Configure rsync

Next, configure the rsync daemon (enable it if needed) to export those directories:

```console
$ sudo sed -i -e "s/RSYNC_ENABLE=false/RSYNC_ENABLE=true/" /etc/default/rsync
$ sudo vim /etc/rsyncd.conf
$ cat /etc/rsyncd.conf
uid = nobody
gid = nogroup
max connections = 25
socket options = SO_KEEPALIVE

[kali]
path = /srv/mirrors/kali
comment = The Kali Archive
read only = true

[kali-images]
path = /srv/mirrors/kali-images
comment = The Kali ISO images
read only = true
$ sudo service rsync start
Starting rsync daemon: rsync.
```

### Configure Your Mirror

Configuration of your web server and FTP server are outside the scope of this article. Ideally, you should export the mirrors at `http://yourmirror.net/kali` and `http://yourmirror.net/kali-images` (and do the same for the FTP protocol, if you're supporting it).

Now comes interesting part: the configuration of the dedicated user that will handle the SSH trigger and the actual mirroring. You should first unpack [ftpsync.tar.gz](http://archive.kali.org/ftpsync.tar.gz) in the user's account:

```console
$ sudo su - archvsync
$ wget http://archive.kali.org/ftpsync.tar.gz
$ tar zxf ftpsync.tar.gz
```

Now we need to create a configuration file. We start from a template and we edit at least the _MIRRORNAME_, _TO_, _RSYNC_PATH_, and _RSYNC_HOST_ parameters:

```console
$ cp etc/ftpsync.conf.sample etc/ftpsync-kali.conf
$ vim etc/ftpsync-kali.conf
$ grep -E '^[^#]' etc/ftpsync-kali.conf
MIRRORNAME=`hostname -f`
TO="/srv/mirrors/kali/"
RSYNC_PATH="kali"
RSYNC_HOST=archive.kali.org
```

### Set Up the SSH Keys

The last step is to setup the `.ssh/authorized_keys` file so that archive.kali.org can trigger your mirror:

```console
$ mkdir -p ~/.ssh/
$ chown 0700 ~/.ssh/
$ wget -O - -q http://archive.kali.org/pushmirror.pub >> ~/.ssh/authorized_keys
$ chown 0644 ~/.ssh/authorized_keys
```

If you have not unpacked the ftpsync.tar.gz in the home directory, then you must adjust accordingly the `~/bin/ftpsync` path, which is hard-coded in `.ssh/authorized_keys`.

Now you must send an email to [devel@kali.org](mailto:devel@kali.org) with all the details concerning your mirrors (including user/port to use for SSH push access, public hostname, etc.) so that you can be added in the main mirror list and so that we can open up your rsync access on archive.kali.org. Please indicate clearly who should be contacted in case of problems (or if changes must be made/coordinated to the mirror setup).

Instead of waiting for the first push from archive.kali.org, you should run an initial rsync with a mirror close to you, using the mirror list linked above to select one. Assuming that you picked ftp.halifax.rwth-aachen.de, here's what you can run as your dedicated mirror user:

```console
$ rsync -qaH ftp.halifax.rwth-aachen.de::kali /srv/mirrors/kali/ &
$ rsync -qaH ftp.halifax.rwth-aachen.de::kali-images /srv/mirrors/kali-images/ &
```

### Set Up cron to Manually Mirror ISO Images

The ISO images repository does not use push mirroring so you must schedule a daily rsync run. We provide a bin/mirror-kali-images script, which is ready to use that you can add in the crontab of your dedicated user. You just have to configure etc/mirror-kali-images.conf.

```console
$ sudo su - archvsync
$ cp etc/mirror-kali-images.conf.sample etc/mirror-kali-images.conf
$ vim etc/mirror-kali-images.conf
$ grep -E '^[^#]' etc/mirror-kali-images.conf
TO=/srv/mirrors/kali-images/
$ crontab -e
$ crontab -l
# m h dom mon dow command
39 3 * * * ~/bin/mirror-kali-images
```

Please adjust the precise time so that archive.kali.org doesn't get overloaded by too many mirrors at the same time.

## How to Set Up a Private Kali Linux Mirror

If you want to setup a private mirror, you can use the same tools as for
the public mirror with the following differences:

- you will not be able to use SSH push mirroring for the package repository, instead you have to put `~/bin/ftpsync sync:archive:kali` in the crontab of the user owning the mirror (`archvsync` in the above explanation).
- you must use a non-kali.org mirror as the source mirror, almost all of them offer public rsync access (kali.org servers are restricted)
