---
title: Samsung Galaxy Note 10.1
description:
icon:
type: archived
weight:
author: ["steev",]
build-script: N/A
headless: kali-desktop-xfce
metapackage: N/A
status: eol
cpu:
gpu:
ram:
ethernet:
wifi:
bluetooth:
usb3:
usb2:
storage:
---

{{% notice info %}}
Kali Linux is no longer providing pre-built images or a build-script to generate your own image.
This hardware is no longer supported.
The page is left for historical value.
{{% /notice %}}

- - -

The Samsung Galaxy Note 10.1 is a 10.1-inch tablet computer designed, developed, and marketed by Samsung. The tablet incorporates a 1.4 GHz quad-core Exynos processor and 2GB of RAM. The touch screen works surprisingly well with Kali as well as the wireless card, however Bluetooth and audio are not yet functional on this image.

By default, the Kali Linux Samsung Galaxy Note 10.1 image **does not** contains the [**kali-linux-default** metapackage](/docs/general-use/metapackages/) which is often found in Kali platforms. If you wish to install extra tools please refer to our [metapackages page](/docs/general-use/metapackages/).

## Kali on Galaxy Note 10.1 - User Instructions

If you're unfamiliar with the details of [downloading and validating a Kali Linux image](/docs/introduction/download-official-kali-linux-images/), or for [using that image to create a bootable device](/docs/usb/live-usb-install-with-windows/), it's strongly recommended that you refer to the more detailed procedures described in the specific articles on those subjects.

To install a pre-built image of the standard build of Kali Linux on your Galaxy Note 10.1, follow these instructions:

1. You'll need **at least 7GB free** on your internal full-size SD card for our image.
2. Root your Samsung Galaxy Note 10.1 if you have not already done so.
3.Download _and validate_ the `Kali Galaxy Note 10.1` image from the [downloads](https://www.offensive-security.com/kali-linux-vmware-arm-image-download/) area. The process for validating an image is described in more detail on [Downloading Kali Linux](/docs/introduction/download-official-kali-linux-images/).
4. Rename the downloaded Kali image to `linux.img` and copy it to `/storage/sdcard0`.
5. Download our `recovery.img` file from here and copy it to `/storage/sdcard0`.
6. Get root on your Galaxy Note 10.1, change `/storage/sdcard0`, and backup your recovery partition:

```console
$ dd if=/dev/block/mmcblk0p6 of=recovery.img_orig
```

7. **[dd](https://packages.debian.org/testing/dd)** the downloaded `recovery.img` image to the recovery partition:

{{% notice info %}}
**Alert!** This process will overwrite your recovery partition. Please make sure you know what you are doing. You may brick your device otherwise.
{{% /notice %}}

```console
$ dd if=recovery.img of=/dev/block/mmcblk0p6
```

8. Reboot your Galaxy Note 10.1 into recovery mode. You can do this by **turning it off**, then press and hold both the **power button** and the **volume up** button. Once you see the "Samsung Galaxy Note 10.1" text appear, **release the power button but keep pressing the volume up button**. This should boot you into Kali and auto-login into GNOME. The [root password](/docs/introduction/kali-linux-default-passwords/) is `changeme`
9. Open the on-screen keyboard by going to : **Applications** -> **Universal Access** -> **Florence Virtual Keyboard**.
10. Wireless works but seems to skip the scanning of networks without some massaging. **If the GNOME Network Manager shows no wireless networks**, simply add your wireless network as a "hidden" one and you should get connected as usual.
11. You can modify, debug, and explore our image easily from within your Galaxy Note, using a Android App such as [Linux Deploy](https://play.google.com/store/apps/details?id=ru.meefik.linuxdeploy&hl=en).
