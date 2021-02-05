---
title: Packages That Behave Differently With Non-root
description:
icon:
type: post
weight:
author: ["gamb1t",]
---

There are many packages that require privileged access to use. There are also packages that may run without privileged access but lose some of their functionality. This page will be continually updated to include any tools that fall under the latter.


### Nmap

`Nmap` is one of the most common tools that may have issues running non-root vs root. Nmap has many different [scanning techniques](https://nmap.org/book/man-port-scanning-techniques.html) that have a different function and utilize different methods to get results. One example of this is with the `TCP SYN` scan which utilizes raw sockets which are only available to the root user. This scan is the default when you are a privileged user, otherwise the `TCP connect` scan is the default.

The difference between these two scans is well explained in the official Nmap port scanning techniques page. A short breakdown is that the `SYN` scan ends the handshake early, while the `connect` scan completes it completely. Each has their own benefits or setbacks, so it is good to understand what is happening with each.
