---
title: Kali Linux Root User Policy
description:
icon:
type: archived
weight:
author: ["g0tmi1k",]
---

**This page is dated**. You can find the **latest version** here: [User Policy](/docs/policy/kali-linux-user-policy/).

Most Linux distributions, quite sensibly, encourage the use of a non-privileged account while running the system and use a utility like sudo when and if escalation of privileges in needed. This is sound security advice: this provides an extra layer of protection between the user and any potentially disruptive or destructive operating system commands or operations. This is especially true for multiple user systems, where user privilege separation is a requirement - misbehavior by one user can disrupt or destroy the work of many users.

Kali Linux, however, as a security and auditing platform, contains many which tools can only run with root privileges. Further, Kali Linux's nature makes its use in a multi-user environment highly unlikely.

For these reasons, the default Kali user is "root", and no non-privileged user is created as a part of the installation process. This is one reason that [Kali Linux is not recommended for use by Linux beginners](/docs/introduction/should-i-use-kali-linux/) who might be more apt to make destructive mistakes while running with root privileges.
