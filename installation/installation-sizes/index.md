---
title: Kali Installation Sizes
description:
icon:
weight:
author: ["gamb1t",]
---

Kali has a lot of customization that can be done during the package selection part of installation. Specifically, there are a total of 20 ways to configure your system during package selection. To help give an idea of what storage size someone should look to have for their preferred packages, we have created this documentation reference page. In general, **a disk size of about 60GB** will allow for any installation and provide a bit of extra storage for use. If you want a more specific answer, then keep reading!

For this page we are going to break down the installations into five sections, each representing what tool metapackages are selected. In each section, we are also going to list out all four desktop environment choices.

## No Tools selected

### No DE - 1.8G

```console
┌──(kali㉿kali)-[~]
└─$ df -h
Filesystem      Size  Used Avail Use% Mounted on
udev            924M     0  924M   0% /dev
tmpfs           193M  748K  193M   1% /run
/dev/sda1        97G  1.8G   91G   2% /
tmpfs           965M     0  965M   0% /dev/shm
tmpfs           5.0M     0  5.0M   0% /run/lock
tmpfs           193M  8.0K  193M   1% /run/user/1000
```
### Xfce - 4.1G

```console
┌──(kali㉿kali)-[~]
└─$ df -h
Filesystem      Size  Used Avail Use% Mounted on
udev            925M     0  925M   0% /dev
tmpfs           193M  1.2M  192M   1% /run
/dev/sda1        97G  4.1G   88G   5% /
tmpfs           965M     0  965M   0% /dev/shm
tmpfs           5.0M     0  5.0M   0% /run/lock
tmpfs           193M  120K  193M   1% /run/user/1000
```
### KDE - 5.5G

```console
┌──(kali㉿kali)-[~]
└─$ df -h
Filesystem      Size  Used Avail Use% Mounted on
udev            924M     0  924M   0% /dev
tmpfs           193M  1.3M  192M   1% /run
/dev/sda1        97G  5.5G   87G   6% /
tmpfs           965M     0  965M   0% /dev/shm
tmpfs           5.0M     0  5.0M   0% /run/lock
tmpfs           193M   96K  193M   1% /run/user/1000
```
### GNOME - 4.4G

```console
┌──(kali㉿kali)-[~]
└─$ df -h
Filesystem      Size  Used Avail Use% Mounted on
udev            924M     0  924M   0% /dev
tmpfs           193M  1.5M  192M   1% /run
/dev/sda1        97G  4.4G   88G   5% /
tmpfs           965M     0  965M   0% /dev/shm
tmpfs           5.0M     0  5.0M   0% /run/lock
tmpfs           193M   84K  193M   1% /run/user/111
tmpfs           193M  124K  193M   1% /run/user/1000
```

## Top 10

### No DE - 5.2G

```console
┌──(kali㉿kali)-[~]
└─$ df -h
Filesystem      Size  Used Avail Use% Mounted on
udev            925M     0  925M   0% /dev
tmpfs           193M  772K  193M   1% /run
/dev/sda1        97G  5.2G   87G   6% /
tmpfs           965M     0  965M   0% /dev/shm
tmpfs           5.0M     0  5.0M   0% /run/lock
tmpfs           193M   12K  193M   1% /run/user/1000
```
### Xfce - 6.9G

```console
┌──(kali㉿kali)-[~]
└─$ df -h
Filesystem      Size  Used Avail Use% Mounted on
udev            925M     0  925M   0% /dev
tmpfs           193M  1.3M  192M   1% /run
/dev/sda1        97G  6.9G   86G   8% /
tmpfs           965M     0  965M   0% /dev/shm
tmpfs           5.0M     0  5.0M   0% /run/lock
tmpfs           193M  112K  193M   1% /run/user/111
tmpfs           193M  120K  193M   1% /run/user/1000
```
### KDE - 8.2G

```console
┌──(kali㉿kali)-[~]
└─$ df -h
Filesystem      Size  Used Avail Use% Mounted on
udev            925M     0  925M   0% /dev
tmpfs           193M  1.2M  192M   1% /run
/dev/sda1        97G  8.2G   84G   9% /
tmpfs           965M     0  965M   0% /dev/shm
tmpfs           5.0M     0  5.0M   0% /run/lock
tmpfs           193M   84K  193M   1% /run/user/112
tmpfs           193M   96K  193M   1% /run/user/1000
```
### GNOME - 7.2G

```console
┌──(kali㉿kali)-[~]
└─$ df -h
Filesystem      Size  Used Avail Use% Mounted on
udev            925M     0  925M   0% /dev
tmpfs           193M  1.4M  192M   1% /run
/dev/sda1        97G  7.2G   85G   8% /
tmpfs           965M     0  965M   0% /dev/shm
tmpfs           5.0M     0  5.0M   0% /run/lock
tmpfs           193M   84K  193M   1% /run/user/112
tmpfs           193M  120K  193M   1% /run/user/1000
```

## Top 10, Default

### No DE - 12G

```console
┌──(kali㉿kali)-[~]
└─$ df -h
Filesystem      Size  Used Avail Use% Mounted on
udev            924M     0  924M   0% /dev
tmpfs           193M  1.1M  192M   1% /run
/dev/sda1        97G   12G   81G  14% /
tmpfs           965M     0  965M   0% /dev/shm
tmpfs           5.0M     0  5.0M   0% /run/lock
tmpfs           193M   20K  193M   1% /run/user/1000
```
### Xfce - 14G

```console
┌──(kali㉿kali)-[~]
└─$ df -h
Filesystem      Size  Used Avail Use% Mounted on
udev            924M     0  924M   0% /dev
tmpfs           193M  1.3M  192M   1% /run
/dev/sda1        97G   14G   79G  15% /
tmpfs           965M     0  965M   0% /dev/shm
tmpfs           5.0M     0  5.0M   0% /run/lock
tmpfs           193M  116K  193M   1% /run/user/126
tmpfs           193M  124K  193M   1% /run/user/1000
```
### KDE - 15G

```console
┌──(kali㉿kali)-[~]
└─$ df -h
Filesystem      Size  Used Avail Use% Mounted on
udev            924M     0  924M   0% /dev
tmpfs           193M  1.2M  192M   1% /run
/dev/sda1        97G   15G   78G  16% /
tmpfs           965M     0  965M   0% /dev/shm
tmpfs           5.0M     0  5.0M   0% /run/lock
tmpfs           193M  134K  193M   1% /run/user/1000
```
### GNOME - 14G

```console
┌──(kali㉿kali)-[~]
└─$ df -h
Filesystem      Size  Used Avail Use% Mounted on
udev            924M     0  924M   0% /dev
tmpfs           193M  1.5M  192M   1% /run
/dev/sda1        97G   14G   79G  15% /
tmpfs           965M     0  965M   0% /dev/shm
tmpfs           5.0M     0  5.0M   0% /run/lock
tmpfs           193M   92K  193M   1% /run/user/128
tmpfs           193M  128K  193M   1% /run/user/1000
```

## Top 10, Default, Large

### No DE - 19G

```console
┌──(kali㉿kali)-[~]
└─$ df -h
Filesystem      Size  Used Avail Use% Mounted on
udev            924M     0  924M   0% /dev
tmpfs           193M  1.1M  192M   1% /run
/dev/sda1        97G   19G   74G  22% /
tmpfs           965M     0  965M   0% /dev/shm
tmpfs           5.0M     0  5.0M   0% /run/lock
tmpfs           193M   28K  193M   1% /run/user/1000
```
### Xfce - 20G

```console
┌──(kali㉿kali)-[~]
└─$ df -h
Filesystem      Size  Used Avail Use% Mounted on
udev            924M     0  924M   0% /dev
tmpfs           193M  1.2M  192M   1% /run
/dev/sda1        97G   20G   73G  22% /
tmpfs           965M     0  965M   0% /dev/shm
tmpfs           5.0M     0  5.0M   0% /run/lock
tmpfs           193M  128K  193M   1% /run/user/1000
```
### KDE - 22G

```console
┌──(kali㉿kali)-[~]
└─$ df -h
Filesystem      Size  Used Avail Use% Mounted on
udev            924M     0  924M   0% /dev
tmpfs           193M  1.3M  192M   1% /run
/dev/sda1        97G   22G   71G  23% /
tmpfs           965M     0  965M   0% /dev/shm
tmpfs           5.0M     0  5.0M   0% /run/lock
tmpfs           193M  104K  193M   1% /run/user/1000
```
### GNOME - 21G

```console
┌──(kali㉿kali)-[~]
└─$ df -h
Filesystem      Size  Used Avail Use% Mounted on
udev            924M     0  924M   0% /dev
tmpfs           193M  1.5M  192M   1% /run
/dev/sda1        97G   21G   72G  22% /
tmpfs           965M     0  965M   0% /dev/shm
tmpfs           5.0M     0  5.0M   0% /run/lock
tmpfs           193M  132K  193M   1% /run/user/1000
```

## Top 10, Default, Large, Everything

### No DE - 33G

```console
┌──(kali㉿kali)-[~]
└─$ df -h
Filesystem      Size  Used Avail Use% Mounted on
udev            939M     0  939M   0% /dev
tmpfs           193M  1.1M  192M   1% /run
/dev/sda1        97G   33G   60G  36% /
tmpfs           965M     0  965M   0% /dev/shm
tmpfs           5.0M     0  5.0M   0% /run/lock
tmpfs           193M   28K  193M   1% /run/user/1000
```
### Xfce - 34G

```console
┌──(kali㉿kali)-[~]
└─$ df -h
Filesystem      Size  Used Avail Use% Mounted on
udev            923M     0  923M   0% /dev
tmpfs           193M  1.3M  192M   1% /run
/dev/sda1        97G   34G   59G  37% /
tmpfs           965M     0  965M   0% /dev/shm
tmpfs           5.0M     0  5.0M   0% /run/lock
tmpfs           193M  132K  193M   1% /run/user/1000
```
### KDE - 35G

```console
┌──(kali㉿kali)-[~]
└─$ df -h
Filesystem      Size  Used Avail Use% Mounted on
udev            923M     0  923M   0% /dev
tmpfs           193M  1.3M  192M   1% /run
/dev/sda1        97G   35G   58G  37% /
tmpfs           965M     0  965M   0% /dev/shm
tmpfs           5.0M     0  5.0M   0% /run/lock
tmpfs           193M  132K  193M   1% /run/user/1000
```
### GNOME - 34G

```console
┌──(kali㉿kali)-[~]
└─$ df -h
Filesystem      Size  Used Avail Use% Mounted on
udev            923M     0  923M   0% /dev
tmpfs           193M  1.3M  192M   1% /run
/dev/sda1        97G   34G   59G  37% /
tmpfs           965M     0  965M   0% /dev/shm
tmpfs           5.0M     0  5.0M   0% /run/lock
tmpfs           193M  132K  193M   1% /run/user/1000
```
