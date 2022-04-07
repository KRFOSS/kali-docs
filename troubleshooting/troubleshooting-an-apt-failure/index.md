---
title: Troubleshooting APT Upgrade Failures
description:
icon:
weight:
author: ["gamb1t",]
---

If you run into an issue with running `apt upgrade` where a package is not going to be installed, there are a couple ways to fix it.

The first way to fix this issue is to install the package manually and then run the command again.
The second way to fix this is to run `apt full-upgrade` instead, which will be sure to install the proper version.
