---
title: VMware Tools for a Kali Guest
description:
icon:
date: 2020-03-20
type: post
weight:
author: ["g0tmi1k",]
tags: ["",]
keywords: ["",]
og_description:
---

Should you decide to create your own VMware installation of Kali Linux rather than using our [pre-made VMware images](https://www.offensive-security.com/kali-linux-vmware-virtualbox-image-download/), you will need to follow the instructions below in order to successfully install VMware Tools in your Kali installation.

## Installing VMware Tools in Kali Linux Rolling

As of Sept 2015, **VMware [recommends](https://blogs.vmware.com/vsphere/2015/09/open-vm-tools-ovt-the-future-of-vmware-tools-for-linux.html) using the distribution-specific open-vm-tools (OVT)** instead of the VMware Tools package for guest machines. To install open-vm-tools in Kali, first make sure you are fully updated:

```html
kali@kali:~$ sudo apt update
kali@kali:~$
kali@kali:~$ sudo apt full-upgrade -y
kali@kali:~$
kali@kali:~$ [ -f /var/run/reboot-required ] && sudo reboot -f
kali@kali:~$
```

Then enter the following:

```
kali@kali:~$ sudo apt install -y --reinstall open-vm-tools-desktop fuse
kali@kali:~$
kali@kali:~$ sudo reboot -f
kali@kali:~$
```

## Adding Support for Shared Folders When Using OVT

Unfortunately, shared folders will not work out of the box. To enable this feature for your current session, you will need to execute the following script after logging in:

```html
kali@kali:~$ cat <<EOF | sudo tee /usr/local/sbin/mount-shared-folders
#!/bin/sh
vmware-hgfsclient | while read folder; do
  vmwpath="/mnt/hgfs/\${folder}"
  echo "[i] Mounting \${folder}   (\${vmwpath})"
  sudo mkdir -p "\${vmwpath}"
  sudo umount -f "\${vmwpath}" 2>/dev/null
  sudo vmhgfs-fuse -o allow_other -o auto_unmount ".host:/\${folder}" "\${vmwpath}"
done
sleep 2s
EOF
kali@kali:~$
kali@kali:~$ sudo chmod +x /usr/local/sbin/mount-shared-folders
kali@kali:~$
```

## Restarting OVT

If OVT stops functioning correctly, such as Copy/Paste between host and guest, the following script may help out:

```html
kali@kali:~$ cat <<EOF | sudo tee /usr/local/sbin/restart-vm-tools
#!/bin/sh

systemctl stop run-vmblock\\\\x2dfuse.mount
killall -q -w vmtoolsd
systemctl start run-vmblock\\\\x2dfuse.mount
systemctl enable run-vmblock\\\\x2dfuse.mount
vmware-user-suid-wrapper vmtoolsd -n vmusr 2>/dev/null
vmtoolsd -b /var/run/vmroot 2>/dev/null
EOF
kali@kali:~$
kali@kali:~$ sudo chmod +x /usr/local/sbin/restart-vm-tools
kali@kali:~$
```

Afterwards,

```
kali@kali:~$ sudo restart-vm-tools
```

- - -

For older versions of Kali Linux, here is our [previous guide](/docs/virtualization/install-vmware-tools-kali-guest-legacy/).
