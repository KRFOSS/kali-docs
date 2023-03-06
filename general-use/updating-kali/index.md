---
title: Updating Kali
description:
icon:
weight:
author: ["gamb1t",]
---

# When should you update Kali?

If you have a default installation of Kali, you should be checking for updates every few weeks. If you need a new version of a tool, or hear about a security update, that may speed up the timetable. However, a good practice is that you ensure all tools work before an engagement and during that engagement you should not update. As Kali is a rolling release, once in a while issues will sneak into rolling and may break a tool that is needed.

If you are using [last-snapshot](/docs/general-use/kali-branches/), you will not receive updates until we release the next version of Kali for that year. For this reason, it is a good idea to follow the [Kali Twitter](https://twitter.com/kalilinux), or check the [Kali website](/) every few months. Kali has a release four times a year, and follows a loose quarterly schedule.

# How to update Kali?

To update Kali, first ensure that `/etc/apt/sources.list` is properly populated:

```console
kali@kali:~$ cat /etc/apt/sources.list
# See https://www.kali.org/docs/general-use/kali-linux-sources-list-repositories/
deb http://http.kali.org/kali kali-rolling main contrib non-free non-free-firmware

# Additional line for source packages
# deb-src http://http.kali.org/kali kali-rolling main contrib non-free non-free-firmware
kali@kali:~$
```

After that we can run the following commands which will [upgrade us to the latest Kali version](/docs/general-use/updating-kali/):

```console
kali@kali:~$ sudo apt update
kali@kali:~$
kali@kali:~$ sudo apt full-upgrade -y
kali@kali:~$
```
