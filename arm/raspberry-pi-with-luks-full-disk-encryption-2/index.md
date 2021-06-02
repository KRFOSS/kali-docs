---
title: Raspberry Pi - Full Disk Encryption
description:
icon:
type: archived
draft: true
weight:
author: ["gamb1t",]
---

{{% notice info %}}
The following documentation is not yet working. Follow along with the status at the following link: https://gitlab.com/kalilinux/documentation/kali-docs/issues/49
{{% /notice %}}

Last year we made a blog post, [Secure Kali Pi 2018](/blog/secure-kali-pi-2018/), covering how to encrypt a Kali RPi install. Since then there have been a few developments. One important note is that unixabg has created a script to automate the process. We will touch more on that after going through the manual method, however we recommend reading what is being done still.

As a review, what we are trying to accomplish is to create a stanalone "leave behind" device that, when discovered, does not make it easy to figure out what you were doing. So we use the LUKS full disk encryption along with the LUKS Nuke capability to put this together. If you have a Raspberry Pi 3 Model B+, or really any other model or similar device, feel free to use the instructions below to set up your own secure system. This updated process is based on our previous documentation, and updated with some community suggestions.

# Overview of the process

Before we dive into the tech of what we are going to try to accomplish, let's take a quick look at our goals on setting up our Raspberry Pi 3 Model B+ (henceforth called "RPi"):

- Create a normal Kali Linux RPi installation
- Prepare the system for encrypted boot with remote disk unlock
- Create an initramfs configured with Dropbear and SSH keys to allow the unlock to occur
- Backup existing data
- Configure the encrypted partitions
- Restore our data
- Hack away!

This might seem like a lot, but its really pretty straightforward. Once completed, we will be left with a RPi that will boot, get an IP from DHCP, and  allow us to connect through Dropbear via SSH to provide the LUKS key. This permits us to run the RPi headless, while still keeping our data secure. Then down the road when we are done with it, we can retrieve it.

# Preparing the system

We first will download and image the latest Kali RPi3 image. If you're following along, be sure to know where you are imaging the file to.

```console
kali@kali:~$ wget https://images.kali.org/arm-images/kali-linux-2021.2-rpi3-nexmon.img.xz
kali@kali:~$
kali@kali:~$ xzcat kali-linux-2021.2-rpi3-nexmon.img.xz | sudo dd of=/dev/sdb bs=4M status=progress
```

Next we are going to get things ready for chroot. Let's create where we want to mount the SD card then mount it.

```console
kali@kali:~$ mkdir -p /mnt/chroot/boot
kali@kali:~$ mount /dev/sdb2 /mnt/chroot/
kali@kali:~$ mount /dev/sdb1 /mnt/chroot/boot/
kali@kali:~$ mount -t proc none /mnt/chroot/proc
kali@kali:~$ mount -t sysfs none /mnt/chroot/sys
kali@kali:~$ mount -o bind /dev /mnt/chroot/dev
kali@kali:~$ mount -o bind /dev/pts /mnt/chroot/dev/pts
kali@kali:~$ sudo apt install -y qemu-user-static
kali@kali:~$ cp /usr/bin/qemu-arm-static /mnt/chroot/usr/bin/
```

# Doing the Magic Fu

Now that our system is set up we can use chroot to set up the RPi image for encryption. Let's first chroot in and install some necessary packages.

```console
kali@kali:~$ LANG=C chroot /mnt/chroot/
kali@kali:~$ sudo apt update
kali@kali:~$ sudo apt install -y cryptsetup lvm2 busybox dropbear
```

We will now be listing out five kernel versions and depending on the RPi being used you will need to choose certain versions. The first version, Re4son+, is for armv6 devices IE. RPi1, RPi0, or RPi0w. The next two, Re4son-v7+ and Re4son-v8+, are the 32-bit and 64-bit versions for armv7 devices, respectfully. The final two will be the ones merged into the armv7 32bit and 64bit versions, and the `l` in the name means they will be for the RPi4. Keep in mind the kernel versions may change, however the name will not.

```console
kali@kali:~$ ls -l /lib/modules/ | awk -F" " '{print $9}'
4.19.81-Re4son+
4.19.81-Re4son-v7+
4.19.81-Re4son-v7l+
4.19.81-Re4son-v8+
4.19.81-Re4son-v8l+
kali@kali:~$ echo initramfs initramfs.gz followkernel >> /boot/config.txt
```

Next we are going to edit `/boot/cmdline.txt` and change the root path. We will want to change the root path to be `/dev/mapper/crypt`, and then we will add in `cryptdevice=/dev/mmcblk0p2:crypt` right after that. The end result should look like this:

```console
kali@kali:~$ cat /boot/cmdline.txt
dwc_otg.fiq_fix_enable=2 console=ttyAMA0,115200 kgdboc=ttyAMA0,115200 console=tty1 root=/dev/mapper/crypt cryptdevice=/dev/mmcblk0p2:crypt rootfstype=ext4 rootwait rootflags=noload net.ifnames=0
```

Now we update fstab to have the correct root filesystem path.

```console
kali@kali:~$ cat /etc/fstab
# <file system> <mount point>   <type>  <options>       <dump>  <pass>
proc            /proc           proc    defaults          0       0
/dev/mmcblk0p1  /boot           vfat    defaults          0       2
/dev/mapper/crypt /             ext4    defaults,noatime  0       1
#/dev/mmcblk0p2  /               ext4    defaults,noatime  0       1
```

Next we will create the crypttab file.

```console
kali@kali:~$ echo -e 'crypt\t/dev/mmcblk0p2\tnone\tluks' > /etc/crypttab
```

Now we do a little filesystem trickery. We create a fake LUKS filesystem which forces cryptsetup to be included.

```console
kali@kali:~$ dd if=/dev/zero of=/tmp/fakeroot.img bs=4M count=20
kali@kali:~$ exit
kali@kali:~$ cryptsetup luksFormat /mnt/chroot/tmp/fakeroot.img
kali@kali:~$ cryptsetup luksOpen /mnt/chroot/tmp/fakeroot.img crypt
kali@kali:~$ mkfs.ext4 /mnt/chroot/dev/mapper/crypt
```

After that we need to copy over, or generate, an ssh key to be added to dropbear's authorized_keys file.

```console
kali@kali:~$ cp id_rsa.pub /mnt/chroot/
kali@kali:~$ LANG=C chroot /mnt/chroot/
```

Next we must add the following to /etc/dropbear-initramfs/authorized_keys:

```console
kali@kali:~$ vim /etc/dropbear-initramfs/authorized_keys
kali@kali:~$ cat /etc/dropbear-initramfs/authorized_keys
command="export PATH='/sbin:/bin/:/usr/sbin:/usr/bin'; /scripts/local-top/cryptroot && kill -9 `ps | grep -m 1 'cryptroot' | cut -d ' ' -f 3` && exit"
```

After doing so, we can append the ssh key that we copied over and then remove it from the card.

```console
kali@kali:~$ cat id_rsa.pub >> /etc/dropbear-initramfs/authorized_keys
kali@kali:~$ rm id_rsa.pub
```

Once you're done, `/etc/dropbear-initramfs/authorized_keys` should look like this (Note: you may need to delete a return after the command so the ssh key follows directly after):

```console
kali@kali:~$ cat /etc/dropbear-initramfs/authorized_keys
command="export PATH='/sbin:/bin/:/usr/sbin:/usr/bin'; /scripts/local-top/cryptroot && kill -9 `ps | grep -m 1 'cryptroot' | cut -d ' ' -f 3` && exit" ssh-rsa key user@sys
```

We now need to edit `/usr/share/initramfs-tools/scripts/init-premount/dropbear` to add a sleep timer, this allows for networking to start *before* dropbear does.

```plaintext
[ "$BOOT" != nfs ] || configure_networking
sleep 5
run_dropbear &
echo $! >/run/dropbear.pid
```

Let's now enable cryptsetup.

```console
kali@kali:~$ echo CRYPTSETUP=y > /etc/cryptsetup-initramfs/conf-hook
kali@kali:~$
kali@kali:~$ cat /etc/cryptsetup-initramfs/conf-hook
CRYPTSETUP=y
```

Now we need to create the initramfs. This is where the kernel versions from before come into play.

```console
kali@kali:~$ mkinitramfs -o /boot/initramfs.gz 4.19.93-Re4son-v7+
```

Now we want to ensure that we created the initramfs corectly. If there is no result, then something went wrong.

```console
kali@kali:~$ lsinitramfs /boot/initramfs.gz | grep cryptsetup
kali@kali:~$ lsinitramfs /boot/initramfs.gz | grep authorized
```

Before we can backup, we have to ensure that rpiwiggle is disabled, otherwise it will delete the filesystem.

```console
kali@kali:~$ systemctl disable rpiwiggle
```

Now we can ensure that all the changes are written, then we can encrypt the disk.

```console
kali@kali:~$ sync
kali@kali:~$ exit
kali@kali:~$ umount /mnt/chroot/boot
kali@kali:~$ umount /mnt/chroot/sys
kali@kali:~$ umount /mnt/chroot/proc
kali@kali:~$ umount /mnt/chroot/dev/pts
kali@kali:~$ umount /mnt/chroot/dev
kali@kali:~$ mkdir -p /mnt/{backup,encrypted}
kali@kali:~$ rsync -avh /mnt/chroot/* /mnt/backup/
kali@kali:~$ cryptsetup luksClose crypt
kali@kali:~$ umount /mnt/chroot
kali@kali:~$ echo -e "d\n2\nw" | fdisk /dev/sdb
kali@kali:~$ partprobe
kali@kali:~$ sleep 5
kali@kali:~$ echo -e "n\np\n2\n\n\nw" | fdisk /dev/sdb
kali@kali:~$ partprobe
kali@kali:~$ sync
kali@kali:~$ cryptsetup -v -y --cipher aes-cbc-essiv:sha256 --key-size 256 luksFormat /dev/sdb2
kali@kali:~$ cryptsetup -v luksOpen /dev/sdb2 crypt
kali@kali:~$ mkfs.ext4 /dev/mapper/crypt
kali@kali:~$ mount /dev/mapper/crypt /mnt/encrypted/
kali@kali:~$ rsync -avh /mnt/backup/* /mnt/encrypted/
kali@kali:~$ sync
```

The final step that we have to do is remake the initramfs file, this step is important as it will not properly boot if not done.

```console
kali@kali:~$ mount /dev/sdb1 /mnt/encrypted/boot/
kali@kali:~$ mount -t proc none /mnt/encrypted/proc
kali@kali:~$ mount -t sysfs none /mnt/encrypted/sys
kali@kali:~$ mount -o bind /dev /mnt/encrypted/dev
kali@kali:~$ mount -o bind /dev/pts /mnt/encrypted/dev/pts
kali@kali:~$ LANG=C chroot /mnt/encrypted
kali@kali:~$ mkinitramfs -o /boot/initramfs.gz 4.19.93-Re4son-v7+
```

Now we can unmount and close up everything.

```console
kali@kali:~$ exit
kali@kali:~$ umount /mnt/encrypted/boot
kali@kali:~$ umount /mnt/encrypted/sys
kali@kali:~$ umount /mnt/encrypted/proc
kali@kali:~$ umount /mnt/encrypted/dev/pts
kali@kali:~$ umount /mnt/encrypted/dev
kali@kali:~$ umount /mnt/encrypted
kali@kali:~$ cryptsetup luksClose /dev/mapper/crypt
```

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
export _IMAGEURL=https://images.kali.org/arm-images/kali-linux-2021.2-rpi4-nexmon-64.img.xz
export _IMAGESHA="f5e126f33d32882f526e16b5148bd8b84a4e7c351bdd0eb9cfe3da2176580181"

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
