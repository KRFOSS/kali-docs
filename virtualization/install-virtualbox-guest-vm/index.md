---
title: Installing Kali inside VirtualBox (Guest VM)
description:
icon:
date: 2020-09-17
type: post
weight:
author: ["g0tmi1k",]
tags: ["",]
keywords: ["",]
og_description:
---

This guide is about virtualizing Kali Linux inside of [VirtualBox](https://www.virtualbox.org/), allowing you to have a Kali VM. This is a great way to use Kali, as it is completely separate from the host, allows you to interact with other VMs (as well as the host machine and other machines on the network), and allows you to revert to snapshots.

You may wish to follow our other guide if you are trying to install VitualBox on Kali Linux (as a [host](/docs/virtualization/install-virtualbox-kali-host/)).

The guide below is what we use to generate our [pre-made Kali Linux VitualBox images](https://www.offensive-security.com/kali-linux-vm-virtualbox-virtualbox-image-download/). You may alter this to your needs. We always generate the images using the [latest version of VitualBox](https://www.virtualbox.org/wiki/Downloads).

Upon starting up VitualBox, select "New" (Machine -> New).

![](vb-01.png)



The next screen is "Name and operating system" which is where you name the VM. This name is also used in any filenames (such as the configuration, hard disk and snapshot - which isn't changed from this point).

We are keeping it generic in this guide (as Kali is a rolling distribution, and we update it), however for our releases, we use the version number in the name as it is a fixed release _(`Kali-Linux-YYYY.N-vbox-ARCH`. Example: `Kali-Linux-2020.3-vbox-amd64`)_.

For the "Type", we set it as `Linux`. For the "Version", we are going to be using the x64 desktop image, so we are going to select `Debian (64-bit)`.

![](vb-02.png)



"Memory size" is the next section, where we can define how much RAM to use. Again, the higher the amount of RAM, the more applications can be open and at increased performance. Various tools inside of Kali can be demanding of resources. When we make the general VMs, we select `2048 MB` (2GB) for RAM, but we often increase this for our personal machines as we have high-performing devices with spare RAM which Kali can utilize.

![](vb-03.png)



This screen below, "Hard disk", allows us to `Create a new virtual disk now`.

![](vb-04.png)


For the "Hard disk file type", we select `VDI (VirtualBox Disk Image)` (and its the default option).

![](vb-05.png)



For the following screen, "Storage on physical hard disk", we go with the default option of `Dynamically allocated`.

![](vb-06.png)



Now with "File location and size", we can now define how large the virtual hard disk will be. We use `80.00 GB` for our VMs.

![](vb-07.png)



After clicking on "Create", the wizard is complete.

Now we click on "Settings", to customize the VM further.

![](vb-08.png)



In "General" -> "Advanced", we make sure to set "Shared Clipboard" to `bidirectional`, as well as "Drag'n'Drop" to `bidirectional`

![](vb-09.png)



In "System" -> "Motherboard", we change the "Boot Order" to make sure `Hard Disk` is top and `Optical` is the second. Everything else is disabled.

![](vb-10.png)



In "System" -> "Processor", we increase the "Processor(s)" to be `2`.

At the same time, we also enable "Extended Features" for `Enable PAE/NX`.

![](vb-11.png)



In "Display" -> "Screen", we make sure to have "Video Memory" set to `128 MB`

Another item to point out is to make sure that "Accelerated 3D graphics" is **disabled**, as people have reported that causes issues.

![](vb-12.png)



The final settings view looks like the following:

![](vb-13.png)



When we are ready to go, press "Start".

The first time we run it, we will get a prompt saying do we wish to mount an image to use as a "start-up disk". We want to use our Kali image, rather than a physical drive, so we select the icon to the side of the drop down.

![](vb-14.png)



A new pop up will open, "Optical Disk Selector". We will now press "Add", then navigate to where our ISO is located.

![](vb-15.png)



After pressing "Open", we can see its been added, so we make sure its selected and press "Choose".

![](vb-16.png)



All that is left now to do is press "Start".

![](vb-17.png)



We now let the VM finish starting up, and then continue installing Kali as we normally would on a bare metal install. Afterwards, we would install the extra part [Guest Addition](/docs/virtualization/install-virtualbox-guest-additions-kali/), to make sure the guest VM is aware of it being in a virtual environment and have better integration.
