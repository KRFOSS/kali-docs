---
title: Raspberry Pi - Full Disk Encryption
description:
icon:
type: post
weight:
author: ["gamb1t",]
---

{{% notice info %}}
The following documentation is not yet working. Follow along with the status at the following link: https://gitlab.com/kalilinux/documentation/kali-docs/issues/49
{{% /notice %}}

Last year we made a blog post, [Secure Kali Pi 2018](https://www.kali.org/tutorials/secure-kali-pi-2018/), covering how to encrypt a Kali RPi install. Since then there have been a few developments. One important note is that unixabg has created a script to automate the process. We will touch more on that after going through the manual method, however we recommend reading what is being done still.

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

```
wget https://images.offensive-security.com/arm-images/kali-linux-$version-rpi3-nexmon.img.xz

xzcat kali-linux-$version-rpi3-nexmon.img.xz | dd of=/dev/sdb bs=4M
```

Next we are going to get things ready for chroot. Let's create where we want to mount the SD card then mount it.

```
mkdir -p /mnt/chroot/boot
mount /dev/sdb2 /mnt/chroot/
mount /dev/sdb1 /mnt/chroot/boot/
mount -t proc none /mnt/chroot/proc
mount -t sysfs none /mnt/chroot/sys
mount -o bind /dev /mnt/chroot/dev
mount -o bind /dev/pts /mnt/chroot/dev/pts
apt install -y qemu-user-static
cp /usr/bin/qemu-arm-static /mnt/chroot/usr/bin/
```

# Doing the Magic Fu

Now that our system is set up we can use chroot to set up the RPi image for encryption. Let's first chroot in and install some necessary packages.

```
LANG=C chroot /mnt/chroot/
sudo apt update
sudo apt install -y cryptsetup lvm2 busybox dropbear
```

We will now be listing out five kernel versions and depending on the RPi being used you will need to choose certain versions. The first version, Re4son+, is for armv6 devices IE. RPi1, RPi0, or RPi0w. The next two, Re4son-v7+ and Re4son-v8+, are the 32-bit and 64-bit versions for armv7 devices, respectfully. The final two will be the ones merged into the armv7 32bit and 64bit versions, and the `l` in the name means they will be for the RPi4. Keep in mind the kernel versions may change, however the name will not.

```
ls -l /lib/modules/ | awk -F" " '{print $9}'
4.19.81-Re4son+
4.19.81-Re4son-v7+
4.19.81-Re4son-v7l+
4.19.81-Re4son-v8+
4.19.81-Re4son-v8l+
echo initramfs initramfs.gz followkernel >> /boot/config.txt
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

```
echo -e 'crypt\t/dev/mmcblk0p2\tnone\tluks' > /etc/crypttab
```

Now we do a little filesystem trickery. We create a fake LUKS filesystem which forces cryptsetup to be included.

```
dd if=/dev/zero of=/tmp/fakeroot.img bs=4M count=20
exit
cryptsetup luksFormat /mnt/chroot/tmp/fakeroot.img
cryptsetup luksOpen /mnt/chroot/tmp/fakeroot.img crypt
mkfs.ext4 /mnt/chroot/dev/mapper/crypt
```

After that we need to copy over, or generate, an ssh key to be added to dropbear's authorized_keys file.

```
cp id_rsa.pub /mnt/chroot/
LANG=C chroot /mnt/chroot/
```

Next we must add the following to /etc/dropbear-initramfs/authorized_keys:

```console
kali@kali:~$ vim /etc/dropbear-initramfs/authorized_keys
kali@kali:~$ cat /etc/dropbear-initramfs/authorized_keys
command="export PATH='/sbin:/bin/:/usr/sbin:/usr/bin'; /scripts/local-top/cryptroot && kill -9 `ps | grep -m 1 'cryptroot' | cut -d ' ' -f 3` && exit"
```

After doing so, we can append the ssh key that we copied over and then remove it from the card.

```
cat id_rsa.pub >> /etc/dropbear-initramfs/authorized_keys && rm id_rsa.pub
```

Once you're done, `/etc/dropbear-initramfs/authorized_keys` should look like this (Note: you may need to delete a return after the command so the ssh key follows directly after):

```console
kali@kali:~$ cat /etc/dropbear-initramfs/authorized_keys
command="export PATH='/sbin:/bin/:/usr/sbin:/usr/bin'; /scripts/local-top/cryptroot && kill -9 `ps | grep -m 1 'cryptroot' | cut -d ' ' -f 3` && exit" ssh-rsa key user@sys
```

We now need to edit `/usr/share/initramfs-tools/scripts/init-premount/dropbear` to add a sleep timer, this allows for networking to start *before* dropbear does.

```
[ "$BOOT" != nfs ] || configure_networking
sleep 5
run_dropbear &
echo $! >/run/dropbear.pid
```

Let's now enable cryptsetup.

```
echo CRYPTSETUP=y > /etc/cryptsetup-initramfs/conf-hook

kali@kali:~$ cat /etc/cryptsetup-initramfs/conf-hook
CRYPTSETUP=y
```

Now we need to create the initramfs. This is where the kernel versions from before come into play.

```
mkinitramfs -o /boot/initramfs.gz 4.19.93-Re4son-v7+
```

Now we want to ensure that we created the initramfs corectly. If there is no result, then something went wrong.

```
lsinitramfs /boot/initramfs.gz | grep cryptsetup
lsinitramfs /boot/initramfs.gz | grep authorized
```
Before we can backup, we have to ensure that rpiwiggle is disabled, otherwise it will delete the filesystem.

```
systemctl disable rpiwiggle
```

Now we can ensure that all the changes are written, then we can encrypt the disk.

```
sync && sync
exit
umount /mnt/chroot/boot
umount /mnt/chroot/sys
umount /mnt/chroot/proc
umount /mnt/chroot/dev/pts
umount /mnt/chroot/dev
mkdir -p /mnt/{backup,encrypted}
rsync -avh /mnt/chroot/* /mnt/backup/
cryptsetup luksClose crypt
umount /mnt/chroot
echo -e "d\n2\nw" | fdisk /dev/sdb
partprobe
sleep 5
echo -e "n\np\n2\n\n\nw" | fdisk /dev/sdb
partprobe
sync && sync
cryptsetup -v -y --cipher aes-cbc-essiv:sha256 --key-size 256 luksFormat /dev/sdb2
cryptsetup -v luksOpen /dev/sdb2 crypt
mkfs.ext4 /dev/mapper/crypt
mount /dev/mapper/crypt /mnt/encrypted/
rsync -avh /mnt/backup/* /mnt/encrypted/
sync
```

The final step that we have to do is remake the initramfs file, this step is important as it will not properly boot if not done.

```
mount /dev/sdb1 /mnt/encrypted/boot/
mount -t proc none /mnt/encrypted/proc
mount -t sysfs none /mnt/encrypted/sys
mount -o bind /dev /mnt/encrypted/dev
mount -o bind /dev/pts /mnt/encrypted/dev/pts
LANG=C chroot /mnt/encrypted
mkinitramfs -o /boot/initramfs.gz 4.19.93-Re4son-v7+
```
Now we can unmount and close up everything.

```
exit
umount /mnt/encrypted/boot
umount /mnt/encrypted/sys
umount /mnt/encrypted/proc
umount /mnt/encrypted/dev/pts
umount /mnt/encrypted/dev
umount /mnt/encrypted
cryptsetup luksClose /dev/mapper/crypt
```
# LUKS NUKE

Should a user also want [LUKS NUKE](https://www.kali.org/tutorials/nuke-kali-linux-luks/), all they need to do is run the following command.

```
dpkg-reconfigure cryptsetup-nuke-password
```

# Automation?

Now how about we get this automated? Thanks to Richard Nelson (unixabg), anyone who wants to get this all set up in much less time than the manual method and much easier, can!

First things first, let's download [unixabg's cryptmypi](https://github.com/unixabg/cryptmypi) script.

```
git clone https://github.com/unixabg/cryptmypi.git
```

There are a number of things we want to do before we can run the build scripts however. Let's go through those together now:

```
cd cryptmypi
cp cryptmypi.conf config
cd config
cat ~/.ssh/id_rsa.pub >> authorized_keys
```

Now we need to edit cryptmypi.conf to change some settings in stage-2. These settings will be personal, but let's just give you all an example.

```
cat cryptmypi.conf
##################
## cryptmypi settings
##################
# export prefix for hooks
export _VER="2.2-beta"

# base and build
export _BASEDIR=$(pwd)
export _BUILDDIR=${_BASEDIR}/cryptmypi-build

##################
## Stage-1
##################
_IMAGEURL=https://images.offensive-security.com/arm-images/kali-linux-$version-rpi3-nexmon-64.img.xz

# compose package actions
export _PKGSPURGE=""
export _PKGSINSTALL=""

# iodine settings
_IODINE_PASSWORD="your iodine password goes here"
_IODINE_DOMAIN="your iodine domain goes here"

# final package actions
export _FINALPKGPURGE=""
export _FINALPKGINSTALL="telnet dsniff bettercap"

##################
## Stage-2
##################
# block device
_BLKDEV="/dev/sdb"

# luks encryption cipher
_LUKSCIPHER="aes-cbc-essiv:sha256"

# luks encryption password
_LUKSPASSWD="toor"

# root password
export _ROOTPASSWD="toor"
```

What we changed here is the block device, luks encryption password, and the root password. The image URL can be changed if you would like to use a different image file, so be sure to do that now if need be.

Now the only thing left to do is run both stages' scripts and follow the instructions. By the end of it, you'll have a fully encrypted filesystem with dropbear SSH access.
