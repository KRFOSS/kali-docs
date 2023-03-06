---
title: Installing VMware Tools (Guest Tools)
description:
icon:
weight: 300
author: ["g0tmi1k",]
---

Installing "Guest Tools", gives a better user experience with VMware VMs. This is why since Kali Linux 2019.3, during the [setup process](https://gitlab.com/kalilinux/build-scripts/live-build-config/-/blob/master/simple-cdd/profiles/offline.downloads) it should **detect if Kali Linux is inside a VM**. If it is, then **automatically install any additional tools** (in VMware case, `open-vm-tools` and `open-vm-tools-desktop`). The Guest Tools are also pre-installed in the Live image since Kali Linux 2021.3.

As of September 2015, **VMware [recommends](https://blogs.vmware.com/vsphere/2015/09/open-vm-tools-ovt-the-future-of-vmware-tools-for-linux.html) using the distribution-specific [open-vm-tools](https://packages.debian.org/testing/open-vm-tools) (OVT)** instead of the VMware Tools package for guest machines.


## Open-VM-Tools

Should you decide to [create your own VMware installation of Kali Linux](/docs/virtualization/install-vmware-guest-vm/) _(rather than using our [pre-made VMware images](/get-kali/#kali-virtual-machines))_, and you want to force a manual reinstall of `open-vm-tools` (as something has gone wrong), first make sure you are [fully updated](/docs/general-use/updating-kali/), then enter the following:

```console
kali@kali:~$ sudo apt update
[...]
kali@kali:~$
kali@kali:~$ sudo apt install -y --reinstall open-vm-tools-desktop fuse
[...]
kali@kali:~$
kali@kali:~$ sudo reboot -f
kali@kali:~$
```


## Adding Support for Shared Folders When Using OVT

Unfortunately, shared folders will not work out of the box, some additional scripts are needed. Those can be installed easily with `kali-tweaks`:

```console
kali@kali:~$ kali-tweaks
```

In the Kali Tweaks menu, select *Virtualization*, then *Install additional packages and scripts for VMware*. Congratulations, you now have two additional tools in your toolbox!

The first one is a little script to **mount the VMware Shared Folders**. Invoke it with:

```console
kali@kali:~$ sudo mount-shared-folders
```

And with a bit of luck, checking `/mnt/hgfs/` you should see your shared folders.

The second script is a helper to **restart the VM tools**. Indeed, it's not uncommon for OVT to stops functioning correctly (e.g. such as copy/paste between the host OS and guest VM stops working). In this case, running this script can help to fix the issues:

```console
kali@kali:~$ sudo restart-vm-tools
```

- - -

For older versions of Kali Linux, here is our [previous guide](/docs/virtualization/install-vmware-guest-tools-legacy/).
