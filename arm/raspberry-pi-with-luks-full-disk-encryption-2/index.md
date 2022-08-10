---
title: Raspberry Pi - Full Disk Encryption
description:
icon:
archived: "false"
draft: false
weight:
author: ["gamb1t", "steev"]
---

## High-level overview

Before we dive into the lower-levels of technical details of what we are going to accomplish, let's take a quick look at our goals that we want to achieve, and break it down:

- [Install Kali Linux](#installing-kali-linux-on-rpi) on a [Raspberry Pi 4](/docs/arm/raspberry-pi-4/) (henceforth called "RPi")
- [Prepare the system](#preparing-the-system) for encrypted boot ready for remote disk unlock
- [Setup SSH keys](#) to allow the remote unlock to occur (using initramfs and Dropbear)
- [Backup](#configuring-remote-ssh-unlock) any existing data
- [Configure](#configuring-for-encryption) the encrypted partitions
- [Restore](#restore-our-data) our data
- **Hack away**!

This might sound like a lot, but it's rather straightforward even if there are a fair few steps. Once completed, we will have a RPi that will:

- Boot
- Get an IP from DHCP
- Wait for us to connect via SSH using keys
- Allow us to provide either the LUKS unlock, or LUKS Nuke passphrases

Then down the road when we are done with whatever it is we are wanting to do, the only thing left is to retrieve it ...at our leisure!


- - -

## Installing Kali Linux on RPi

{{% notice info %}}
If you're following along, be sure to know where you are imaging the file to, and replace `/dev/sdX`. Don't blindly copy/paste!
{{% /notice %}}

We will be creating our drop box machine on an existing Kali installation. It should be very easy to use other Debian-based distributions, and pretty straight forward for other OSes (except Windows users!)

We first will [download](/get-kali/#kali-arm) the [latest stable](/releases/) Kali RPi image. At the time of writing, that's [Kali 2022.2](/blog/kali-linux-2022.2-release/).
We have also chosen the 64-bit image, as we have more than 4GB of RAM, and are not using any [HATs](https://www.raspberrypi.com/news/introducing-raspberry-pi-hats/) (Hardware Attached on Top). The steps for 32-bit would be the same, after adjusting filenames:

```console
$ wget https://kali.download/arm-images/kali-2022.3/kali-linux-2022.3-raspberry-pi-arm64.img.xz
$ xzcat kali-linux-2022.3-raspberry-pi-arm64.img.xz | sudo dd of=/dev/sdX bs=512k status=progress
```

- - -

## Preparing the system

### Preparing the chroot

We next are going to get things ready for a chroot. Let's create where we want to mount the microSD card, then mount it:

```console
$ sudo mkdir -vp /mnt/chroot/
$ sudo mount /dev/sdX2 /mnt/chroot/
$ sudo mount /dev/sdX1 /mnt/chroot/boot/
$ sudo mount -t proc none /mnt/chroot/proc
$ sudo mount -t sysfs none /mnt/chroot/sys
$ sudo mount -o bind /dev /mnt/chroot/dev
$ sudo mount -o bind /dev/pts /mnt/chroot/dev/pts
$ sudo apt install -y qemu-user-static
$ sudo cp /usr/bin/qemu-aarch64-static /mnt/chroot/usr/bin/
```  

The last two commands will come in handy ready for initramfs later.

- - -

### Installing required packages

Now that our system is set up we can use the chroot to set up the RPi image for encryption. Let's first enter the chroot and install some necessary packages:

```console
$ sudo env LANG=C chroot /mnt/chroot/
┌──(root㉿kali)-[/]
└─# apt update

┌──(root㉿kali)-[/]
└─# apt install -y busybox cryptsetup dropbear-initramfs lvm2
```

- - -

We want to ensure we are on the latest kernel before we get started, so lets also make sure we have them installed:

```console
┌──(root㉿kali)-[/]
└─# apt install -y kalipi-kernel kalipi-bootloader kalipi-re4son-firmware
```

- - -

### Boot options

Next we are going to edit `/boot/cmdline.txt` and change the root path. We will want to change the root path to be `/dev/mapper/crypt`, and then we will add in `cryptdevice=PARTUUID=$partuuid:crypt` right after that.  The reason for this is that the kernel needs to know where the root filesystem is, in order to mount it and use it, and since we are encrypting the rootfs later in the post, during boot time it can't see the unencrypted device either, because of the encryption! While we are changing the name here to "crypt", you can call it whatever you want, really. The `/boot/cmdline.txt` file on a RaspberryPi device is used to pass the kernel command line options.
The end result should look like this:

```console
┌──(root㉿kali)-[/]
└─# vim /boot/cmdline.txt

┌──(root㉿kali)-[/]
└─# cat /boot/cmdline.txt
dwc_otg.fiq_fix_enable=2 console=serial0,115200 kgdboc=serial0,115200 console=tty1 root=/dev/mapper/crypt cryptdevice=PARTUUID=ed889dad-02:crypt rootfstype=ext4 fsck.repair=yes rootwait net.ifnames=0
```

- - -

### Partition layout

We now need to update the `/etc/fstab` file, this is a configuration file on the system that contains all available disks, disk partitions, and what options to use when handling them.

Currently it is populated with the UUID of the root filesystem, and we need it to point at the encrypted filesystem that we will be making.  In this example, we've commented out what the previous root device's UUID, and point at `/dev/mapper/crypt` which is what our encrypted filesystem will mount as, once we create it.

```console
┌──(root㉿kali)-[/]
└─# vim /etc/fstab

┌──(root㉿kali)-[/]
└─# cat /etc/fstab
# <file system> <mount point>   <type>  <options>       <dump>  <pass>
proc            /proc           proc    defaults          0       0

/dev/mapper/crypt /               ext4 errors=remount-ro 0       0
#UUID=747bfa7c-edd2-471f-8fff-0ecafc2d3791 /               ext4 errors=remount-ro 0       1
LABEL=BOOT      /boot           vfat    defaults          0       2
```

- - -

### Configure the encrypted partitions

When using encrypted partitions, we need to edit, or create, if it doesn't exist, the `/etc/crypttab` file, which is used by cryptsetup to know what options are needed in order to unlock the encrypted device. 

Because this file doesn't exist, we will create the `/etc/crypttab` file, and fill it with the options we need.

```console
┌──(root㉿kali)-[/]
└─# echo -e 'crypt\tPARTUUID=ed889dad-02\tnone\tluks' > /etc/crypttab
```

- - -

Now we do a little file-system trickery. We create a fake LUKS file-system which will allow cryptsetup to be included in the initramfs because it sees an encrypted partition.  When you format any LUKS partitions, you will be prompted for a password, and while normally you will use a strong password, because we are only using this as a hack to include cryptsetup into our initramfs, the password you create at this prompt will not be needed or used past these steps, so you can set it to something short/quick to type.  This will happen at the `cryptsetup luksFormat` step, and you will be prompted for the password you set during `cryptsetup luksFormat` when you run the `cryptsetup luksOpen` step.

{{% notice info %}}
You will not see any input being typed when entering the password
{{% /notice %}}

```console
┌──(root㉿kali)-[/]
└─# dd if=/dev/zero of=/tmp/fakeroot.img bs=1M count=20

┌──(root㉿kali)-[/]
└─# exit
$ sudo cryptsetup luksFormat /mnt/chroot/tmp/fakeroot.img
$ sudo cryptsetup luksOpen /mnt/chroot/tmp/fakeroot.img crypt
$ sudo mkfs.ext4 /dev/mapper/crypt
```

- - -

### Configuring SSH keys

After that we need to copy over OR generate a new ssh key to be added to Dropbear's `authorized_keys` file.

If we already have an existing key to copy over:

```console
$ sudo cp ~/.ssh/id_rsa.pub /mnt/chroot/
```

Alternatively to generate a new key:

```console
$ ssh-keygen -t rsa -b 4096
[...]
Enter file in which to save the key (/home/kali/.ssh/id_rsa): /home/kali/.ssh/id_rsa_dropbear
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/kali/.ssh/id_rsa_dropbear
Your public key has been saved in /home/kali/.ssh/id_rsa_dropbear.pub
[...]
$ sudo cp ~/.ssh/id_rsa_dropbear.pub /mnt/chroot/
```

{{% notice info %}}
You will not see any input being typed when entering a passphrase
{{% /notice %}}

- - -

### Configuring for encryption

Going back into the chroot, we need to create a few new files.

First is the `zz-cryptsetup` hook which adds the files we need for `cryptsetup` into the `initramfs`. For it to work, it needs to be marked as executable so that `mkinitramfs` will run the hook:

```console
$ sudo env LANG=C chroot /mnt/chroot/
┌──(root㉿kali)-[/]
└─# vim /etc/initramfs-tools/hooks/zz-cryptsetup

┌──(root㉿kali)-[/]
└─# cat /etc/initramfs-tools/hooks/zz-cryptsetup
#!/bin/sh
set -e

PREREQ=""
prereqs()
{
	echo "${PREREQ}"
}

case "${1}" in
	prereqs)
		prereqs
		exit 0
		;;
esac

. /usr/share/initramfs-tools/hook-functions

mkdir -p ${DESTDIR}/cryptroot || true
cat /etc/crypttab >> ${DESTDIR}/cryptroot/crypttab
cat /etc/fstab >> ${DESTDIR}/cryptroot/fstab
cat /etc/crypttab >> ${DESTDIR}/etc/crypttab
cat /etc/fstab >> ${DESTDIR}/etc/fstab
copy_file config /etc/initramfs-tools/unlock.sh /etc/unlock.sh

┌──(root㉿kali)-[/]
└─# chmod +x /etc/initramfs-tools/hooks/zz-cryptsetup
```

_Should you wish to disable it at any point in the future for any reason, simply remove the executable bit._

- - -

We edit the modules file for `initramfs-tools` so that we include the `dm-crypt` module, and cat the file to verify it is correct:

```console
┌──(root㉿kali)-[/]
└─# grep -q dm_crypt /etc/initramfs-tools/modules || echo dm_crypt >> /etc/initramfs-tools/modules

┌──(root㉿kali)-[/]
└─# cat /etc/initramfs-tools/modules
# List of modules that you want to include in your initramfs.
# They will be loaded at boot time in the order below.
#
# Syntax:  module_name [args ...]
#
# You must run update-initramfs(8) to effect this change.
#
# Examples:
#
# raid1
# sd_mod
dm_crypt
```

- - -

### Configuring remote SSH unlock

Create an `unlock.sh` script with the following contents, and then mark it as executable so that the script runs in the `initramfs`:

```console
┌──(root㉿kali)-[/]
└─# vim /etc/initramfs-tools/unlock.sh

┌──(root㉿kali)-[/]
└─# cat /etc/initramfs-tools/unlock.sh
#!/bin/sh

export PATH='/sbin:/bin:/usr/sbin:/usr/bin'

while true; do
	test -e /dev/mapper/crypt && break || cryptsetup luksOpen /dev/disk/by-uuid/$REPLACE_LATER crypt
done

/scripts/local-top/cryptroot
for i in $(ps aux | grep 'cryptroot' | grep -v 'grep' | awk '{print $1}'); do kill -9 $i; done
for i in $(ps aux | grep 'askpass' | grep -v 'grep' | awk '{print $1}'); do kill -9 $i; done
for i in $(ps aux | grep 'ask-for-password' | grep -v 'grep' | awk '{print $1}'); do kill -9 $i; done
for i in $(ps aux | grep '\\-sh' | grep -v 'grep' | awk '{print $1}'); do kill -9 $i; done
exit 0

┌──(root㉿kali)-[/]
└─# chmod +x /etc/initramfs-tools/unlock.sh
```

- - -

Next we must add the following to the beginning of `/etc/dropbear/initramfs/authorized_keys`, which tells it to run this command when we SSH in if the key matches:

```console
┌──(root㉿kali)-[/]
└─# vim /etc/dropbear/initramfs/authorized_keys

┌──(root㉿kali)-[/]
└─# cat /etc/dropbear/initramfs/authorized_keys
command="/etc/unlock.sh; exit"
```

- - -

After doing so, we can append the SSH key that we copied over and then remove it from the card:

```console
┌──(root㉿kali)-[/]
└─# cat id_rsa.pub >> /etc/dropbear/initramfs/authorized_keys && rm -v id_rsa.pub
```

Once you're done, `/etc/dropbear/initramfs/authorized_keys` should look like this:

```console
┌──(root㉿kali)-[/]
└─# cat /etc/dropbear/initramfs/authorized_keys
command="/etc/unlock.sh; exit" ssh-rsa <key> kali@kali
```

Everything in the `authorized_keys` file should be one line, as well as a space between the command's ending `"` and the ssh key (e.g. `[...]exit" ssh-rsa[...]`)

- - -

We now need to edit `/usr/share/initramfs-tools/scripts/init-premount/dropbear` to add a sleep timer, this allows for networking to start _before_ Dropbear does. It is important to note that when there are updates to the `dropbear-initramfs` package, this edit will need to be re-added:

```console
┌──(root㉿kali)-[/]
└─# vim /usr/share/initramfs-tools/scripts/init-premount/dropbear

┌──(root㉿kali)-[/]
└─# cat /usr/share/initramfs-tools/scripts/init-premount/dropbear
[ "$BOOT" != nfs ] || configure_networking
sleep 5
run_dropbear &
echo $! >/run/dropbear.pid
```

- - -

Now we enable cryptsetup:

```console
┌──(root㉿kali)-[/]
└─# echo CRYPTSETUP=y >> /etc/cryptsetup-initramfs/conf-hook

┌──(root㉿kali)-[/]
└─# tail /etc/cryptsetup-initramfs/conf-hook
#
# Whether to include the askpass binary to the initramfs image.  askpass
# is required for interactive passphrase prompts, and ASKPASS=y (the
# default) is implied when the hook detects that same device needs to be
# unlocked interactively (i.e., not via keyfile nor keyscript) at
# initramfs stage.  Setting ASKPASS=n also skips `cryptroot-unlock`
# inclusion as it requires the askpass executable.

#ASKPASS=y
CRYPTSETUP=y
```

- - -

### Kernel

The next step is important for the people who are following along. What to select, depends on the RPi device you are using, will . Below are five kernel names/editions/flavours which you need to select one of for your needs _(please pay attention!)_:

- `Re4son+`     is for 32-bit ARMEL armv6 devices - i.e. RPi1, RPi0, or RPi0w
- `Re4son-v7+`  is for 32-bit ARMHF armv7 devices - i.e. RPi2 v1.2, RPi3 or RPi02w
- `Re4son-v8+`  is for 64-bit ARM64 armv8 devices - i.e. RPi2 v1.2, RPi3 or RPi02w
- `Re4son-v7l+` is for 32-bit ARMHF armv7 devices - i.e. RPi4 or RPi400 devices
- `Re4son-v8l+` is for 64-bit ARM64 armv8 devices - i.e. RPi4 or RPi400 devices

{{% notice info %}}
The `l` in the name stands for lpae - [Large Physical Address Extension](https://wikipedia.org/wiki/ARM_architecture_family#LPAE)
{{% /notice %}}

As a reminder, we are using the RPi4, 64-bit image. So we would need `Re4son-v8l+`. Please make sure you adjust to your device.
So now we know what kernel name to use, we now need to find what kernel version. This will alter from device to device, and it will also change as and when Kali gets updates At the time of writing, it is `5.15.44` for our RPi:

Keep in mind the kernel versions may change, however the name will not:

```console
┌──(root㉿kali)-[/]
└─# ls -l /lib/modules/ | awk -F" " '{print $9}'
5.15.44-Re4son+
5.15.44-Re4son-v7+
5.15.44-Re4son-v7l+
5.15.44-Re4son-v8+
5.15.44-Re4son-v8l+

┌──(root㉿kali)-[/]
└─# echo "initramfs initramfs.gz followkernel" >> /boot/config.txt
```

{{% notice info %}}
Keep in mind the kernel versions (`5.15.44`) may change, however the kernel name (`Re4son-v8l+`) will not.
{{% /notice %}}


- - -

Now we need to create the `initramfs`. This is where the kernel version comes into play:

```console
┌──(root㉿kali)-[/]
└─# mkinitramfs -o /boot/initramfs.gz 5.15.44-Re4son-v8l+
```

- - -

Now we want to ensure that we created the `initramfs` correctly. If there is no result, then something went wrong:

```console
┌──(root㉿kali)-[/]
└─# lsinitramfs /boot/initramfs.gz | grep cryptsetup
usr/lib/aarch64-linux-gnu/libcryptsetup.so.12
usr/lib/aarch64-linux-gnu/libcryptsetup.so.12.7.0
usr/lib/cryptsetup
usr/lib/cryptsetup-nuke-password
usr/lib/cryptsetup-nuke-password/crypt
usr/lib/cryptsetup/askpass
usr/lib/cryptsetup/askpass.cryptsetup
usr/lib/cryptsetup/functions
usr/sbin/cryptsetup

┌──(root㉿kali)-[/]
└─# lsinitramfs /boot/initramfs.gz | grep authorized
root-Q2iWOODUwk/.ssh/authorized_keys

┌──(root㉿kali)-[/]
└─# lsinitramfs /boot/initramfs.gz | grep unlock.sh
etc/unlock.sh
```

- - -

### Disable services

Before we can backup, we have to ensure that `rpi-resizerootfs` is disabled.  This is a service we typically run on all of our ARM devices that resizes the root filesystem partition to increase the size of the partition to the full size of the storage device it is on.  Since we are doing this step manually, we want to disable it, so it doesn't potentially delete our root filesystem and re-make it.

```console
┌──(root㉿kali)-[/]
└─# systemctl disable rpi-resizerootfs
```

## Backup any existing data

Now we can ensure that all the changes are written, then we can encrypt the disk:

```console
┌──(root㉿kali)-[/]
└─# sync

┌──(root㉿kali)-[/]
└─# exit
$ sudo umount /mnt/chroot/{boot,sys,proc,dev/pts,dev}
$ sudo mkdir -vp /mnt/{backup,encrypted}
$ sudo rsync -avh /mnt/chroot/* /mnt/backup/
$ sudo cryptsetup luksClose crypt
$ sudo umount /mnt/chroot
$ echo -e "d\n2\nw" | sudo fdisk /dev/sdX
$ echo -e "n\np\n2\n\n\nw" | sudo fdisk /dev/sdX
```  

## Configure the encrypted partitions

Depending on what device you are using you will have to use one of two commands. If you are using a RPi4 with 4GB or more, use this command:

```console
$ sudo cryptsetup -v -y --cipher aes-cbc-essiv:sha256 --key-size 256 luksFormat /dev/sdX2
```

Otherwise you will want to use the following which uses an older version of LUKS:

```console
$ sudo cryptsetup -v -y --pbkdf pbkdf2 --cipher aes-cbc-essiv:sha256 --key-size 256 luksFormat /dev/sdX2
```  

## Restore our data

Afterwards you can finish restoring data back to the now encrypted partition:

```console
$ sudo cryptsetup -v luksOpen /dev/sdX2 crypt
$ sudo mkfs.ext4 /dev/mapper/crypt
$ sudo mount /dev/mapper/crypt /mnt/encrypted/
$ sudo rsync -avh /mnt/backup/* /mnt/encrypted/
$ sync
```  


- - -

The final steps that we have to make are to fix up the `/etc/fstab` file for the new LUKS UUID, or you can leave it as `/dev/mapper/crypt` and replace the UUID in our unlock script and remake the initramfs file, this step is important as it will not properly boot if not done, because it won't have the information to use the encrypted filesystem! Remember to put the information in from **YOUR** system, as the UUID will be different for every system:

```console
$ sudo mount /dev/sdX1 /mnt/encrypted/boot/
$ sudo mount -t proc none /mnt/encrypted/proc
$ sudo mount -t sysfs none /mnt/encrypted/sys
$ sudo mount -o bind /dev /mnt/encrypted/dev
$ sudo mount -o bind /dev/pts /mnt/encrypted/dev/pts
$ sudo env LANG=C chroot /mnt/encrypted
┌──(root㉿kali)-[/]
└─# blkid /dev/sdX2
/dev/sdX2: UUID="173e2de4-0501-4d8e-9039-a4923bfa5ee7" TYPE="crypto_LUKS" PARTUUID="e1750e08-02"

┌──(root㉿kali)-[/]
└─# cat /etc/fstab
# <file system> <mount point>   <type>  <options>       <dump>  <pass>
proc            /proc           proc    defaults          0       0

UUID=173e2de4-0501-4d8e-9039-a4923bfa5ee7 /               ext4 errors=remount-ro 0       1
LABEL=BOOT      /boot           vfat    defaults          0       2

┌──(root㉿kali)-[/]
└─# vim /etc/initramfs-tools/unlock.sh

┌──(root㉿kali)-[/]
└─# cat /etc/initramfs-tools/unlock.sh
#!/bin/sh

export PATH='/sbin:/bin:/usr/sbin:/usr/bin'

while true; do
	test -e /dev/mapper/crypt && break || cryptsetup luksOpen /dev/disk/by-uuid/173e2de4-0501-4d8e-9039-a4923bfa5ee7 crypt
done

/scripts/local-top/cryptroot
for i in $(ps aux | grep 'cryptroot' | grep -v 'grep' | awk '{print $1}'); do kill -9 $i; done
for i in $(ps aux | grep 'askpass' | grep -v 'grep' | awk '{print $1}'); do kill -9 $i; done
for i in $(ps aux | grep 'ask-for-password' | grep -v 'grep' | awk '{print $1}'); do kill -9 $i; done
for i in $(ps aux | grep '\\-sh' | grep -v 'grep' | awk '{print $1}'); do kill -9 $i; done
exit 0

┌──(root㉿kali)-[/]
└─# vim /etc/crypttab

┌──(root㉿kali)-[/]
└─# cat /etc/crypttab
crypt	PARTUUID=e1750e08-02	none	luks

┌──(root㉿kali)-[/]
└─# mkinitramfs -o /boot/initramfs.gz 5.15.44-Re4son-v8l+
```

{{% notice info %}}
If you get a cryptsetup error here, similar to `cryptsetup: ERROR: Couldn't resolve device PARTUUID=ed889dad-02` that means that you did not edit the `/etc/crypttab` file and put the correct PARTUUID in. The warning about no fsck.luks existing can be ignored, as there is no such thing.
{{% /notice %}}

- - -

Now we can unmount and close up everything:

```console
┌──(root㉿kali)-[/]
└─# exit
$ sudo umount /mnt/encrypted/{boot,sys,proc,dev/pts,dev}
$ sudo umount /mnt/encrypted
$ sudo cryptsetup luksClose crypt
```

- - -

# LUKS NUKE

Should a user also want [LUKS NUKE](/blog/nuke-kali-linux-luks/), all they need to do is run the following command.

```console
kali@kali:~$ dpkg-reconfigure cryptsetup-nuke-password
```

# Automation?

Now how about we get this automated? Thanks to Richard Nelson (unixabg), anyone who wants to get this all set up in much less time than the manual method and much easier, can!

First things first, let's clone [unixabg's cryptmypi](https://github.com/unixabg/cryptmypi) script repository.

```console
kali@kali:~$ git clone https://github.com/unixabg/cryptmypi.git
```

After clone is complete, let's change to the working directory of cryptmypi:

```console
kali@kali:~$ cd cryptmypi/
```

Next let's list available Kali examples to build:

```console
kali@kali:~$ ls -aFl examples/ | grep kali
```

Now we need to edit the cryptmypi.conf on the example you wish to build. These settings will be personal, but let's just give you all an example.

```console
kali@kali:~$ cat kali-encrypted-basic/cryptmypi.conf
###############################################################################
## cryptmypi profile ##########################################################


# EXAMPLE OF A ENCRYPTED KALI CONFIGURATION
#   Will create a encrypted Kali system:
#   - during boot the encryption password will be prompted
#   - with ssh server (available after boot)
#       The id_rsa.pub public key will be added to authorized_keys
#
#   Some optional hooks are defined on stage2:
#   - "optional-sys-rootpassword" that sets root password


# General settings ------------------------------------------------------------
# You need to choose a kernel compatible with your RPi version.
#   Kali RPi images name its kernels:
#   - Re4son+ is for armv6 devices (ie. RPi1, RPi0, and RPi0w)
#   - v7+ and v8+ sufixes are for the 32bit and 64bit armv7 devices (ie. RPi 3)
#   - l+ sufix in the name means they will be ready for the RPi4.
export _KERNEL_VERSION_FILTER="v8+"

# HOSTNAME
#   Each element of the hostname must be from 1 to 63 characters long and
#   the entire hostname, including the dots, can be at most 253
#   characters long.  Valid characters for hostnames are ASCII(7) letters
#   from a to z, the digits from 0 to 9, and the hyphen (-)
export _HOSTNAME="kali-encrypted-basic"

# BLOCK DEVICE
#   The SD card or USD SD card reader block device
#   - USB drives will show up as the normal /dev/sdb, /dev/sdc, etc.
#   - MMC/SDcards may show up the same way if the card reader is USB-connected.
#   - Internal card readers normally show up as /dev/mmcblk0, /dev/mmcblk1, ...
#   You can use the lsblk command to get an easy quick view of all block
#   devices on your system at a given moment.
export _BLKDEV="/dev/sdb"

# LUKS ENCRYPTION -------------------------------------------------------------
## Encryption Cypher
export _LUKSCIPHER="aes-cbc-essiv:sha256"

## Encryption Password
export _LUKSPASSWD="luks_password"

## Encryption Extra
# On rpi0-1-2-3 you may want to reduce the required memory to unlock
#  _LUKSEXTRA="--pbkdf-memory 131072"
export _LUKSEXTRA=""


# LINUX IMAGE FILE ------------------------------------------------------------
export _IMAGEURL=https://images.kali.org/arm-images/kali-linux-2022.3-raspberry-pi-arm64.img.xz
export _IMAGESHA="9ef1a0c011c274a81baaa626206ec985e1caa9494dab2b88ecec0a2473d6cf1f"

# PACKAGE ACTIONS -------------------------------------------------------------
export _PKGSPURGE=""
export _PKGSINSTALL="tree htop"


# MINIMAL SSH CONFIG ----------------------------------------------------------
#   Keyfile to be used to access the system remotelly through ssh.
#   Its public key will be added to the system's root .ssh/autorized_keys
export _SSH_LOCAL_KEYFILE="$_USER_HOME/.ssh/id_rsa"


###############################################################################
## Stage 1 Settings ###########################################################

# Custom Stage1 Profile
#   Check functions/stage1profiles.fns for reference. You may instruct hooks
#   here or you may call one predefined stage1profile functions.
# Optional: if stage1_hooks function is not defined, a prompt will be displayed
stage1_hooks(){
    stage1profile_encryption
}


###############################################################################
## Stage-2 Settings ###########################################################


# Optional stage 2 hooks
#   If declared, this function is called during stage2 build by the
#   stage2-runoptional hook.
#
#   Optional function: can be ommited.
stage2_optional_hooks(){
    myhooks "optional-sys-rootpassword"
}


###############################################################################
##Optional Hook Settings #####################################################


# ROOT PASSWORD CHANGER settings ----------------------------------------------
# Hooks
#   optional-sys-rootpassword
#       Changes the system root password

## The new root password
export _ROOTPASSWD="root_password"
```

After you have made all the chages you desire to the example you have selected to attempt to build, the only thing left to do is initiate the build attempt and follow the instructions.

```console
kali@kali:~$ sudo ./cryptmypi.sh examples/kali-encrypted-basic
```

By the end of it, you should have a fully encrypted filesystem with features enabled of the example you selected. Should you encounter any issues with your automated build, you are encouraged to examine issues at the project's [issues](https://github.com/unixabg/cryptmypi/issues) page. If your believe your issue is new or not listed, please file a new issue.
