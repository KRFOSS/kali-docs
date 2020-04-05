---
title: Building a New Device File
description:
icon:
date: 2019-11-29
type: post
weight: 1000
author: ["g0tmi1k",]
tags: ["",]
keywords: ["",]
og_description:
---

The device file is a bash script that builds the kernel based off the sources you defined and copies over your edify installer. The edify installer is what allows you to flash the kernel in TWRP/Clockwork Mod.

------------------

At around line 50 in **androidmenu.sh**, you will see source/links to device files. All device/kernel build scripts reside in the devices folder. Here is how a typical nexus is broken down in devices/nexusXX:

```html
f_nexusXX_kernel(){
```
This is the function that starts the build. It's called in the **androidmenu.sh** main menu.

The next part is the frozen kernel option and update script checker. This is not crucial to the build script so will not be discussed in these instructions.

```html
f_kernel_build_init
```

This downloads the android toolchain/copies typical kernel build templates

```markdown
if [ $LOCALGIT == 1 ]; then
  echo "Copying kernel to rootfs"
  cp -rf ${basepwd}/XXXXXXXXXXXX ${basedir}/kernel
else
  git clone https://github.com/XXXXXXX/XXXXXXX.git -b XXXXXX ${basedir}/kernel
fi
cd ${basedir}/kernel
```

This is where you will want to define the kernel folder. You have two options:  Ccpy a local kernel folder or download one from GitHub. _Localgit_ is set in **androidmenu.sh** and you will have to have a copy of the kernel folder in order for it to work. Change the "XX" to the name of the kernel.

```markdown
make clean
sleep 10
make kali_defconfig
```

Now we are building the kernel. We `make clean` to start fresh then use the configuration file _kali_defconfig_. The config files live in **arch/arm/configs** in the **kernel** folder. We are getting a bit ahead of ourselves though!

```bash
# Attach kernel builder to updater-script
echo "#KERNEL_SCRIPT_START" >> ${basedir}/flashkernel/META-INF/com/google/android/updater-script
cat << EOF > ${basedir}/flashkernel/META-INF/com/google/android/updater-script
# <-- This is where the kernel installer will go
EOF
```

We attach the edify script that will be used to install the kernel. It's best to see an example by looking at another device.

```html
f_kernel_build
fi
}
```

This is the final portion. It will copy the kernel to the **flashable** folder then zip up everything for you.
