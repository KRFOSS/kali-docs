---
title: Installing VMware Tools (Guest Tools)
description:
icon:
type: post
weight: 300
author: ["g0tmi1k",]
---

Installing "Guest Tools", gives a better user experience with VMware VMs. This is why since Kali Linux 2019.3, during the [setup process](https://gitlab.com/kalilinux/build-scripts/live-build-config/-/blob/master/simple-cdd/profiles/offline.downloads) it should **detect if Kali Linux is inside a VM**. If it is, then **automatically install any additional tools** (in VMware case, `open-vm-tool`).

As of September 2015, **VMware [recommends](https://blogs.vmware.com/vsphere/2015/09/open-vm-tools-ovt-the-future-of-vmware-tools-for-linux.html) using the distribution-specific [open-vm-tools](https://packages.debian.org/testing/open-vm-tools) (OVT)** instead of the VMware Tools package for guest machines.

## Open-VM-Tools

Should you decide to [create your own VMware installation of Kali Linux](/docs/virtualization/install-vmware-guest-vm/) _(rather than using our [pre-made VMware images](https://www.offensive-security.com/kali-linux-vmware-virtualbox-image-download/))_, and you want to force a manual reinstall of `open-vm-tools` (as something has gone wrong), first make sure you are [fully updated](/docs/general-use/updating-kali/), then enter the following.

```markdown
kali@kali:~$ sudo apt update
...
kali@kali:~$
kali@kali:~$ sudo apt install -y --reinstall open-vm-tools-desktop fuse
...
kali@kali:~$
kali@kali:~$ sudo reboot -f
kali@kali:~$
```

## Adding Support for Shared Folders When Using OVT

Unfortunately, shared folders will not work out of the box. To enable this feature for your current session, you will need to execute the following script after logging in.

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

- - -

All you then need todo is run.

```
kali@kali:~$ sudo mount-shared-folders
```

And with a bit of luck, checking `/mnt/hgfs/` you should see your shared folders.

## Restarting OVT

Some time to time, its not uncommon for OVT to stops functioning correctly (e.g. such as copy/paste between the host OS and guest VM stops working).

By creating the following script, and then calling it when there is trouble, should fix a some issues.

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

Afterwards just need to call it.

```markdown
kali@kali:~$ sudo restart-vm-tools
...
kali@kali:~$
```

- - -

For older versions of Kali Linux, here is our [previous guide](/docs/virtualization/install-vmware-guest-tools-legacy/).
