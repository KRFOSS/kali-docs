---
title: VMware Tools for a Kali Guest
description:
icon:
date: 2019-10-26
type: post
weight: 15
author: ["g0tmi1k",]
tags: ["",]
keywords: ["",]
og_description:
---

Should you decide to create your own VMware installation of Kali Linux rather than using our [pre-made VMware images](https://www.offensive-security.com/kali-linux-vmware-virtualbox-image-download/), you will need to follow the instructions below in order to successfully install VMware Tools in your Kali installation.

## Installing VMware Tools in Kali Linux Rolling

As of Sept 2015, **VMware [recommends](https://blogs.vmware.com/vsphere/2015/09/open-vm-tools-ovt-the-future-of-vmware-tools-for-linux.html) using the distribution-specific open-vm-tools (OVT)** instead of the VMware Tools package for guest machines. To install open-vm-tools in Kali, first make sure you are fully updated, and then enter the following:

```html
apt update && apt -y full-upgrade

# Reboot now in case you have updated to a new kernel. Once rebooted:
apt -y --reinstall install open-vm-tools-desktop fuse
reboot
```

## Adding Support for Shared Folders When Using OVT

Unfortunately, shared folders will not work out of the box. To enable this feature for your current session, you will need to execute the following script after logging in:

```html
cat <<EOF | sudo tee /usr/local/sbin/mount-shared-folders
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
sudo chmod +x /usr/local/sbin/mount-shared-folders
```

If you wish to make it a little easier, you can add a shortcut to the desktop (and allow the script to be executed upon double clicking if you are you are using GNOME):

```markdown
ln -sf /usr/local/sbin/mount-shared-folders ~/Desktop/mount-shared-folders
gsettings set org.gnome.nautilus.preferences executable-text-activation 'ask'
```

## Restarting OVT

If OVT stops functioning correctly, such as Copy/Paste between host and guest, the following script may help out:

```html
cat <<EOF | sudo tee /usr/local/sbin/restart-vm-tools
#!/bin/sh
systemctl stop run-vmblock\\\\x2dfuse.mount
sudo killall -q -w vmtoolsd
systemctl start run-vmblock\\\\x2dfuse.mount
systemctl enable run-vmblock\\\\x2dfuse.mount
sudo vmware-user-suid-wrapper vmtoolsd -n vmusr 2>/dev/null
sudo vmtoolsd -b /var/run/vmroot 2>/dev/null
EOF
sudo chmod +x /usr/local/sbin/restart-vm-tools
ln -sf /usr/local/sbin/restart-vm-tools ~/Desktop/restart-vm-tools
gsettings set org.gnome.nautilus.preferences executable-text-activation 'ask'
```

## Installing VMware Tools in Older Kali Versions

The latest version of vmware-tools at this date compiles against our kernel, albeit with several warnings. We utilise a set of vmware-tool patches to facilitate the installation.

```markdown
cd ~/
apt install -y git gcc make linux-headers-$(uname -r)
git clone https://github.com/rasa/vmware-tools-patches.git
cd vmware-tools-patches/
```

Next, mount the VMware tools ISO by clicking "Install VMware Tools" from the appropriate menu. Once the VMware Tools ISO has been attached to the virtual machine, copy the installer to the _downloads_ directory and then run the installer script :

```markdown
cd ~/vmware-tools-patches/
cp /media/cdrom/VMwareTools-9.9.0-2304977.tar.gz downloads/
./untar-and-patch-and-compile.sh
```
