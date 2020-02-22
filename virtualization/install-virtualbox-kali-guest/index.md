---
title: VirtualBox for a Kali Guest
description:
icon:
date: 2020-01-16
type: post
weight: 25
author: ["g0tmi1k",]
tags: ["",]
keywords: ["",]
og_description:
---

If you run Kali Linux as a "guest" within VirtualBox, this article will help you to successfully install the  "Guest Addition" tools.
You must use version **4.2.xx or higher** of VirtualBox in order to take advantage of the improvements, including compatibility updates, and enhanced stability of both the core application and the Guest Additions.

## Installing VirtualBox Guest Additions in Kali Linux

The VirtualBox Guest Additions provide proper mouse and screen integration, as well as folder sharing, with your host operating system. To install them, proceed as follows.

Start up your Kali Linux virtual machine, open a terminal window and issue the following commands.

```markdown
sudo apt update
sudo apt install -y virtualbox-guest-x11
reboot
```

![virtualbox-tools](virtualbox-tools.png)

## Installing VirtualBox Guest Additions in Older Kali Versions

Start up your Kali Linux virtual machine, open a terminal window and issue the following command to install the Linux kernel headers.

```markdown
sudo apt update && sudo apt install -y linux-headers-$(uname -r)
```

Once this is complete you can now attach the "Guest Additions" CD-ROM image. Select "Devices" from the VirtualBox menu and then select "Install Guest Additions". This will mount the Guest Additions ISO in the virtual CD drive in your Kali Linux virtual machine. When prompted to autorun the CD, click the Cancel button.

![VirtualBox-Cancel-Auto-Run](Figure-17-Cancel-Auto-Run.png)

From a terminal window, copy the VboxLinuxAdditions.run file from the Guest Additions CD-ROM to a path on your local system. Ensure it is executable and run the file to begin the installation.

```markdown
cp /media/cd-rom/VBoxLinuxAdditions.run /$HOME/
chmod 755 /$HOME/VBoxLinuxAdditions.run
cd /$HOME
./VBoxLinuxAdditions.run
```

![VirtualBox-VBox-Additions-Install](Figure-19-VBox-Additions-Install.png)

Reboot the Kali Linux VM to complete the Guest Additions installation. You should now have full mouse and screen integration as well as the ability to share folders with the host system.

## Creating Shared Folders with the Host System

This section explains how to share folders on your host system with your Kali Linux VirtualBox "guest".

From the VirtualBox Manager, select your Kali Linux VM instance and click on the "Shared Folders" link in the right window pane. This will launch a pop up window for adding shared folders. Within this window click the "Add Folders" icon.

In the Folder Path text box, provide the path to the folder you would like to share, or click the drop-down arrow to browse your host system for the path to the folder. Select the check boxes that allow for 'Auto-mount' and 'Make Permanent' and click the "OK" button both times when prompted.

![Figure-20-Shared-folder-config](Figure-20-Shared-folder-config.png)

Your shared folders will now be available in the media directory. You can create a bookmark or link for easier access to the directory.

![VirtualBox-Shared-folder-in-Kali](Figure-21-Shared-folder-in-Kali.png)
