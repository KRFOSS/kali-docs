---
title: Kernel Configuration
description:
icon:
weight:
author: ["arnaudr", "gamb1t",]
---

The Kali Linux kernel differs slightly from the "usual" kernel. For the purposes of penetration testing, we chose to use some defaults that differ from general-purpose Linux distributions, and we also patch the kernel here and there. This page will document these changes.

{{% notice info %}}
Default values can be modified via the tool `kali-tweaks`, but patches can't be undone without generating a new kernel.
{{% /notice %}}

Beside each header will determine if the configuration is a kernel patch or if the behavior was modified by a file. If you would like to explore these kernel patches for yourself, you can find them [here, on our GitLab](https://gitlab.com/kalilinux/packages/linux/-/tree/kali/master/debian/patches/features/all).

### WiFi injection (Patch)

We supply a [WiFi injection patch](https://gitlab.com/kalilinux/packages/linux/-/blob/kali/master/debian/patches/features/all/kali-wifi-injection.patch) to help with WiFi-based penetration testing.

### dmesg unrestricted (File)

The kernel logs are unrestricted by default, meaning that any unprivileged user can run the command `dmesg` and inspect the kernel logs (also called the "kernel ring buffer"). If you would prefer `dmesg` to remain a privileged command, you can use `kali-tweaks` to restore this behavior.

### Privileged ports (File)

Traditionally, IPv4 ports below 1024 are called "privileged ports," and can only be used by the privileged user. In Kali Linux, this is disabled by default, meaning that all ports are "unprivileged," or in other words any user can run a program that binds to any port, even below 1024.

This was done as Kali is no longer root user by default, and with a non-root user some tools, such as netcat or Metasploit, may be trying to use ports that would require privilege. This should cut-down on the need to run `sudo` as often. If you would like to leave ports 1024 and below as privileged, this can be done through `kali-tweaks`.

### WiFi driver support (Patch)

- [Enable sniffer mode for carl9170 cards](https://gitlab.com/kalilinux/packages/linux/-/blob/kali/master/debian/patches/features/all/wireless-carl9170-Enable-sniffer-mode-promisc-flag-t.patch)
- [Add support for af9005 cards](https://gitlab.com/kalilinux/packages/linux/-/blob/kali/master/debian/patches/features/all/drivers-media-dvb-usb-af9005-request_firmware.patch)
- [Adds touchscreen support for Odroid VU7 devices](https://gitlab.com/kalilinux/packages/linux/-/blob/kali/master/debian/patches/features/all/dwav-usb-mt-driver.patch)