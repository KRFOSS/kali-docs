---
title: Modifying the Kernel
description:
icon:
date: 2019-10-26
type: post
weight: 100
author: ["g0tmi1k",]
tags: ["",]
keywords: ["",]
og_description:
---

We will continue from the [Porting NetHunter page](http://localhost:1414/docs/nethunter/porting-nethunter/) and work on the Galaxy Note 3 kernel as an example. The idea stays the same though: replace the _defconfig_ with one that is used by your kernel.

Let's start fresh:

```html
make clean
make msm8974_sec_defconfig
make menuconfig
```

## Patching

By default, we use the mac80211 injection patch. You can apply this patch as follows:

```bash
wget http://patches.aircrack-ng.org/mac80211.compat08082009.wl_frag+ack_v1.patch
patch -p1 < mac80211.compat08082009.wl_frag+ack_v1.patch
```

Other patches are [HID patches for keyboard support](https://github.com/pelya/android-keyboard-gadget) and CDROM patch.

## Configure Builds
We are now presented with options to modify the kernel. Since I ran into the Touch Wake problem, I will show how easy it was to remove it. This is what the error looked like:

```html
$ /note3/drivers/misc/touch_wake.c:539: undefined reference to `register_power_suspend'
```

We can see that it's located in the **drivers/misc** folder so it works the same in **menuconfig**. Go to _Device Drivers_ in the menu then de-select _Touch Wake_ with the spacebar:

![Removing touch wake](http://i.imgur.com/decVf1d.png)

### Defaults

The first options to check are in _General Setup_. Check that _System V IPC_ is enabled and feel free to change the hostname to "kali". Use the spacebar to enable the `*` next to what you want to enable:

![General Setup](http://i.imgur.com/suxbpl5.png)

### Modules

Next, we want to enable modules in _Enable Loadable Module Support_ just in case there are any devices we want to load through the command line using **modprobe**. The correct options look like this:

![Modules](http://i.imgur.com/xyKZrN2.png)

### MAC80211

_Networking Support_ is where we go to enable support for most of the network devices we are adding. Go to _Wireless_ (you may need to de-select some of the modules in the _Wireless_ menu as you don't want `M` unless you want to load a module each time your device starts). It should look like this:

![](http://i.imgur.com/YiAL9Ue.png)

### Bluetooth

While still in _Networking Support_, navigate to _Bluetooth subsystem Support_, then _Bluetooth Device Drivers_. Set the options as shown below:

![](http://i.imgur.com/nRr5hz6.png)

### Ethernet

Navigate to _Device Drivers_ -> _Network Device Support_ ->  _USB Network Adapters_ and configure the following options:

![USB Ethernet](http://i.imgur.com/7qzmwTB.png)

### Wireless LAN

Navigate to _Device Drivers_ -> _Network Device Support_ ->  _Wireless LAN_ and make the following selections:

![Top](http://i.imgur.com/fML0zM1.png)
![Bottom](http://i.imgur.com/LRFpqEE.png)

![Atheros](http://i.imgur.com/Gc5bZ6V.png)
![RALink](http://i.imgur.com/BALJr8p.png)


### SDR

Choose _Device Drivers_ then _Multimedia_ and select the following:

![](http://i.imgur.com/x5PlnWQ.png)

Navigate to _Device Drivers_ -> _Multimedia_ -> _DVB/ATSC adapters_ -> _Customize DVB Frontends_ and ensure the following is selected:

![RTL-SDR](http://i.imgur.com/KPFo66j.png)

### USB Modem - CDC ACM support is required for Proxmark and similar devices

Navigate to _Device Drivers_ -> _USB support_ and select the option "USB Modem (CDC ACM) support":

![CDC_ACM](https://user-images.githubusercontent.com/12821486/53321772-7de54600-392d-11e9-9aae-e09954e5d9a1.png)

### USB Gadget support - Generic serial, CDC ACM, CDC ECM, and HID are required for various USB ethernet gadget functions:

Navigate to _Device Drivers_ -> _USB support_ -> _USB Gadget Support_ and select "Ethernet Control Model (CDC ECM)":

![CDC_ECM](/uploads/e932f3d564ee6f8c26cdc15eeb1e89f2/ecm.png)

## Save and Rebuild

![http://i.imgur.com/fwqE6m8.png](http://i.imgur.com/fwqE6m8.png)

Save your new kernel configuration as **.config** then start your kernel build using the new config:

```markdown
make
```

If it builds successfully, then we can save the new config file:

```markdown
cp .config arch/arm/configs/kali_defconfig
```

In the future, if we need to build/modify a new kernel, we can just do the following:

```markdown
make clean
make kali_defconfig
make # or if you want to modify make menuconfig
```

You should now be the proud owner of a new kernel in **arch/arm/boot/zImage** or **arch/arm/boot/zImage-dtb**. We prefer to use the -dtb extension if given the choice.
