---
title: Kali Linux Network Service Policy
description:
icon:
weight:
author: ["g0tmi1k",]
---

Kali Linux is a penetration testing toolkit, and may potentially be used in "hostile" environments. Accordingly, Kali Linux deals with network services in a very different way than typical Linux distributions. Specifically, Kali _does not enable any externally-listening services by default_ with the goal of minimizing exposure when in a default state.

#### Default Disallow Policy

Kali Linux, as a standard policy, will _disallow network services from persisting across reboots by default_.
The following example can be seen when attempting to install a tool which would by default would start a network proxy service on TCP port 3142:

```console
kali@kali:~$ sudo apt install -y apt-cacher-ng
[...]
Setting up apt-cacher-ng (0.7.11-1) ...
update-rc.d: We have no instructions for the apt-cacher-ng init script.
update-rc.d: It looks like a network service, we disable it.
[...]
kali@kali:~$
```

Notice how the update-rc.d script disallowed persistence of the apt-cacher-ng daemon by default.

#### Overriding the Default Policy

In certain situations, you may actually want certain services to persist over reboots. To allow for this, you can enable a service to persist through reboots using the systemctl command as follows:

```console
kali@kali:~$ sudo systemctl enable apt-cacher-ng
Synchronizing state of apt-cacher-ng.service with SysV service script with /lib/systemd/systemd-sysv-install.
Executing: /lib/systemd/systemd-sysv-install enable apt-cacher-ng
insserv: warning: current start runlevel(s) (empty) of script `apt-cacher-ng' overrides LSB defaults (2 3 4 5).
insserv: warning: current stop runlevel(s) (0 1 2 3 4 5 6) of script `apt-cacher-ng' overrides LSB defaults (0 1 6).
```

#### Service allowlists and blocklists

Service allowlists and blocklists can be found in the **/usr/sbin/update-rc.d** file. You can edit this file to explicitly allow or deny services the ability to automatically start up at boot time:

```console
kali@kali:~$ tail -95 /usr/sbin/update-rc.d | more
[...]
__DATA__
#
# List of blocklisted init scripts
#
apache2 disabled
avahi-daemon disabled
bluetooth disabled
cups disabled
dictd disabled
ssh disabled
[...]
#
# List of allowlisted init scripts
#
acpid enabled
acpi-fakekey enabled
acpi-support enabled
alsa-utils enabled
anacron enabled
[...]
```
