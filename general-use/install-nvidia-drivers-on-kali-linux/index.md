---
title: Install NVIDIA GPU Drivers
description:
icon:
date: 2019-10-26
type: post
weight: 10
author: ["g0tmi1k",]
tags: ["",]
keywords: ["",]
og_description:
---

Do not attempt this in a VM. It is [possible in theory](https://mathiashueber.com/windows-virtual-machine-gpu-passthrough-ubuntu/), however this likely will not work and we do not recommend that users attempt this. 

This document explains how to install NVIDIA GPU drivers and CUDA support, allowing integration with popular penetration testing tools.

#### Prerequisites

First, you'll need to ensure that your system is fully upgraded and that your card supports [CUDA](https://developer.nvidia.com/cuda-gpus).

{{% notice info %}}
GPUs with a <a href=https://developer.nvidia.com/cuda-gpus> CUDA compute capability </a> > 5.0 are recommended, but GPUs with less will still work.
{{% /notice %}}

```markdown
apt update && apt dist-upgrade -y && reboot
```

Let's determine the exact GPU installed, and check the kernel modules it's using.

```markdown
root@kali:~# lspci -v
01:00.0 VGA compatible controller: NVIDIA Corporation GM204 [GeForce GTX 970] (rev a1) (prog-if 00 [VGA controller])
  Subsystem: ZOTAC International (MCO) Ltd. GM204 [GeForce GTX 970]
  Region 1: Memory at e0000000 (64-bit, prefetchable) [size=256M]
  Capabilities: [60] Power Management version 3
  Capabilities: [68] MSI: Enable+ Count=1/1 Maskable- 64bit+
  Capabilities: [78] Express (v2) Legacy Endpoint, MSI 00
  Capabilities: [600 v1] Vendor Specific Information: ID=0001 Rev=1 Len=024
  Kernel driver in use: nouveau
  Kernel modules: nouveau
```

#### Installation

Once the system has rebooted, we will proceed to install the **OpenCL ICD Loader**, **Drivers**, and the **CUDA toolkit**.

```markdown
apt install -y ocl-icd-libopencl1 nvidia-driver nvidia-cuda-toolkit
```

During installation of the drivers the system created new kernel modules, so another reboot is required.

#### Verify Driver Installation

Now that our system should be ready to go, we need to verify the drivers have been loaded correctly. We can quickly verify this by running the [nvidia-smi](https://developer.nvidia.com/nvidia-system-management-interface) tool.

```markdown
root@kali:~# nvidia-smi
+-----------------------------------------------------------------------------+
| NVIDIA-SMI 375.26                 Driver Version: 375.26                    |
|-------------------------------+----------------------+----------------------+
| GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
| Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
|===============================+======================+======================|
|   0  GeForce GTX 970     Off  | 0000:01:00.0      On |                  N/A |
| 36%   46C    P0    47W / 325W |    200MiB /  4036MiB |      0%      Default |
+-------------------------------+----------------------+----------------------+
+-----------------------------------------------------------------------------+
| Processes:                                                       GPU Memory |
|  GPU       PID  Type  Process name                               Usage      |
|=============================================================================|
|    0       692    G   /usr/lib/xorg/Xorg                             198MiB |
+-----------------------------------------------------------------------------+
```

With the output displaying our driver and GPU correctly, we can now dive into benchmarking. Before we get too far ahead, let's double check to make sure hashcat and CUDA are working together.

```html
root@kali:~# hashcat -I
OpenCL Info:
Platform ID #1
  Vendor  : NVIDIA Corporation
  Name    : NVIDIA CUDA
  Version : OpenCL 1.2 CUDA 8.0.0
  Device ID #1
    Type           : GPU
    Vendor ID      : 32
    Vendor         : NVIDIA Corporation
    Name           : GeForce GTX 970
    Version        : OpenCL 1.2 CUDA
    Processor(s)   : 13
    Clock          : 1240
    Memory         : 1009/4036 MB allocatable
    OpenCL Version : OpenCL C 1.2
    Driver Version : 375.26
```

It appears everything is working, let's go ahead and run a benchmark test.

#### Benchmarking

```html
root@kali:~# hashcat -b
OpenCL Platform #1: NVIDIA Corporation
======================================
* Device #1: Geforce GTX 970, 1009/4095 MB allocatable, 13MCU

Hashtype: MD5
Speed.Dev.#1.....: 10443.1 MH/s
Hashtype: SHA1
Speed.Dev.#1.....: 3349.8 MH/s
Hashtype: SHA256
Speed.Dev.#1.....: 1321.8 MH/s
```

There are a multitude of configurations to improve cracking speed, not mentioned in this guide. However, we encourage you to take a look at the [hashcat documentation](https://hashcat.net/wiki/) for your specific cases.

#### Troubleshooting

In the event setup isn't going as planned, we'll install [clinfo](https://packages.debian.org/jessie/clinfo) for detailed troubleshooting information.

```markdown
apt install -y clinfo
```

###### OpenCL Loaders

It may be necessary to check for additional packages that may be conflicting with our setup. Let's first check to see what **OpenCL Loader** we have installed. The NVIDIA OpenCL Loader and the generic OpenCL Loader will both work for our system.

```markdown
root@kali:~# dpkg -l |grep -i icd
ii  nvidia-egl-icd:amd64                      375.26-2                             amd64        NVIDIA EGL installable client driver (ICD)
ii  nvidia-opencl-icd:amd64                   375.26-2                             amd64        NVIDIA OpenCL installable client driver (ICD)
ii  nvidia-vulkan-icd:amd64                   375.26-2                             amd64        NVIDIA Vulkan installable client driver (ICD)
ii  ocl-icd-libopencl1:amd64                  2.2.11-1                             amd64        Generic OpenCL ICD Loader
```

If **mesa-opencl-icd** is installed run:

```markdown
apt remove mesa-opencl-icd
```

Since we have determined that we have a compatible ICD loader installed, we can easily determine which loader is currently being used.

```markdown
root@kali:~# clinfo | grep -i "icd loader"
ICD loader properties
  ICD loader Name                                 OpenCL ICD Loader
  ICD loader Vendor                               OCL Icd free software
  ICD loader Version                              2.2.11
  ICD loader Profile                              OpenCL 2.1
```

As expected, our setup is using the open source loader that was installed earlier. Now, let's get some detailed information about the system.

###### Querying GPU Information

We'll use nvidia-smi once again, but with a much more verbose output.

```html
root@kali:~# nvidia-smi -i 0 -q
Driver Version                      : 375.26
Attached GPUs                       : 1
GPU 0000:01:00.0
    Product Name                    : GeForce GTX 970
    Product Brand                   : GeForce
    Display Mode                    : Enabled
    Display Active                  : Enabled
    Persistence Mode                : Disabled
    Accounting Mode                 : Disabled
    Accounting Mode Buffer Size     : 1920
Temperature
        GPU Current Temp            : 47 C
        GPU Shutdown Temp           : 96 C
        GPU Slowdown Temp           : 91 C
    Clocks
        Graphics                    : 1101 MHz
        SM                          : 1101 MHz
        Memory                      : 3523 MHz
        Video                       : 1012 MHz
    Processes
        Process ID                  : 692
            Type                    : G
            Name                    : /usr/lib/xorg/Xorg
            Used GPU Memory         : 198 MiB
```

It looks like our GPU is being recognized correctly, so let's use [glxinfo](https://dri.freedesktop.org/wiki/glxinfo/) to determine if 3D Rendering is enabled.

```markdown
root@kali:~# glxinfo | grep -i "direct rendering"
direct rendering: Yes
```

The combination of these tools should assist the troubleshooting process greatly. If you still experience issues, we recommend searching for similar setups and any nuances that may affect your specific system.
