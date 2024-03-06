---
title: Kali inside Proxmox (Guest VM)
description:
icon:
weight:
author: ["gamb1t",]
---

{{% notice info %}}
Due to the type of hypervisor Proxmox is we do not have a documentation page on how to install it. However, this can be found through [Proxmox's official page](https://www.proxmox.com/en/proxmox-ve/get-started).
{{% /notice %}}

Proxmox has two ways of accessing a nested environment. The first is through virtualization, using QEMU. The other is through containerization using LXC. We will discuss both methods (for QEMU there are two ways of installation).

## Kali as a Proxmox VM (Virtualization) using prebuilt virtual machine

- Go to [Kali images](/get-kali/#kali-installer-images) and select "Pre-built Virtual Machines"
- Download the proper image (Qemu64 or Qemu32)
- Extract the image from the downloaded file (note that this is [7Zip](https://www.7-zip.org/) format)
- Rename the file to *.iso (i.e. change the file extension from qcow2 to iso). Take a note of the file name (eg. ```kali-linux-2023.4-qemu-amd64.iso```)
- Open Proxmox
- Select the storage to be used (eg. locate correct node and find local) in the left pane
- In the middle pane select ISO images and click Upload
- Click Select File and then select the previously extracted (and renamed) file. Click upload and wait for upload to complete
- Click Create VM:
  - Fill in 'VM ID' and 'Name'. Enable 'Start at boot' if this is wanted. Click Next
  - Select 'Do not use any media'.  Click Next
  - Enable 'Qemu Agent'. Click Next
  - Click on Delete next to the scsi0 disk (will change to No Disks). Click Next
  - Set needed number of CPUs (or leave to default). Clck Next
  - Set memory to 2048 or more. Click Next
  - Set network according to need (or leave to default). Click Next
  - Review configuration and click Finish
- Right click on node and select Shell
- Locate the uploaded file by issuing a ```find / -name {filename}``` (eg. ```find / -name kali-linux-2023.4-qemu-amd64.iso```)
- Change directory to the location of the file
- Rename file from *.iso to *.qcow2 (eg. ```mv kali-linux-2023.4-qemu-amd64.iso kali-linux-2023.4-qemu-amd64.qcow2```)
- Run command ```qm importimage VM-ID {filename.qcow2} {storage}``` (eg. ```qm importimage 108 kali-linux-2023.4-qemu-amd64.qcow2 local-lvm```)
- Close shell
- In Proxmox, locate the created VM
- In the middle pane, click Hardware and then locate the 'Unused disk 0' in the right pane. Click Edit
- Make neccessary changes to the configuration and then click Add
- A new disk has been created so boot order must be adjusted; click Options in the middle pane then click 'Boot Order' in the right pane. Click Edit
- Enable the (only) disk and then change the boot order so that the disk is either #1 or #2 (preceeded by ide2 / CD). Click OK
- Now Kali is ready to start. Please remember that the default credentials "kali/kali" is to be used when logging in (ie. remember to change these)  

## Kali as a Proxmox VM (Virtualization)

We will go over the steps and discuss some of the changes that can be made.

- Download the latest ISO from [get Kali](/get-kali/#kali-installer-images)
- Select "Create VM"
- Assign the VM a name and ID
- Select the ISO we downloaded previously
- We can skip past "System" using the defaults
- Create a disk with at least 20 GiB, you may need more so think about your needs when selecting storage
- Select 2 for both "Sockets" and "Cores" to get our total cores to 4, you may be able to use less or may need more, again think about your needs
- Use at least 2048 MiB
- Be sure to select the proper network device, if you are unsure which to select leave it as default
- Confirm your settings

From here we select the new VM under our node and after selecting "Console" we select "Start Now". From here we can proceed as though it is a [normal install](/docs/installation/hard-disk-install/).

## Kali as a Proxmox CT (Containerization)

We will go over the steps and discuss some of the changes that can be made.

We first need to install the container template to be used. To do this we navigate to [a LXD repo](https://us.lxd.images.canonical.com/images/kali/current/) and determine what our requirements are. For this guide, we have a Proxmox system that is run on amd64 hardware, so we select that. We are going to select default, this will go for all hardware options. Next we select the most recent date. Now we download the "rootfs.tar.xz". We can alternatively copy this link and in Proxmox under "CT Templates" select "Download from URL" and supply this URL and then query it.

- Select "Create CT"
- Assign the container a hostname and a password. If relevant, supply a SSH public key as well.
- Select the template we downloaded previously
- Depending on the use-case of this container, you may want to give the system more storage. A good balance would be to give it 20 GiB for a typical Kali install.
- Up the "Cores" to be 2, you may be able to use less or may need more, think about your needs
- Again, depending on the use case you may want to up the amount of memory. A good balance would be to give it 2048 for a typical Kali install, with 1024 swap.
- Select network settings if necessary, otherwise leave as default.
- If necessary change the DNS settings
- Confirm settings and finish setup

From here we wait for the container to be created and finish with "TASK OK". Once this is complete we can close this window, select the container under our node, select console, and select "Start" on the top bar. We login with "root" user and the password we input earlier.
