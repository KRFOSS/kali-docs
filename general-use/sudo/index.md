---
title: All about sudo
description:
icon:
date: 2020-02-24
type: post
weight: 6
author: ["gamb1t",]
tags: ["",]
keywords: ["",]
og_description:
---

# Non-root user

With 2020.1 Kali has swapped to a privileged non-root user by default. This means that root has no password set, and the account created during installation is the one to use. It is possible to re-enable access to the root user, however this is not recommended.

# Sudo?

`sudo` is a way that we can access tools, ports, or services that need administrative privileges. Sudo is powerful however, and can allow full access to the system, so it is not advised to use `sudo` on every command.

### Sudo on Kali

Because Kali creates a user with administrative privileges by default, users can use `sudo` right away and supply their password for authentication. Should a user wish to enable password-less `sudo`, which poses a security risk should someone gain access the the user account, they have that option.

```markdown
sudo apt install -y kali-grant-root && sudo dpkg-reconfigure kali-grant-root
```

The previous command installs a package that will allow for a user to be added to a trusted group that will not need to supply a password when using `sudo`. This does not mean however that root will be reinstated.

### In use

```markdown
kali@kali:~$ ls /root
ls: cannot open directory '/root': Permission denied
kali@kali:~$
kali@kali:~$ sudo ls /root
[sudo] password for kali:
hello
kali@kali:~$ sudo apt install -y kali-grant-root && sudo dpkg-reconfigure kali-grant-root
...SNIP...
kali@kali:~$
kali@kali:~$ sudo ls /root
hello
kali@kali:~$
```
