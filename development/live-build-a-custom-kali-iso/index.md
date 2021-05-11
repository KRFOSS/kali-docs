---
title: Live-Build a Custom Kali ISO
description:
icon:
type: post
weight: 51
author: ["g0tmi1k",]
---

## An Introduction to Building Your Own Kali ISO

Building a customized Kali ISO is easy, fun, and rewarding. You can configure virtually any aspect of your Kali ISO build using the Debian [live-build](https://live-team.pages.debian.net/live-manual/html/live-manual/index.en.html) scripts. These scripts allow developers to easily build live system images by providing a framework that uses a configuration set to automate and customize all aspects of building the image. The Kali Linux development team has adopted these scripts and they're used to produce the official Kali ISO releases.

### Where Should You Build Your ISO?

Ideally, you should build your custom Kali ISO from **within a pre-existing Kali environment**.

### Getting Ready — Setting Up The live-build System

We first need to prepare the Kali ISO build environment by installing and setting up live-build and its requirements with the following commands:

```console
kali@kali:~$ sudo apt update
kali@kali:~$ sudo apt install -y git live-build simple-cdd cdebootstrap curl
kali@kali:~$ git clone https://gitlab.com/kalilinux/build-scripts/live-build-config.git
```

Now you can simply build an updated Kali ISO by entering the "live-build-config" directory and running our **build.sh** wrapper script, as follows:

```console
kali@kali:~$ cd live-build-config/
kali@kali:~/live-build-config$ ./build.sh --verbose
```

The `build.sh` script will take a while to complete, as it downloads all of the required packages needed to create your ISO. Good time for a coffee. By default, it will generate a **Live Image** _(which uses live-build on the back end)_. If you want an **Installer Image** _(powered by simple-cdd)_:

```console
kali@kali:~/live-build-config$ ./build.sh --verbose --installer
```

### Configuring The Kali ISO Build (Optional)

If you want to customize your Kali Linux ISO, this section will explain some of the details. Through the **kali-config** directory, the Kali Linux live-build supports a wide range of customization options, which are well-documented on the Debian [live-build 4.x](https://live-team.pages.debian.net/live-manual/html/live-manual/customization-overview.en.html) page. However, for the impatient, here are some of the highlights.

#### Building Kali With Different Desktop Environments

Since Kali 2.0, we now support built in configurations for various desktop environments, including Xfce _(default)_, KDE, Gnome, E17, I3WM, LXDE, MATE. To build any of these, you would use syntax similar to the following:

```console
kali@kali:~/live-build-config$ # These are the different Desktop Environment build options:
kali@kali:~/live-build-config$ #./build.sh --variant {xfce,gnome,kde,mate,e17,lxde,i3wm} --verbose
kali@kali:~/live-build-config$
kali@kali:~/live-build-config$ # To build a Gnome ISO:
kali@kali:~/live-build-config$ ./build.sh --variant gnome --verbose
kali@kali:~/live-build-config$
kali@kali:~/live-build-config$ # To build a KDE ISO:
kali@kali:~/live-build-config$ ./build.sh --variant kde --verbose
```

#### Controlling The Packages Included In Your Build

The list of packages included in your build will be present in the the respective **kali-config** directory. For example:

- If you're building the default ISO (a Live image using Xfce), you would use the following package lists file - **kali-config/variant-default/package-lists/kali.list.chroot**.
- Sticking with a Live image, but this time using Gnome, you would need to use this package lists file - **kali-config/variant-gnome/package-lists/kali.list.chroot**.
- However, if you are wanting to alter the Installer image, you would need to use the following package lists file - **kali-config/installer-default/packages**.

By default, this lists includes the ["kali-linux-default" metapackage](/docs/general-use/metapackages/), as well as some others. These can be commented out and replaced with a manual list of packages to include in the ISO for greater granularity.

#### Build Hooks, Binary and Chroot

Live-build hooks allows us to hook scripts in various stages of the Kali ISO live-build. For more detailed information about hooks and how to use them, refer to the [live-build manual](https://live-team.pages.debian.net/live-manual/html/live-manual/customizing-contents.en.html#507). As an example, we recommend you check out the existing hooks in **kali-config/common/hooks/**.

#### Overlaying Files In Your Build

You have the option to include additional files or scripts in your build by overlaying them on the existing file-system, inside the **includes.{chroot,binary,installer}** directories, respectively. For example, if we wanted to include our own custom script into the **/root/** directory of the ISO (this would correspond to the "chroot" stage), then we would drop this script file in the **kali-config/common/includes.chroot/** directory before building the ISO.

### Building a Kali Linux ISO for Different Architectures

By default, Live-build will generate the image based on the architectures of the current operating system. If you wish to alter this:

- x64: `./build.sh --verbose --arch amd64`
- x86: `./build.sh --verbose --arch i386`

### Building a Kali Linux ISO for Older i386 Architectures

The Kali Linux i386 ISO has PAE enabled. If you require a default kernel for older hardware with PAE disabled, you will need to rebuild a Kali Linux ISO. The rebuilding process is much the same as described above, except that the **686-pae** parameter that needs to be changed to **586** in **auto/config** as follows. First, install the prerequisites.

```console
kali@kali:~$ sudo apt update
kali@kali:~$ sudo apt install -y git live-build simple-cdd cdebootstrap curl
kali@kali:~$ git clone https://gitlab.com/kalilinux/build-scripts/live-build-config.git
```

Next, make the change in `auto/config` for the appropriate architecture:

```console
kali@kali:~$ cd live-build-config/
kali@kali:~/live-build-config$ sed -i 's/686-pae/686/g' auto/config
```

Finally, run your build.

```console
kali@kali:~/live-build-config$ ./build.sh --verbose --arch i386
```

### Using A Custom Network Mirror For Building

If you decide to start building images, to help speed up the progress, you may wish to setup a [local network mirror](/docs/community/setting-up-a-kali-linux-mirror/). To instruct live-build to use it, with the assumption its at **http://192.168.0.101/kali**:

```console
kali@kali:~/live-build-config$ echo "http://192.168.0.101/kali/" > .mirror
kali@kali:~/live-build-config$ ./build.sh --verbose
```

- - -

## Building Kali on Non-Kali Debian Based Systems

You can easily run live-build on Debian based systems other than Kali Linux. The instructions below have been tested to work with both Debian and Ubuntu.

First, we prep the system by ensuring it is fully updated, then proceed to download the Kali archive keyring and live-build packages.

```console
$ sudo apt update
$ sudo apt upgrade
$ cd /root/
$
$ wget http://http.kali.org/pool/main/k/kali-archive-keyring/kali-archive-keyring_2018.2_all.deb
$ wget https://archive.kali.org/kali/pool/main/l/live-build/live-build_20190311_all.deb
```

With that completed, we install some additional dependencies and the previously downloaded files.

```console
$ sudo apt install -y git live-build cdebootstrap debootstrap curl
$ sudo dpkg -i kali-archive-keyring_2018.2_all.deb
$ sudo dpkg -i live-build_20190311_all.deb
```

With the environment all prepared, we start the live-build process by setting up the build script and checking out the build config.

```console
$ cd /usr/share/debootstrap/scripts/
$ echo "default_mirror http://http.kali.org/kali"; sed -e "s/debian-archive-keyring.gpg/kali-archive-keyring.gpg/g" sid > /tmp/kali
$ sudo mv /tmp/kali .
$ sudo ln -s kali kali-rolling
$
$ cd ~/
$ git clone https://gitlab.com/kalilinux/build-scripts/live-build-config.git
$
$ cd live-build-config/
```

At this point, we have to edit the `build.sh` script to bypass a version check. We do this by commenting out the "exit 1" below.

```console
$ # Check we have a good debootstrap
$ ver_debootstrap=$(dpkg-query -f '${Version}' -W debootstrap)
$ if dpkg --compare-versions "$ver_debootstrap" lt "1.0.97"; then
if ! echo "$ver_debootstrap" | grep -q kali; then
echo "ERROR: You need debootstrap >= 1.0.97 (or a Kali patched debootstrap). Your current version: $ver_debootstrap" >&2
exit 1
fi
fi
```

With that change made, the script should like as follows:

```console
$ # Check we have a good debootstrap
$ ver_debootstrap=$(dpkg-query -f '${Version}' -W debootstrap)
$ if dpkg --compare-versions "$ver_debootstrap" lt "1.0.97"; then
if ! echo "$ver_debootstrap" | grep -q kali; then
echo "ERROR: You need debootstrap >= 1.0.97 (or a Kali patched debootstrap). Your current version: $ver_debootstrap" >&2
# exit 1
fi
fi
```

At this point, we can build our ISO as normal

```console
$ sudo ./build.sh --variant light --verbose
```
