---
title: Raspberry Pi - Disk Encryption
description:
icon:
archived: "true"
weight:
author: ["steev",]
---

With the advent of smaller, faster ARM hardware such as the new **Raspberry Pi 2 (or even 3!)** (which now has a Kali image built for it), we've been seeing more and more use of these small devices as "**throw-away hackboxes**". While this might be a new and novel technology, **there's one major drawback** to this concept - and that is the **confidentiality of the data** stored on the device itself. Most of the setups we've seen do little to protect the sensitive information saved on the SD cards of these little computers. This fact, together with a nudge from friends is what prompted us to create a LUKS encrypted, NUKE capable Kali Linux image for our Raspberry Pi devices. The following article describes the process, so you can repeat it and make your own shiny shiny.

### Birds Eye View of the Disk Encryption Process

The process described below was tried and **tested successfully on a Raspberry Pi B+ and a Raspberry Pi 2/3** (henceforth collectively called "RPi"). but it should be trivial to port these instructions to any ARM device running Kali Linux. Before we begin, let's take a minute to quickly describe what we'll be doing - as while this process is not complicated, it **is involved**. This is basically our spiel:

1. We [download](/get-kali/) the required Kali RPi image and **dd** it to an SD card.
2. We chroot to the RPi image and install/update several files in preparation for our encrypted boot.
3. We create an initramfs file which includes Dropbear and freshly generated SSH keys.
4. We rsync the modified rootfs to a temporary backup location and then delete the rootfs partition from the SD card.
5. We then recreate an encrypted partition to which we restore the root partition data. That's it!

If all goes well, the RPi will boot and then LUKS will kick in and ask for a password to decrypt the root drive, while simultaneously opening a Dropbear SSH session through which **you can SSH in and provide the boot decryption password**. Oh yeah, did we mention this image also has **LUKS NUKE capabilities**?

### Getting Your Hands Dirty

As always, all our ARM dev is done on a Kali amd64 machine and we've made sure that we have all the [dependencies](https://gitlab.com/kalilinux/build-scripts/kali-arm/blob/main/build-deps.sh) we need. We [download the latest Kali RPi3 image](/get-kali/) (2019.4), extract it, and **dd** it to our SD card, which in our case showed up as /dev/sdb2 - adapt as necessary!

```console
$ dd if=kali-linux-2024.3-rpi3-nexmon.img of=/dev/sdb conv=fsync bs=4M
```

Once dd'd, we mount the various partitions and chroot into the Kali RPi3 image:

```console
kali@kali:~$ mkdir -p /mnt/chroot/boot
kali@kali:~$
kali@kali:~$ mount /dev/sdb2 /mnt/chroot/
kali@kali:~$ mount /dev/sdb1 /mnt/chroot/boot/
kali@kali:~$
kali@kali:~$ mount -t proc none /mnt/chroot/proc
kali@kali:~$ mount -t sysfs none /mnt/chroot/sys
kali@kali:~$ mount -o bind /dev /mnt/chroot/dev
kali@kali:~$ mount -o bind /dev/pts /mnt/chroot/dev/pts
kali@kali:~$ sudo apt install -y qemu-user-static
kali@kali:~$
kali@kali:~$ cp /usr/bin/qemu-arm-static /mnt/chroot/usr/bin/
kali@kali:~$ LANG=C chroot /mnt/chroot/
```

We then update our image and install some essential packages we will need for this process:

```console
kali@kali:~$ sudo apt update
kali@kali:~$ sudo apt install -y busybox cryptsetup dropbear-initramfs cryptsetup-nuke-password
```

We create an initial initramfs file, which will trigger the dropbear SSH key generation. We first find out the modules directory version number as follows (this will change between different image versions and Kali releases):

```console
kali@kali:~$ ls -l /lib/modules/ | awk -F" " '{print $9}'
4.9.59-Re4son-Kali-Pi+
```

We then use that version info to generate an initial initramfs file:

```console
kali@kali:~$ mkinitramfs -o /boot/initramfs.gz 4.9.59-Re4son-Kali-Pi+
```

We change the default root password:

```console
kali@kali:~$ sudo passwd root
```

Next, we modify the boot parameters in cmdline.txt and config.txt:

```console
kali@kali:~$ vim /boot/cmdline.txt
```

...and add / change the following parameters:

```console
kali@kali:~$ root=/dev/mapper/crypt_sdcard cryptdevice=/dev/mmcblk0p2:crypt_sdcard rootfstype=ext4
```

Next create or add to /boot/config.txt:

```console
kali@kali:~$ echo initramfs initramfs.gz >> /boot/config.txt
```

Now we deal with the Dropbear SSH access. We copy over SSH private key from our laptop, or, create one specifically for doing this:

#### Copying:

```console
kali@kali:~$ cp /root/.ssh/id_rsa.pub /etc/dropbear-initramfs/authorized_keys
kali@kali:~$ chmod 0600 /etc/dropbear-initramfs/authorized_keys
```

Creating (on the host machine, **NOT** in the chroot:

```console
kali@kali:~$ ssh-keygen -N "" -f kali-luks-unlock
kali@kali:~$ cat kali-luks-unlock.pub > /mnt/chroot/etc/dropbear-initramfs/authorized_keys
kali@kali:~$ chmod 0600 /mnt/chroot/etc/dropbear-initramfs/authorized_keys
```

And limit the SSH connection to allow interaction with the cryptroot application only:

```console
kali@kali:~$ vim /etc/dropbear-initramfs/authorized_keys
```

We paste the following **before** the ssh public key begins:

```plaintext
command="/scripts/local-top/cryptroot && kill -9 `ps | grep -m 1 'cryptroot' | cut -d ' ' -f 3`"
```

Then to ensure we get cryptsetup in the initramfs, we edit the cryptsetup initramfs hook:

```console
kali@kali:~$ echo "CRYPTSETUP=y" >> /etc/cryptsetup-initramfs/conf-hook
```

We then edit fstab and crypttab with our configured boot device and exit the chroot:

```console
kali@kali:~$ cat > /etc/fstab < /etc/crypttab
```

During our tests, we noticed that in some instances, the USB ports take a while to wake up, which can kill the initrd Dropbear network initialization. To fix this, we introduce a 5-second sleep in the configure_networking function located in the initrd itself:

```console
kali@kali:~$ vim /usr/share/initramfs-tools/scripts/functions
```

change:

```plaintext
configure_networking()
{
[...]
```

to:

```plaintext
configure_networking()
{

echo "Waiting 5 seconds for USB to wake"
sleep 5
[...]
```

{{% notice info %}}
Do NOT add the "..." they are a placeholder to mean there is more stuff there, that we aren't editing.
{{% /notice %}}

Save the file then regenerate the initramfs and exit the chroot. You can ignore the cryptsetup and device-mapper warnings:

```console
kali@kali:~$ mkinitramfs -o /boot/initramfs.gz 4.9.59-Re4son-Kali-Pi+
kali@kali:~$ exit
```

Now we proceed to tear down the chroot and backup our rootfs partition:

```console
kali@kali:~$ umount /mnt/chroot/boot
kali@kali:~$ umount /mnt/chroot/sys
kali@kali:~$ umount /mnt/chroot/proc
kali@kali:~$ umount /mnt/chroot/dev/pts
kali@kali:~$ umount /mnt/chroot/dev
kali@kali:~$ mkdir -p /mnt/backup
kali@kali:~$ rsync -avh /mnt/chroot/* /mnt/backup/
```

Once the backup is done, we unmount everything:

```console
kali@kali:~$ umount /mnt/chroot
```

Now we delete the existing 2nd partition on the SD card and recreate an empty one, which we will set up for LUKS encryption:

```console
kali@kali:~$ echo -e "d\n2\nw" | fdisk /dev/sdb
kali@kali:~$ echo -e "n\np\n2\n\n\nw" | fdisk /dev/sdb
```

Unplug your SD card and plug it back in to have the new partitions register, then start setting up your encrypted partition:

```console
kali@kali:~$ cryptsetup -v -y --cipher aes-xts-plain64 --key-size 256 luksFormat /dev/sdb2
kali@kali:~$ cryptsetup -v luksOpen /dev/sdb2 crypt_sdcard
kali@kali:~$ mkfs.ext4 /dev/mapper/crypt_sdcard
```

Once ready, we restore the rootfs backup to the now encrypted partition:

```console
kali@kali:~$ mkdir -p /mnt/encrypted
kali@kali:~$ mount /dev/mapper/crypt_sdcard /mnt/encrypted/
kali@kali:~$ rsync -avh /mnt/backup/* /mnt/encrypted/
kali@kali:~$ umount /mnt/encrypted/
kali@kali:~$ rm -rf /mnt/backup
kali@kali:~$ sync
```

Then we unmount and close the volume:

```console
kali@kali:~$ cryptsetup luksClose /dev/mapper/crypt_sdcard
```

### That's it!

Now all that remains is to boot up the RPi using the modified SD card. The initramfs will load Dropbear and get a DHCP address on your local LAN (you can also hardcode an IP), allowing you to SSH to the booting RPi and enter a decryption password. Once the password is accepted, Dropbear will exit and the RPi will continue to boot. You should see something like the following:

```console
kali@kali:~$ ssh -i key 192.168.13.37
The authenticity of host '192.168.13.37 (192.168.13.37)' can't be established.
RSA key fingerprint is a6:a2:ad:7d:cb:d8:70:58:d1:ed:81:e8:4a:d5:23:3a.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '192.168.13.37' (RSA) to the list of known hosts.
Unlocking the disk /dev/mmcblk0p2 (crypt_sdcard)
Enter passphrase: cryptsetup: crypt_sdcard set up successfully
Connection to 192.168.13.37 closed.
kali@kali:~$
```

### Can I Have Some LUKS NUKE With That Pi?

If you are not familiar with the [Kali Linux LUKS NUKE](/blog/nuke-kali-linux-luks/) feature then you're missing out. Although this stage is optional, it allows you to configure and apply an emergency self-destruct password to your LUKS encrypted drive. To do this, we simply define a Nuke password with our special purpose package cryptsetup-nuke-password:

```console
kali@kali:~$ dpkg-reconfigure cryptsetup-nuke-password
```

With the Nuke password defined, you can now remotely wipe the LUKS decryption keyslots, making the data on the SD card inaccessible.

### Raspberry Pi Disk Encryption Video

In order to give a bit more visual context to the process, we made a short video which shows the sequence of commands used to get LUKS disk encryption working on a Raspberry Pi B+. Enjoy!

Setting up LUKS disk encryption on a Raspberry Pi running Kali Linux. Also supports LUKS Nuke features!

{{< vimeo 121449299 >}}

### References

We came up with this procedure by cannibalising ideas and instructions from various sources on the net, most notably, the two below. Big thanks to the Raspberry Pi community!

1. [ofthedeed.org/posts/Encrypted_Raspberry_Pi/](https://www.ofthedeed.org/posts/Encrypted_Raspberry_Pi/)
2. [raspberrypi.org/forums/viewtopic.php?f=28&t=7626](https://www.raspberrypi.org/forums/viewtopic.php?f=28&t=7626)
