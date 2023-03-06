---
title: Standalone Kali Linux 2021.4 Installation on a USB Drive, Fully Encrypted
description:
icon:
weight: 75
author: ["voidyourwarranty",]
---

# Standalone Kali Linux 2021.4 Installation on a USB Drive, Fully Encrypted

These instructions allow you to create a fully encrypted standalone installation of Kali Linux 2021.4 on an external USB drive. By this, I mean an installation that
- is fully encrypted using luks, including encryption of the boot and swap partitions,
- runs on any 64bit Intel/AMD machine that can boot from a USB drive, either using EFI boot or legacy boot,
- does not involve any "live" system, i.e. the system that boots from the USB drive does not need to `chroot` to a RAM disk; the present Kali installation runs on the external drive as it would do on an internal disk; in particular, you can install further packages with `apt-get install <something>`, you can edit configuration files, and all these changes are permanent even if you boot from this USB drive on a different machine the next time; you can also compile a custom kernel and install it on the USB drive.

Use cases are, for example, a tailored bootable Kali 2021.4 installation to be used as a repair, forensic or maintenance tool. If you wish to probe a network from the point of view of a specific machine, you usually need to reconfigure some switches to provide port mirroring and divert all traffic to a segment from where you can probe the it. Now there is an alternative. Simply reboot the relevant machine from your USB Kali installation, connect it to the network, and there you are, right with the usual hardware and the usual MAC address, but this time running Kali.

Also, if your main computer runs Windows or some other flavour of Linux, and you just occasionally require Kali, then you can avoid a dual-boot setup. Whenever you need Kali, simply boot from the USB drive, but otherwise leave your existing installation alone.

Of course, you use the subsequent instructions only if you know exactly what you are doing. You can easily make the harddisks of the machine on which you work, unreadable and thereby lose all your data.

## Outline

Creating such an installation is a bit tricky and involves quite a bit of manual intervention:
- First, we partition our USB drive using an existing Linux installation, and we set up a number of partitions, some of which will be encrypted:
    1. A `/boot` partition with `ext4` file system encrypted with luks, version 1. Fortunately, the boot loader `grub2` can mount luks1 encrypted partitions and boot from them.
    2. A boot sector for `grub2` to reside in.
    3. An EFI partition with `vfat` filesystem.
    4. A `swap` partition, encrypted with luks2.
    5. The root (`/`) partition of our installation with `btrfs` filesystem. Any more fine grained structure is achived by creating subvolumes.
- We also need a USB drive with the Kali "bare metal" installer. This is a Debian type installer. We need to interrupt the expert mode installation and intervene from the command line in order to mount the encrypted partitions of our target USB drive, then install Kali Linux 2021.4 on it, create an initial RAM disk on the `/boot` partition which contains the keys for `grub2` in order to eventually be able to mount the encrypted partitions, but we must not install the `grub2` boot loader at this stage. If we did, this might change the EFI boot information of the machine on which we are performing the installation and produce an unintended dual-boot setup, and so we are left with a complete installation that is merely lacking any boot loader.
- We then need a USB drive with a rescue system. I have not managed to do this with the Kali "bare metal" installer, but rather employ the "Xubuntu 20.04 LTS" live USB medium. With its help, we can install `grub2` in `--removable` mode. This prevents `grub2` from modifying the EFI boot information of the machine on which we are working, but rather affects only our target USB drive.

## Preparation

### Kali Linux 2021.4 Bare Metal Installer

First, we obtain the ISO image of the [Kali Linux 2021.4 Bare Metal Installer](/get-kali/#kali-installer-images). In my case, the ISO image file is called `kali-linux-2021.4-installer-amd64.iso`. We work on an existing Linux machine. In my case, this is an Ubuntu 20.04 LTS.

We insert a blank USB pen drive of at least 4GB and copy the image onto that pen drive as follows. Once we plug in the USB pen drive, we need to find out which block device it corresponds to. Usually this will be `/dev/sdx` where `x`is a one of the letters `a`, `b`, and so on. If our Linux distribution auto-mounts the USB pen drive, `df`, `mount` or `lsblk` will reveal the device file it is associated with. Then make sure the drive is unmounted before you proceed (e.g. `sudo umount /dev/sdxn` where `n` stands for the numbers of all partitions of the drive that have been auto-mounted).
If the drive is not auto-mounted, `ls /dev` before and after plugging it in should reveal the device file name.

On my Xubuntu 20.04 LTS machine, the first USB pen drive is associated with `/dev/sda`, and so in order to copy the ISO image to the pen drive,

```console
$ sudo dd if=kali-linux-2021.4-installer-amd64.iso of=/dev/sda status=progress
```

(Note that on Ubuntu like systems, rather than working under the `root` user, you can issue commands with root privileges by calling them via `sudo`)

### Xubuntu 20.04 LTS Installer

We also need the ISO image of the [Xubuntu 20.04 LTS Live Installer](https://xubuntu.org/download) on a second USB pen drive. In my case, the ISO image file is called `xubuntu-20.04-desktop-amd64.iso`. In the same fashion, this goes to a second USB pen drive (at least 4GB in size). Unplug the first USB pen drive and plug in the second one:

```console
$ sudo dd if=xubuntu-20.04-desktop-amd64.iso of=/dev/sda status=progress
```

### Partitioning the Target USB Drive

Finally, we insert the USB drive on which we wish to perform the Kali Linux 2021.4 installation. I use a 128GB NVMe M.2 SSD in an USB 3.1 gen 2 enclosure for this purpose. Disconnect the second pen drive and connect the target USB drive. Similarly to the above, find out which device `/dev/sdx` the target USB drive corresponds to and make sure that none of its partitions are mounted. In my case, the target USB drive is again associated with `/dev/sda`.

We then use `sudo gdisk /dev/sda` in order to create the following partitions:

```plaintext
/dev/sda1,   4.0 GiB, type 8301, Linux reserved, will become /boot
/dev/sda2,   2.0 MiB, type ef02, BIOS boot, will contain grub2
/dev/sda3, 128.0 MiB, type ef00, EFI system partition
/dev/sda4,   8.0 GiB, type 8200, Linux swap
/dev/sda5,  <something big>, type 8300, Linux filesystem, will be the main partition
```

Here, I have allocated 8GB for swap which is sufficient for my purposes. Note that you will not be able to hibernate (suspend to disk) if this partition is too small. If you wish to connect your Kali Linux USB drive to different machines, you should probably forget about hibernation because your system would most likely not properly wake up on hardware different from where you put it to sleep.

The main partition can be as big as you like. If you require additional partitions, you can treat them all precisely as we treat `/dev/sda5` in the present notes.

Rather than using `gdisk`, we can alternatively repartition the target USB drive from the command line:

```console
$ sgdisk --zap-all /dev/sda
$ sgdisk --new=1:0:+4096M /dev/sda
$ sgdisk --new=2:0:+2M /dev/sda
$ sgdisk --new=3:0:+128M /dev/sda
$ sgdisk --new=4:0:+8192M /dev/sda
$ sgdisk --new=5:0:0 /dev/sda
$ sgdisk --typecode=1:8301 --typecode=2:ef02 --typecode=3:ef00 --typecode=4:8200 --typecode=5:8300 /dev/sda
$ sgdisk --change-name=1:/boot --change-name=2:GRUB --change-name=3:EFI-SP --change-name=4:swap --change-name=5:rootfs /dev/sda
$ sgdisk --hybrid 1:2:3 /dev/sda
$ sgdisk --print /dev/sda
```
(Note that the size hint `:0` for partition 5 indicates that we request all the remaining free space)

Next, we set up luks encryption for the partitions 1,4 and 5. Note that the boot loader `grub2` can mount only luks, version 1:

```console
$ sudo cryptsetup luksFormat --type=luks1 /dev/sda1
$ sudo cryptsetup luksFormat /dev/sda4
$ sudo cryptsetup luksFormat /dev/sda5
```

Since the keys to unlock partitions 4 and 5 will be placed in the initial RAM disk on the `/boot` partition, we can set the same passphrase for all three partitions. Using different phrases would not increase security. We now unlock the three partitions,

```console
$ sudo cryptsetup open /dev/sda1 LUKS_BOOT
$ sudo cryptsetup open /dev/sda4 LUKS_SWAP
$ sudo cryptsetup open /dev/sda5 LUKS_ROOT
```

The command `ls /dev/mapper` then shows the three devices associated with the unlocked partitions. We can now format the partitions 1,3,4 and 5, aka create filesystems on them:

```console
$ sudo mkfs.ext4 -L boot /dev/mapper/LUKS_BOOT
$ sudo mkfs.vfat -F 16 -n EFI-SP /dev/sda3
$ sudo mkswap -L swap /dev/mapper/LUKS_SWAP
$ sudo mkfs.btrfs -L root /dev/mapper/LUKS_ROOT
```

Finally, we remove the mapped devices (and our machine forgets how to decrypt the partitions):

```console
$ sudo cryptsetup close LUKS_BOOT
$ sudo cryptsetup close LUKS_SWAP
$ sudo cryptsetup close LUKS_ROOT
```
## Installation of Kali Linux 2021.4

### Setting Up the Installer

Now setup the BIOS of your computer to boot from a USB drive. If we boot in EFI mode, we will eventually obtain a USB drive that boots only in EFI mode. If we boot in legacy BIOS mode, we will eventually produce a USB drive that boots both in legacy and in EFI mode.

Plug in the USB pen drive with the Kali 2021.4 installer and boot the machine. Note that we will require network access during installation.

Chose *Advanced Options* and *Graphical Expert Install*. Execute the following items of the installation menu (but none other than these):
- *Choose language*
- *Configure the keyboard*
- *Detect and mount installation medium*
- *Load debconf preconfiguration file*
- *Load installer components from installation medium*, select `crypto-dm-modules`, `fdisk-udeb`, `mbr-udeb`, `parted-udeb`, `rescue-mode`.
- *Detect network hardware*
- *Configure the network*, make sure that you have network access at this stage.
- *Set up users and passwords*, select *shadow passwords* and setup the main user that will be able to get root privileges via `sudo`. 
- *Configure the clock*, note that when you choose *ntp* (the network time protocol), your machine will start polling once connected to a network which is perhaps undesirable for your installation

Do not execute *Detect disks*. Rather press [Ctrl]+[Alt]+[F3] and then [Enter] in order to open a text console with root privileges. Now Connect the target USB drive on which we will install Kali Linux 2021.4. Use `ls /dev` before and after connecting in order to discover which device files it is associated with. In my case, the USB pen drive from which I have booted, is `/dev/sda`, and the target USB drive becomes `/dev/sdb`.

We unlock the three encrypted partitions:

```console
$ cryptsetup open /dev/sdb1 LUKS_BOOT
$ cryptsetup open /dev/sdb4 LUKS_SWAP
$ cryptsetup open /dev/sdb5 LUKS_ROOT
```
### Partitioning

Now press [Ctrl]+[Alt]+[F5] and continue with the installation in graphical mode as follows.

- *Detect disks*
- *Partition disks*, manual.

Place the cursor on the line `4.3 GB ...` below `LUKS_BOOT`, press [Enter], use as `Ext4`, format, mount at `/boot`, options `noatime`, and select *Done setting up the partition*.
Place the cursor on the line below `LUKS_ROOT`, press [Enter], use as `btrfs`, format, mount at `/`, options `noatime`, and select *Done setting up the partition*.
The partition `LUKS_SWAP` has already been recognized and marked as `swap`.

Select on *Finish partitioning and write changes to disk*.

Then we press [Ctrl]+[Alt]+[F3] in order to get to the root console again.

Calling `df` shows that the two non-swap encrypted partitions have been mounted at `/target` and `/target/boot`, respectively. If the machine was booted in EFI mode, there is also `/target/boot/efi`, but the wrong one, pointing to the EFI partition of the first internal harddisk. It rather ought to be the EFI partition on the target USB drive. See below for more details.

### Btrfs Adjustments

The debian installer has created the subvolume `@rootfs` on the `btrfs` partition. We mount the top level subvolume,
create further subvolumes, `@` for the default subvolume, `@home`, `@root`, `@snapshots` and `@var`, and we set `@` to be the default:

```console
$ mkdir -p /mnt/point/
$ mount -o subvol=/ /dev/mapper/LUKS_ROOT /mnt/point
$ cd /mnt/point/
$ btrfs subvolume create @
$ btrfs subvolume create @home
$ btrfs subvolume create @root
$ btrfs subvolume create @snapshots
$ btrfs subvolume list .
$ btrfs subvolume set-default 257 . # where 257 is the subvolume ID that was displayed for @
$ cd /
$ umount /mnt/point
$ umount /target/boot/efi # only required when booted in EFI mode
$ umount /target/boot
$ umount /target
$ mount -o subvol=@ /dev/mapper/LUKS_ROOT /target
$ mkdir -p /target/boot
$ mkdir -p /target/etc
$ mkdir -p /target/media
$ mkdir -p /target/snapshots
$ mount /dev/mapper/LUKS_BOOT /target/boot
$ mount -o subvol=@rootfs /dev/mapper/LUKS_ROOT /mnt/point
$ cp /mnt/point/etc/fstab /target/etc/fstab
```

Here, the `btrfs subvolume list` shows the subvolume IDs on the current volume, and we use the number assigned to `@` in the subsequent `set-default` command.

We have created the subvolume `@` as the one to be mounted on `/` by default, follwing `snapper` naming conventions. It is a matter of taste how many further subvolumes to create. We go for `@home`, `@root` and `@snapshots` which we will later mount at `/home`, `/root` and `/snapshots`. Note that although some like `/var` on a seperate subvolume, this will not work below.

### The File System Table

The `umount /target/boot/efi` is required only when the installation medium was booted in EFI mode. The key point here is that even though we are installing to a USB drive, the EFI partition mounted so far is the EFI partition of the internal harddisk of the computer. This wrong mount would cause serious trouble later when we install a boot loader.

Then we call `blkid -s PARTUUID -o value /dev/sdb3` in order to find out the UUID of the EFI partition on the target USB drive and write it down on paper. We call `vim /target/etc/fstab` in order to adapt the file system table as follows.

Firstly, in the line for `/`, we change `subvol=@rootfs` to `subvol=@`. We also add a line

```plaintext
PARTUUID=<whatever> /boot/efi vfat umask=0077 0 1
```

with the UUID we just wrote down. This allows the installation to locate and mount the correct EFI partition irrespectively of any device number. Then we add lines in order to mount the `btrfs` subvolumes we just created. I have also added mount options that are useful for working with SSDs. In my case, the other lines thus read as follows:

```plaintext
/dev/mapper/LUKS_ROOT /               btrfs   defaults,noatime,ssd,compress=lzo,subvol=@              0 0
/dev/mapper/LUKS_BOOT /boot           ext4    defaults,noatime                                                    0 1
/dev/mapper/LUKS_ROOT /home           btrfs   defaults,noatime,ssd,compress=lzo,subvol=@home      0 2
/dev/mapper/LUKS_ROOT /root           btrfs   defaults,noatime,ssd,compress=lzo,subvol=@root      0 3
/dev/mapper/LUKS_ROOT /snapshots      btrfs   defaults,noatime,ssd,compress=lzo,subvol=@snapshots 0 4
/dev/mapper/LUKS_SWAP none            swap    sw                                                                          0 0
```

(in which only one line says `LUKS_BOOT` instead of `LUKS_ROOT`). In the editor `nano`, we press [Ctrl]+[o] in order to save the file, confirm with [Enter] and then [Ctrl]+[x] to quit the editor. Now we remove the top level subvolume and mount the correct EFI partition in the appropriate place:

```console
$ umount /mnt/point
$ mkdir -p /target/boot/efi
$ mount /dev/sdb3 /target/boot/efi
$ mount -o subvol=@home /dev/mapper/LUKS_ROOT /target/home
$ mount -o subvol=@root /dev/mapper/LUKS_ROOT /target/root
```

If you have created further btrfs subvolumes that might be affected by the installation, mount them here as well.

### Actual Installation

Again, [Ctrl]+[Alt]+[F5] gets us to the graphical screen, and we continue the installation as follows.

- *Install the base system*, select the kernel *linux-image-5.14.0-kali4-amd64*, *generic initrd*.
- *Configure the package manager*.
- *Select and install software*, we keep the defaults.

But then, do *not* install a boot loader, but rather

- *Continue without boot loader*

Some people who tried to install Linux on a portable drive report that the installation nevertheless modified the boot sectors of the internal harddisk of the machine they were working on. I suspect by mounting the correct partition at `/target/boot/efi` above, we would have avoided this. Nevertheless we cannot use the installation of `grub2` that the installer would perform because it would lack the `--removable` flag. We therefore postpone the installation of the boot loader to a later stage.

### Setting Up the Initial RAM Disk

Then back to the root console with [Ctrl]+[Alt]+[F3] in order to make sure that `grub2`, when it is evetually installed, will be able to decrypt the boot partition.

We change root to the newly installed system, mount all partitions and subvolumes,

```console
$ for n in dev proc sys run etc/resolv.conf; do mount --bind /$n /target/$n; done
$ chroot /target
$ mount -a
```

If the `mount -a` reports errors, this might be due to typos in the file system table we modified above.

The `/etc/resolv.conf` is there in order not to lose the DNS information that the installer obtained when we set up the network. On the target system, we install all tools in order to install and update `grub2` as well as to add decryption tools to the initial RAM disk:

```console
$ apt-get install grub-common grub-efi-amd64 os-prober
$ apt-get install cryptsetup-initramfs
```

In the following, we create a random luks key file which is able to unlock the three encrypted partitions and set up a few configuration files before the initial RAM disk is composed:

```console
$ echo "KEYFILE_PATTERN=/etc/luks/*.keyfile" >>/etc/cryptsetup-initramfs/conf-hook
$ echo "UMASK=0077" >>/etc/initramfs-tools/initramfs.conf
$ mkdir -p /etc/luks
$ dd if=/dev/urandom of=/etc/luks/boot_os.keyfile bs=4096 count=1
$ chmod u=rx,go-rwx /etc/luks
$ chmod u=r,go-rwx /etc/luks/boot_os.keyfile
$ cryptsetup luksAddKey /dev/sdb1 /etc/luks/boot_os.keyfile
$ cryptsetup luksAddKey /dev/sdb4 /etc/luks/boot_os.keyfile
$ cryptsetup luksAddKey /dev/sdb5 /etc/luks/boot_os.keyfile
$ echo "LUKS_BOOT UUID=$(blkid -s UUID -o value /dev/sdb1) /etc/luks/boot_os.keyfile luks,discard" >>/etc/crypttab
$ echo "LUKS_SWAP UUID=$(blkid -s UUID -o value /dev/sdb4) /etc/luks/boot_os.keyfile luks,discard" >>/etc/crypttab
$ echo "LUKS_ROOT UUID=$(blkid -s UUID -o value /dev/sdb5) /etc/luks/boot_os.keyfile luks,discard" >>/etc/crypttab
$ /usr/sbin/update-initramfs -u -k all
```

During the boot process, `grub2` will detect that there is an encrypted `/boot` partition. It will ask for a passphrase (the one that we associated with `/boot` right in the beginning). The `/boot` partition then contains a kernel with initial RAM disk that contains the key file we just created and which can be used to unlock all our encrypted partitions.

Cryptographically, the weakest link is the passphrase protection of the `/boot` partition. Should that be broken, the other partitions are compromised, too. And note that this is merely version luks1.

### Finishing the Installation

Finally, we press [Ctrl]+[Alt]+[F5] go back to the graphical installer and

- *Finish the installation* with system clock set to UTC.

Eventually, we click *Continue* to boot the system, but we rather remove the USB pen drive and the USB installation target and turn off the computer.

## Creation of a Boot Loader

Note that in order to produce an EFI bootable USB drive, when we next boot the computer, this has to be in UEFI mode. If required, go to the BIOS/UEFI Setup and switch it on.

Now we connect the USB pen drive that contains the Xubuntu 20.04.3 LTS live installation medium and boot from it, select *Try
Xubuntu wihout installing* in order to get to the live system. Then we connect the USB drive that forms our installation target.

Make sure that the Xubuntu live system has network access (this is done by the `NetworkManager` in the graphical interface). Open a terminal and call `ls /sys/firmware/efi/efivars` in order to confirm that we are indeed in EFI mode. If this directory were missing, we would be in legacy BIOS boot mode. 

Again use `ls /dev` and `df` in order to find out to which device `/dev/sdx` our installation target is associated. We know that the Xubuntu live image is mounted at `/media/cdrom` (so this is *not* it) and that our target USB drive contains the 5 partitions that we created. In the following, I assume that again `/dev/sdb` corresponds to the installation target.

Note that the Xubuntu live system will try to mount the three luks partitions. Click `Cancel` three times in order not to do so.

We unlock the encrypted partitions and mount the required ones by hand,

```console
$ sudo -i
$ cryptsetup open /dev/sdb1 LUKS_BOOT
$ cryptsetup open /dev/sdb5 LUKS_ROOT
$ cd /
$ mkdir -p /target
$ mount /dev/mapper/LUKS_ROOT /target
$ mount /dev/mapper/LUKS_BOOT /target/boot
$ mount /dev/sdb3 /target/boot/efi
```

Then we change root to our new unfinished installation and mount everything:

```console
$ for n in dev dev/pts proc sys sys/firmware/efi/efivars run etc/resolv.conf; do mount --bind /$n /target/$n; done
$ chroot /target
$ mount -a
```

Now we merely need to `vim /etc/default/grub` and add a line

```plaintext
GRUB_ENABLE_CRYPTODISK=y
```

Save the file by [Ctrl]+[o] and [Enter] and quit the editor by [Ctrl]+[x]. Then we install `grub2` in removable mode by,

```console
$ grub-install --removable /dev/sdb
$ update-grub
```
Presumably there is an error that there is no grub drive for the partition that carries the Xubuntu live system. We can safely ignore that. We press [Ctrl]+[d] in order to leave the `chroot` session and then use the graphical interface of Xubuntu in order to shut down the computer. The installation on our USB drive is complete.

## Usage

### How to Boot the New Kali Linux 2021.4 USB Drive

When we boot from our USB drive either in legacy or in EFI mode, `grub2` asks for the passphrase (the one of the `/boot`) partition and then boots right into our new Kali Linux 2021.4.

On some older UEFI machines, however, we are thrown into a `grub2` shell. Nevertheless, we can simply type `exit` [Enter] and get to the prompt for the passphrase.

### Kali and Networking

Kali Linux is very conservative about network connections, and so your new installation probably needs some further setup. If you intend to boot the USB drive on different machines, you will probably set up networking on a case by case basis.

If you wish to place WLAN under the control of the `NetworkManager` in the graphical interface, you need to `sudo vim /etc/NetworkManager/NetworkManager.conf` and under `[ifupdown]` set `managed=true`. I think `sudo systemctl restart NetworkManager` ought to suffice, but somehow I recall that it worked only after rebooting.

### Hardware Limitations

I am not an expert on the Debian style installer that was used, but it seems that although it installs a full kernel with all modules, it copies only selected firmware drivers to the target installation. Apparently, the installer probes the hardware on which it is running and then installs the firmware appropriate for that very machine.

I performed a test installation on a Chinese made no-name Intel Core i5-10210U based laptop with integrated Intel graphics and sound, and I do have working sound on that machine. But when I boot the same installation on a Lenovo T14 AMD Gen 1 (AMD Ryzen 5 Pro 4650U with integrated Radeon graphics and Realtek ALC257 audio), then sound does not work. So you have to expect that some hardware specific manual configuration will be necessary. Fortunately, in my case, both ethernet and WLAN work out of the box on any hardware that I have tried so far.

Finally, although the installation allows to to /suspend to disk/ (/hibernate/), you cannot expect it to function when you put the system to sleep (/suspend/) on one hardware and then try to wake it up (/resume/) on a different machine.

### Compilation of a Custom Kernel

Compilation of custom kernel works as described in [these Kali instructions](/docs/development/recompiling-the-kali-linux-kernel/) with only minor adjustments.

Starting with the installation performed so far, we need the following packages:

```console
$ sudo apt-get install build-essential libncurses5-dev fakeroot xz-utils libelf-dev libssl-dev dwarves
```

We install the current kernel sources and unpack them inside `~/src` as follows,

```console
$ sudo apt-get install linux-source-5.15
$ mkdir -p ~/src/
$ cd ~/src/
$ tar -xzf /usr/src/linux-source-5.15.tar.xz
```
If you have a custom kernel configuration file, copy it to `~/src/linux-source-5.15/.config`. In order to use the configuration of the running kernel as a starting point, we obtain the configuration from the `/boot` partition,

```console
$ cp /boot/config-5.14.0-kali4-amd64 ~/src/linux-source-5.15/.config
```

The kernel is then configured as usual with 

```console
$ cd ~/src/linux-source-5.15/
$ make menuconfig
```

and compiled as follows. Note that we build the Debian style kernel packages here which automatically takes care of all patches, etc:

```console
$ make clean
$ make deb-pkg LOCALVERSION=-custom KDEB_PKGVERSION=$(make kernelversion)-1
$ ls ../*.deb
```

It suffices to install the newly created Debian kernel package. It includes an initial RAM disk which is able to decrypt the luks encrypted partitions and places everything on the `/boot` partition for `grub2` to find. The precise version of the kernel depends on the update status of your Kali 2021.4 at the time when you last upgraded and unpacked the sources. The above `ls ../*.deb` command shows the full names of the produced Debian packages:

```console
$ sudo dpkg -i ~/src/linux-image-5.15.5-custom_5.15.5-1_amd64.deb
```

It is not necessary to re-install `grub2` or to make any other manual adjustments. The encrypted boot procesudure still works.

### Remarks on Cryptography

- To anyone who is able to examine your USB drive, it will be obvious that there is a `grub2` boot partition, an EFI partition, as well as three luks encrypted partitions. There is no *plausible deniability*. This is because luks partitions have a standardized header.
- Note that the full AES keys for the encryption are also stored in this header, merely protected by a passphrase. So it is not even required to attack the AES key itself, but rather the data present in the luks header allow an attack on the passphrase. An attacker might transfer the luks headers to a data centre of his choice and run a massively parallel dictionary attack. This is no surprise because luks encryption uses only *one factor*, knowing the passphrase. If you wanted to improve on this, you might decide to store the AES keys on yet another medium in order to obtain a *second factor*, possession of the passphase-encrypted AES key. But this would no longer be bootable by `grub2`.
- Also note that it is the passphrase of the `/boot` partition that it sufficies to attack, and that is only luks1.
- Encryption of the `/boot` partition substantially reduces the attack surface to an *evil maid attack*. Without encryption of `/boot`, an attacker who gets hold of your USB drive, can tamper with the kernel image and with the initial RAM disk stored on that `/boot` partition. This is no longer possible.
- It might still be possible to replace the `grub2` boot loader by an alternative program that first asks for your passphrase, then has enough rights to access the network (on whatever machine you might be trying to boot from your USB drive) in order to exfiltrate your passphrase to the attacker, and which eventually uses your passphrase to boot your very Kali installation. You would not notice that this has happened.
- There is still another potential software based *evil maid attack* left. The maid who finds your USB drive in your hotel room might first extract a bitwise copy of its content. She might then try to boot from it and notice the exact form of the prompt for the passphrase. She might then overwrite your USB drive with another boot loader that prompts for your passphrase in exactly the same fashion and immediately afterwards does what *she* wants. So if you plug in your USB drive the next time and boot from it, you will give away your passphrase to her boot loader. If she can then somehow exfiltrate your passphrase (by WLAN?), she not only has a bitwise copy of your drive, but also your passphrase. But you will immediately notice that this has happened as she is unlikely to be able to mimick your exact Kali installation.
- You still need to trust the hardware on which you are running your USB drive.

## Acknowledgements

The present instructions would not have been possible without

- [Ubuntu Full Disk Encryption Howto 2019](https://help.ubuntu.com/community/Full_Disk_Encryption_Howto_2019)
