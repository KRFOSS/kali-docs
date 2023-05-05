---
title: Import Pre-Made Kali Hyper-V VM
description:
icon:
weight: 216
draft: true
author: ["arnaudr", "gamb1t",]
---

Importing the [Kali Hyper-V image](/get-kali/#kali-virtual-machines) is very easy.

We first need to extract the Hyper-V image. For that we need to use the [official 7z app](https://www.7-zip.org/). Note if we are on Windows 11 the option will be hidden behind the context menu "Show more options".

```
XXX Screenshot with this mysterius Show more options menu maybe?
```

We just unzipped a directory named `kali-linux-<VERSION>-hyperv-amd64`. Then we double-click on the file `install-vm.bat` file. It will start a console, and run a series of steps in order to setup the Kali Linux Virtual Machine. If all goes well, we should see the following screen:

```
XXX Add screenshot that features the powershell console after all steps runs. In the bakground, the screenshot also shows the file explorer with the 3 files that were unpacked
```

We then launch the Hyper-V Manager:

```
XXX Add screenshot of Hyper-V Manager, it should show the Kali Linux VM listed there.
```

From there, all is left to do is to start the Kali Linux VM. Shortly after, we should see this login screen. The default credentials are `kali` and `kali` as usual:

```
XXX One more screenshot, please. I expect the XRDP login screen, not the usual XFCE login screen, so it's important to have a screenshot here.
```

A brief technical note: the Kali Linux Hyper-V image is pre-configured for Enhanced Session Mode, using HVSocket and XRDP under the hood.

## Troubleshooting and extra tips

**Moving the Virtual Hard Disk**

At this point, we have a Kali Linux VM up and running. However, we can't move the Virtual Hard Disk (ie. the file named `kali-linux-<VERSION>-hyperv-amd64.vhdx`) anymore, as it's linked to the Virtual Machine... It seems that Hyper-V usually stores its Virtual Hard Disks at `C:\ProgramData\Microsoft\Windows\Virtual Hard Disks\`. So if we like to keep our computer neat and tidy, we can restart the procedure above, except that we should move the unzipped files to this location *before* running the script `install-vm.bat`.

**Renaming the Virtual Machine**

If, for some reason, we'd like to have 2 Kali VMs side-by-side, we will need to rename one of it. In order to do that, after unzipping the image, we must: 1) Rename the file `.vhdx` to something else, and 2), edit the file `create-vm.ps1` and change the value of the `$Name` variable accordingly. Then move the files in the location where we like to keep our Hyper-V Virtual Disks, and finally run the script `install-vm.bat`.
