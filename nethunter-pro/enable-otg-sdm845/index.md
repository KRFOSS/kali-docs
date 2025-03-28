---
title: NetHunter Pro - Enable OTG in SDM845 (OnePlus6/6T and POCO F1)
description:
icon:
weight:
author: ["ShubhamVis98",]
---

### Video Guide

{{< youtube bjhgKxhgmIY >}}

### Install pre-requisites

```
sudo apt install -y abootimg device-tree-compiler
```
### NOTE:

A few devices, like the POCO F1, require externally powered OTG cables to function. So, make sure to connect an external power source to the OTG cable.

### Steps to modify the dtb

**Below are the steps for the POCO F1 EBBG variant. You can identify the variant by running the following command: `cat /proc/cmdline`**
**If your device uses A/B partitioning, you can use the `qbootctl` tool to determine the active partition.**

```
# Create a temporary working directory
TMPDIR=$(mktemp -d) && cd $TMPDIR

# Copy the dtb file of your device (eg berylium-ebbg for POCO F1 EBBG Variant)
cp /usr/lib/linux-image-$(linux-version list | tail -1)/qcom/sdm845-xiaomi-beryllium-ebbg.dtb orig.dtb

# Decompile/Convert dtb to dts file and change the dr_mode value
dtc -o tmp.dts orig.dtb
sed -i 's/dr_mode = "peripheral"/dr_mode = "host"/' tmp.dts

# Recompile the modified dts file to dtb
dtc -o host.dtb tmp.dts

# Merge kernel and modified dtb file into one file
cat /boot/vmlinuz-$(linux-version list | tail -1) host.dtb > kernel_dtb

# Get the flashed boot.img from boot partition
dd if=/dev/disk/by-partlabel/boot of=boot.img

# Update the kernel in boot.img file
abootimg -u boot.img -k kernel_dtb

# Flash the updated boot.img
dd if=boot.img of=/dev/disk/by-partlabel/boot
```

After performing above steps successfully, reboot your device.
