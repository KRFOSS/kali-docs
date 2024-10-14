---
title: Adding your device
description:
icon:
weight: 36
author: ["yesimxev",]
---

## Adding your own device to the devices repo

Congratulations! You've successfully built your own Kali NetHunter kernel. Feel free to add it to our kernel repo, supporting the community.

Please follow the instructions on the [kernels repository](https://gitlab.com/kalilinux/nethunter/build-scripts/kali-nethunter-kernels).

Useful example of a recent unofficial device support [merge request](https://gitlab.com/kalilinux/nethunter/build-scripts/kali-nethunter-kernels/-/merge_requests/293)

If you used the kernel builder, your new kernel image can be found in ***out/arch/arm64/boot/***, and your modules are in ***modules_out/lib/modules/***.

Be sure to test if you can create a working NetHunter installer zip before submitting your commits. 
