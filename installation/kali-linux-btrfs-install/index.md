---
title: BTRFS Install
description:
icon:
date: 2020-02-22
type: post
weight: 90
author: ["re4son",]
tags: ["",]
keywords: ["",]
og_description:
---

## Content:

- [Overview](#overview)
- [Installation steps](#kali-linux-btrfs-installation-steps)
- [Usage](#usage)

## Overview

#### We are going to install Kali Linux to automatically create file system snapshots during apt operations so we can rollback the system after botched upgrades.

[Btrfs](https://btrfs.wiki.kernel.org/index.php/Main_Page) is a modern copy on write (CoW) filesystem for Linux aimed at implementing advanced features such as pooling, snapshots, checksums, and integrated multi-device spanning.
In particular, the [snapshot](https://btrfs.wiki.kernel.org/index.php/UseCases#Snapshots_and_subvolumes) support is what makes Btrfs attractive for Kali installations on bare metal. Virtualization solutions such as VMWare and Virtualbox provide their own snapshotting functionality and using btrfs in those environments is not really required.

The snapshotting strategy of this walkthrough centres around a tool called "apt-btrfs-snapshot" from the Ubuntu repositories,
which is a wrapper around "apt". This wrapper transparently hooks into the apt workflow and automatically creates snapshots before and after any apt operation.
This neat little feature allows to easily rollback a system after a botched upgrade.

Snapper is another useful utility to create snapshots. We are preparing the Kali system for the use of snapper by creating a separate subvolume for its snapshots but we are not
including the installation and usage of snapper in this walkthrough.
Details about snapper can be found on the following website:
[http://snapper.io/](http://snapper.io/)



### Installation Overview

Installing Kali Linux with snapshotting functionality is very similar to a standard installation with the following exceptions:

1. We pause the installation midway to set up a btrfs partition and btrfs subvolumes on the command line using the tool "partman" before continuing the installation
2. We adjust the fstab and move some folders to the new subvolumes before we reboot into the newly installed system



### Partitioning Scheme

We are going to use the following layout:

```plaintext
Mount Point         | Subvolume         | Description
-------------------------------------------------------------------------
/                   | @                 | The root filesystem incl. /boot
/home               | @home             | User home directories
/root               | @root             | The root user's home directory
/var/log            | @log              | Log files
/.snapshots         | @snapshots        | Snapper's snapshot directory
```





## Kali Linux Btrfs Installation Steps

#### Installation Prerequisites

* A minimum of 20 GB disk space for the Kali Linux install.
* RAM for i386 and amd64 architectures, minimum: 1GB, recommended: 2GB or more.
* CD-DVD Drive / USB boot support

### Preparing for the Installation

1. [Download Kali Linux](/docs/introduction/download-official-kali-linux-images/).
2. Burn the Kali Linux ISO to DVD or [Image Kali Linux Live to USB](/downloading/kali-linux-live-usb-install).
3. Ensure that your computer is set to boot from CD / USB in your BIOS.

### Kali Linux Installation Procedure

1. To start your installation, boot with your chosen installation medium. You should be greeted with the Kali Boot screen. Choose _Graphical Install_.

2. The installation steps are identical to a standard Kali installation except a pause during the step where you choose a domain name as seen below.

    ![btrfs-g-08-di.png](btrfs-g-08-di.png)
3. When prompted, pause the installation and switch to the second VT via "Ctrl + Alt + 2"

    ![btrfs-g-09-cli.png](btrfs-g-09-cli.png)



    Press `enter` to activate that console and run `partman` to partition the hard disk.

    ![btrfs-g-11-cli.png](btrfs-g-11-cli.png)
4. First we create two partitions: swap and root.
   Choose `manual` under "Partitioning method" and press `enter`.

    ![btrfs-g-12-partman.png](btrfs-g-12-partman.png)
5. Choose your hard disk:

    ![btrfs-g-13-partman.png](btrfs-g-13-partman.png)
6. Confirm to create a new partition table

    ![btrfs-g-14-partman.png](btrfs-g-14-partman.png)
7. Next, select the newly defined "free space":

    ![btrfs-g-15-partman.png](btrfs-g-15-partman.png)
8. Select `Create a new partition`:

    ![btrfs-g-16-partman.png](btrfs-g-16-partman.png)
9. Pick the desired size for the swap partition:

    ![btrfs-g-17-partman.png](btrfs-g-17-partman.png)
10. Choose the preferred partition type:

    ![btrfs-g-18-partman.png](btrfs-g-18-partman.png)
11. The location of the swap partition is personal preference, we choose "end" here so it's out of the way

    ![btrfs-g-19-partman.png](btrfs-g-19-partman.png)
12. Choose "Done setting up the partition":

    ![btrfs-g-20-partman.png](btrfs-g-20-partman.png)
13. Next we repeat the procedure to setup the maim btrfs partition:

    ![btrfs-g-21-partman.png](btrfs-g-21-partman.png)
14. Let's create a new partition:

    ![btrfs-g-22-partman.png](btrfs-g-22-partman.png)
15. Use the rest of the available space:



    ![btrfs-g-23-partman.png](btrfs-g-23-partman.png)
16. Choose "Primary" as partition type:

    ![btrfs-g-24-partman.png](btrfs-g-24-partman.png)
17. Configure the following parameters and select `Done setting up the partition`:

     ```markdown
    Use as:          btrfs journaling file system
    Mount point:     /
    Bootable flag:   on
     ```
    ![btrfs-g-25-partman.png](btrfs-g-25-partman.png)
18. Finish the partitioning and confirming to write the partition table to disk:

    ![btrfs-g-26-partman.png](btrfs-g-26-partman.png)


    ![btrfs-g-27-partman.png](btrfs-g-27-partman.png)
19. We return to the command line and can confirm that the new btrfs partition is mounted at /target:

    ![btrfs-g-28-postpartman.png](btrfs-g-28-postpartman.png)
20. Next we create the desired subvolumes:

    ```plaintext
    btrfs subvolume create /target/@
    btrfs subvolume create /target/@home
    btrfs subvolume create /target/@log
    btrfs subvolume create /target/@root
    btrfs subvolume create /target/@snapshots
    ```

    ![btrfs-g-30-postpartman.png](btrfs-g-30-postpartman.png)
21. Lastly we obtain the subvolume id from our new root subvolume "@" via
    ````plaintext
btrfs subvolume list /target
    ````

    here "257" - and we set that as out new default and unmount the partition
    ````plaintext
btrfs subvolume set-default 257 /target
umount /target
    ````
    ![btrfs-g-33-postpartman.png](btrfs-g-33-postpartman.png)
22. Now can switch back to the graphical install via Ctrl + Alt + F5 and continue with the installation:

    ![btrfs-g-08-di.png](btrfs-g-08-di.png)
23. When we get to the partitioning phase, just skip through it and confirm that we are happy to use the existing file system:

    ![btrfs-g-38-di.png](btrfs-g-38-di.png)



    ![btrfs-g-39-di.png](btrfs-g-39-di.png)
24. If you wish you can switch back to VT 2 and confirm that the installer has indeed mounted our "@" subvolume as the temporary root for the installation "/target":

    ![btrfs-g-40-cli.png](btrfs-g-40-cli.png)
25. Returning back to VT 5 we can continue with our installation until we hit the final screen were we pause for one last time:

    ![btrfs-g-45-di.png](btrfs-g-45-di.png)
26. Pressing `Ctrl + Alt + F2` we can return to VT2 and perform our post-installation steps:

    - Create temporary mount points
    - mount the subvolumes
    - move "/home", "/var/log", "/root" to their dedicated subvolumes:
    ```plaintext
    mkdir /target/mnt/root
    mkdir /target/mnt/home
    mkdir /target/mnt/log
    mkdir /target/.snapshots

    mount -t btrfs -o subvol=@root /dev/sda2 /target/mnt/root
    mount -t btrfs -o subvol=@home /dev/sda2 /target/mnt/home
    mount -t btrfs -o subvol=@log /dev/sda2 /target/mnt/log

    mv /target/root/.* /target/mnt/root/
    mv /target/home/* /target/mnt/home/
    mv /target/var/log/* /target/mnt/log/

    nano /target/etc/fstab
    ```

27. After that we can edit fstab to mount each subvolume via `nano /target/etc/fstab`:

    ```plaintext
    UUID=<UUID of btrfs partition> /               btrfs   defaults,subvol=@             0       0
    UUID=<UUID of btrfs partition> /home           btrfs   defaults,subvol=@home         0       0
    UUID=<UUID of btrfs partition> /var/log        btrfs   defaults,subvol=@log          0       0
    UUID=<UUID of btrfs partition> /root           btrfs   defaults,subvol=@root         0       0
    UUID=<UUID of btrfs partition> /.snapshots     btrfs   defaults,subvol=@snapshots    0       0
    ```

    e.g.:

    ![btrfs-g-48-postinst.png](btrfs-g-48-postinst.png)
28. Optionally we can configure "locate" to ignore the .snapshot folder used by snapper (if installed later)
    Add `PRUNENAMES = ".snapshots"` to `/mnt/root/etc/updatedb.conf`

    ![btrfs-g-49-postinst.png](btrfs-g-49-postinst.png)
29. As the last step we have to reset the "default-subvolume" to 5, as that is a requirement for "apt-btrfs-snapshot" to work properly:

    ![btrfs-g-50-postinst.png](btrfs-g-50-postinst.png)
30. Installation is finished now and we can switch back to VT5 (`Ctrl + Alt + F5`) and reboot.

    ![btrfs-g-52-finish.png](btrfs-g-52-finish.png)
31. After the reboot we can log in and install some more tools we need.
    First let's install "btrfs-progs":

     `sudo apt install btrfs-progs`



32. Now we can download and install the "apt-btrfs-snapshot" tool from the Ubuntu repository

    ```bash
    wget https://launchpad.net/ubuntu/+archive/primary/+files/apt-btrfs-snapshot_3.5.2_all.deb
    sudo apt install ./apt-btrfs-snapshot_3.5.2_all.deb
    ```



Congratulations, you have just installed a Kali system with automatic snapshotting functionality! Next, we will cover some basic usage examples.



## Usage

### Create snapshots
Snapshots are automatically created during apt operations. There are no additional steps required, e.g.:

![btrfs-50-example-snapshot](btrfs-50-example-snapshot.png)

### List snapshots
Firstly, a snapshot is also a subvolume, thus all snapshots also show up when listing btrfs subvolumes, e.g. via
`sudo btrfs subvolume list /`

![btrfs-51-example-subvolume-list](btrfs-51-example-subvolume-list.png)

To list only the snapshots, we can use the following command:
`sudo apt-btrfs-snapshot list`

![btrfs-52-example-snapshot-list](btrfs-52-example-snapshot-list.png)

### Delete snapshots

The easiest way to delete a snapshot is by using the following command:
`sudo apt-btrfs-snapshot delete`

![btrfs-52-example-snapshot-delete](btrfs-52-example-snapshot-delete.png)

Voila, it's gone:

![btrfs-53-example-snapshot-list-after-delete](btrfs-53-example-snapshot-list-after-delete.png)

There are more sophisticated ways to delete multiple snapshots, e.g. the following deletes all snapshots older than 2 days:

`sudo apt-btrfs-snapshot delete-older-than 2d`

Refer to the help output for all the different features of "apt-btrfs-snapshot"

### Rollback

To roll back to a previous snapshot we have to remember two things:
- the root "/" of the file system has been installed in a subvolume "/@" and not the root of the btrfs partition "/"
- a snapshot is treated like just another subvolume

thus all we have to do is mount the btrfs partition and replace the current root subvolume "@" with the last snapshot. To be safe we'll backup the curent root ("@") subvolume.
E.g.:

```bash
# mount your root partition (replace "/dev/mmcblk2p2" with yours):
sudo mount /dev/mmcblk2p2 /mnt

# Move the old root away:
sudo mv /mnt/@ /mnt/@_badroot

# Roll back to a previous snapshot:
sudo mv /mnt/@ /mnt/@apt-snapshot-2019-10-13_18:07:40 /mnt/@

sudo reboot -f
```



### Full walkthrough from apt full-upgrade to rollback

#### full-upgrade
After a new installation we don't have any snapshots yet as we can see via:
`sudo apt-btrfs-snapshot list`

![btrfs-70-Rollback-01.png](btrfs-70-Rollback-01.png)

Let's do a full system upgrade:
```bash
apt update
apt full-upgrade
```



![btrfs-72-Rollback-03.png](btrfs-72-Rollback-03.png)



We can observe that a snapshot is being created before any packages are installed:



![btrfs-74-Rollback-05.png](btrfs-74-Rollback-05.png)



Once finished we can confirm that there are no more updates available:



![btrfs-74-Rollback-05b.png](btrfs-74-Rollback-05b.png)



If we list the snapshots again we can see the one that has just been created:



![btrfs-75-Rollback-06.png](btrfs-75-Rollback-06.png)



#### Rollback

Remember that "/" itself is the subvolume "@". To rollback to a snapshot, all we have to do is replace "@" with the snapshot we want.

1. First we have to mount the btrfs partition via:

    `sudo mount /dev/<your btrfs partition> /mnt`

    If we list the content of that partition we can see all the subvolumes, including the snapshots:



    ![btrfs-77-Rollback-08.png](btrfs-77-Rollback-08.png)
2. Before we replace the current root with our snapshot, let's move "@" away just to be safe:

    `sudo mv /mnt/@ /mnt/@_badroot`



    ![btrfs-78-Rollback-09.png](btrfs-78-Rollback-09.png)
3. Now we can pick the snapshot from before the last upgrade and rename it to "@":

    `sudo mv /mnt/@apt-snapshot-2019-10-21_23:50:26 /mnt/@`



    ![btrfs-79-Rollback-10.png](btrfs-79-Rollback-10.png)



    And that's all there is to it, here's the new "@":





    ![btrfs-80-Rollback-11.png](btrfs-80-Rollback-11.png)



    Let's reboot for the rollback to take effect:



    ![btrfs-81-Rollback-12.png](btrfs-81-Rollback-12.png)



#### Confirming that the rollback worked

After the reboot, we can see that the snapshot is gone, because we rolled back to it:



![btrfs-82-Rollback-13.png](btrfs-82-Rollback-13.png)



And if we issue another "apt update", we can see that we are back to where we were before the snapshot:



![btrfs-83-Rollback-14.png](btrfs-83-Rollback-14.png)



Once you confirmed that the system works you can delete the old "root" by mounting the btrfs partition and using the "btrfs subvolume delete" command:



```bash
sudo mount /dev/<your btrfs partition> /mnt
sudo btrfs subvolume delete /mnt/@_badroot
```



![btrfs-84-Rollback-15.png](btrfs-84-Rollback-15.png)




## References:
[Btrfs Wiki](https://btrfs.wiki.kernel.org/index.php/Main_Page),
[Btrfs Debian site](https://wiki.debian.org/Btrfs),
[apt-btrfs-snapshot](https://launchpad.net/ubuntu/+source/apt-btrfs-snapshot),
[Snapper](http://snapper.io/)
