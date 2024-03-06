---
title: Updating a Package
description:
icon:
weight:
author: ["deleteduser",]
---

To update a package in Kali Linux, you can use the package management tools provided by Debian, which Kali Linux is based on. The primary package management tool is apt. Regularly updating your system is crucial for security, as updates often include patches for vulnerabilities. You can update the packages in the following ways :

## Updating all Packages

To update all packages on Kali Linux, you can run the following commands in the terminal.

```console
kali@kali:~$ sudo apt update
```
This command updates the packages list.

```console
kali@kali:~$ sudo apt upgrade
```
Upgrades all installed packages to their latest versions.

## Upgrading a specific Package

It is also possible to update a single specific package.

```console
kali@kali:~$ sudo apt install --only-upgrade package_name
```
Replace `package_name` with the name of the package you want to update(for example nmap).This command will install the latest version of the specified package, and effectively updates it. The `--only-upgrade` option ensures that only the selected package is upgraded without attempting to install any new packages. After running this command, the specified package will be updated to the latest version available in the repositories.




