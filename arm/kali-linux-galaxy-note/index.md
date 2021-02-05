---
title: Galaxy Note 10.1
description:
icon:
type: archived
weight:
author: ["steev",]
---

The Samsung Galaxy Note 10.1 is a 10.1-inch tablet computer designed, developed, and marketed by Samsung. The tablet incorporates a 1.4 GHz quad-core Exynos processor and 2 GB of RAM. The touch screen works surprisingly well with Kali as well as the wireless card, however Bluetooth and audio are not yet functional on this image.

## Stock Kali on Galaxy Note 10.1 - Easy Version

If all you want to do is to install Kali on your Galaxy Note 10.1, follow these instructions:

1. You'll need **at least 7 GB free** on your internal SD card for our image.
2. Root your Samsung Galaxy Note 10.1 if you have not already done so.
3. Download the **Kali Linux Galaxy Note 10.1** image from our [downloads](https://www.offensive-security.com/kali-linux-vmware-arm-image-download/) area.
4. Rename the downloaded Kali image to **linux.img** and copy it to `/storage/sdcard0`.
5. Download our `recovery.img` file from here and copy it to `/storage/sdcard0`.
6. Get root on your Galaxy Note 10.1, change /storage/sdcard0, and backup your recovery partition:

```
dd if=/dev/block/mmcblk0p6 of=recovery.img_orig
```

7. **dd** the downloaded recovery.img image to the recovery partition:

{{% notice info %}}
**Alert!** This process will overwrite your recovery partition. Please make sure you know what you are doing. You may brick your device if you fumble this.
{{% /notice %}}

```
dd if=recovery.img of=/dev/block/mmcblk0p6
```

8. Reboot your Galaxy Note 10.1 into recovery mode. You can do this by **turning it off**, then press and hold both the **power button** and the **volume up** button. Once you see the "Samsung Galaxy Note 10.1" text appear, **release the power button but keep pressing the volume up button**. This should boot you into Kali and auto-login into Gnome. The root password is "changeme" (without the quotes!)
9. Open the onscreen keyboard by going to : **Applications** -> **Universal Access** -> **Florence Virtual Keyboard**.
10. Wireless works but seems to skip the scanning of networks without some massaging. **If the Gnome Network Manager shows no wireless networks**, simply add your wireless network as a "hidden" one and you should get connected as usual.
11. You can modify, debug, and explore our image easily from within your Galaxy Note, using a wonderful Android App called **[Linux Deploy](https://play.google.com/store/apps/details?id=ru.meefik.linuxdeploy&hl=en)**.
