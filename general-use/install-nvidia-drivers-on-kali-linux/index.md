---
title: Install NVIDIA GPU Drivers
description:
icon:
weight: 50
author: ["g0tmi1k",]
---

Do not attempt this in a VM. It is [possible in theory](https://mathiashueber.com/windows-virtual-machine-gpu-passthrough-ubuntu/), however this likely will not work and we do not recommend that users attempt this.

This document explains how to install NVIDIA GPU drivers and CUDA support, allowing integration with popular penetration testing tools.

**This guide is also for a dedicated card (desktops users), not Optimus (notebook users). We do not have the hardware in order to write up the guide. So we are looking for [community contribution](/docs/community/contribute/) to help out. If you have the hardware, and expertise, please [edit this guide](https://gitlab.com/kalilinux/documentation/kali-docs/edit/master/general-use/install-nvidia-drivers-on-kali-linux/index.md)!**

## Prerequisites

First, you'll need to ensure that your card supports [CUDA](https://developer.nvidia.com/cuda-gpus).

{{% notice info %}}
GPUs with a <a href="https://developer.nvidia.com/cuda-gpus">CUDA compute capability</a> > 5.0 are recommended, but GPUs with less will still work.
{{% /notice %}}

Afterwards, make sure you have [`contrib` & `non-free` components are enabled in your network Repositories](/docs/general-use/kali-linux-sources-list-repositories/) and that your system is [fully up-to-date](/docs/general-use/updating-kali/).

```console
kali@kali:~$ sudo apt update
kali@kali:~$
kali@kali:~$ sudo apt -y full-upgrade -y
kali@kali:~$
kali@kali:~$ [ -f /var/run/reboot-required ] && sudo reboot -f
kali@kali:~$
```

Let's determine the exact GPU installed, and check the kernel modules it's using:

```console
kali@kali:~$ lspci | grep -i vga
07:00.0 VGA compatible controller: NVIDIA Corporation GP106 [GeForce GTX 1060 6GB] (rev a1)
kali@kali:~$
kali@kali:~$ lspci -s 07:00.0 -v
07:00.0 VGA compatible controller: NVIDIA Corporation GP106 [GeForce GTX 1060 6GB] (rev a1) (prog-if 00 [VGA controller])
        Subsystem: Gigabyte Technology Co., Ltd GP106 [GeForce GTX 1060 6GB]
        Flags: bus master, fast devsel, latency 0, IRQ 100
        Memory at f6000000 (32-bit, non-prefetchable) [size=16M]
        Memory at e0000000 (64-bit, prefetchable) [size=256M]
        Memory at f0000000 (64-bit, prefetchable) [size=32M]
        I/O ports at e000 [size=128]
        Expansion ROM at 000c0000 [disabled] [size=128K]
        Capabilities: <access denied>
        Kernel driver in use: nouveau
        Kernel modules: nouveau

kali@kali:~$
```

Notice how `Kernel driver in use` & `Kernel modules` are using **nouveau**? This is the open source driver for nVidia. This guide covers installing the close source, from NVIDIA.

{{% notice info %}}
There is a package called `nvidia-detect` which will fail to detect the driver due to Kali being a [rolling distribution](/docs/general-use/kali-branches/) and requires a stable release.
{{% /notice %}}

## Installation

Once the system has rebooted from doing an OS upgrade, we will proceed to install the **Drivers**, and the **CUDA toolkit** _(allowing for tool to take advantage of the GPU)_.

During installation of the drivers the system created new kernel modules, so a reboot is required:

```console
kali@kali:~$ sudo apt install -y nvidia-driver nvidia-cuda-toolkit

┌─────────────────────────────────┤ Configuring xserver-xorg-video-nvidia ├─────────────────────────────────┐
│                                                                                                           │
│ Conflicting nouveau kernel module loaded                                                                  │
│                                                                                                           │
│ The free nouveau kernel module is currently loaded and conflicts with the non-free nvidia kernel module.  │
│                                                                                                           │
│ The easiest way to fix this is to reboot the machine once the installation has finished.                  │
│                                                                                                           │
│                                                  <Ok>                                                     │
│                                                                                                           │
└───────────────────────────────────────────────────────────────────────────────────────────────────────────┘

kali@kali:~$
kali@kali:~$ sudo reboot -f
kali@kali:~$
```

## DPI/PPI

Upon Kali starting back up, certain things may appear different than what is expected.

- If certain things are **smaller**, this could because of [HiDPI](/docs/general-use/hidpi/).
- However, if certain things are **larger**, this could because the [DPI](/docs/general-use/fixing-dpi/) is incorrect.

## Verify Driver Installation

Now that our system should be ready to go, we need to verify the drivers have been loaded correctly. We can quickly verify this by running the [nvidia-smi](https://developer.nvidia.com/nvidia-system-management-interface) tool.

```console
kali@kali:~$ nvidia-smi
Tue Jan 28 11:37:47 2020
+-----------------------------------------------------------------------------+
| NVIDIA-SMI 430.64       Driver Version: 430.64       CUDA Version: 10.1     |
|-------------------------------+----------------------+----------------------+
| GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
| Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
|===============================+======================+======================|
|   0  GeForce GTX 106...  Off  | 00000000:07:00.0  On |                  N/A |
|  0%   50C    P8     7W / 120W |    116MiB /  6075MiB |      0%      Default |
+-------------------------------+----------------------+----------------------+

+-----------------------------------------------------------------------------+
| Processes:                                                       GPU Memory |
|  GPU       PID   Type   Process name                             Usage      |
|=============================================================================|
|    0       807      G   /usr/lib/xorg/Xorg                           112MiB |
|    0       979      G   xfwm4                                          2MiB |
+-----------------------------------------------------------------------------+
kali@kali:~$
kali@kali:~$ lspci | grep -i vga
07:00.0 VGA compatible controller: NVIDIA Corporation GP106 [GeForce GTX 1060 6GB] (rev a1)
kali@kali:~$
kali@kali:~$ lspci -s 07:00.0 -v
...
        Kernel driver in use: nvidia
        Kernel modules: nvidia

kali@kali:~$
```

You can see our hardware has been detected we are using **nvidia** rather than **nouveau** drive now.

## Hashcat

With the output displaying our driver and GPU correctly, we can now dive into benchmarking (using the CUDA toolkit). Before we get too far ahead, let's double check to make sure [hashcat](https://tools.kali.org/password-attacks/hashcat) and CUDA are working together.

```console
kali@kali:~$ sudo apt install -y hashcat
kali@kali:~$
kali@kali:~$ hashcat -I
hashcat (v6.0.0) starting...

CUDA Info:
==========

CUDA.Version.: 10.2

Backend Device ID #1 (Alias: #2)
  Name...........: GeForce GTX 1060 6GB
  Processor(s)...: 10
  Clock..........: 1771
  Memory.Total...: 6075 MB
  Memory.Free....: 5908 MB

OpenCL Info:
============

OpenCL Platform ID #1
  Vendor..: NVIDIA Corporation
  Name....: NVIDIA CUDA
  Version.: OpenCL 1.2 CUDA 10.2.185

  Backend Device ID #2 (Alias: #1)
    Type...........: GPU
    Vendor.ID......: 32
    Vendor.........: NVIDIA Corporation
    Name...........: GeForce GTX 1060 6GB
    Version........: OpenCL 1.2 CUDA
    Processor(s)...: 10
    Clock..........: 1771
    Memory.Total...: 6075 MB (limited to 1518 MB allocatable in one block)
    Memory.Free....: 5888 MB
    OpenCL.Version.: OpenCL C 1.2
    Driver.Version.: 440.100

kali@kali:~$
```

It appears everything is working, let's go ahead and run hashcat's inbuilt benchmark test.

#### Benchmarking

```console
kali@kali:~$ hashcat -b | uniq
hashcat (v6.0.0) starting in benchmark mode...

Benchmarking uses hand-optimized kernel code by default.
You can use it in your cracking session by setting the -O option.
Note: Using optimized kernel code limits the maximum supported password length.
To disable the optimized kernel code in benchmark mode, use the -w option.

* Device #1: WARNING! Kernel exec timeout is not disabled.
             This may cause "CL_OUT_OF_RESOURCES" or related errors.
             To disable the timeout, see: https://hashcat.net/q/timeoutpatch
* Device #2: WARNING! Kernel exec timeout is not disabled.
             This may cause "CL_OUT_OF_RESOURCES" or related errors.
             To disable the timeout, see: https://hashcat.net/q/timeoutpatch
CUDA API (CUDA 10.2)
====================
* Device #1: GeForce GTX 1060 6GB, 5908/6075 MB, 10MCU

OpenCL API (OpenCL 1.2 CUDA 10.2.185) - Platform #1 [NVIDIA Corporation]
========================================================================
* Device #2: GeForce GTX 1060 6GB, skipped

Benchmark relevant options:
===========================
* --optimized-kernel-enable

Hashmode: 0 - MD5
Speed.#1.........: 14350.4 MH/s (46.67ms) @ Accel:64 Loops:1024 Thr:1024 Vec:8

Hashmode: 100 - SHA1
Speed.#1.........:  4800.5 MH/s (69.83ms) @ Accel:32 Loops:1024 Thr:1024 Vec:1
...
Started: Tue Jul 21 17:12:39 2020
Stopped: Tue Jul 21 17:16:10 2020
kali@kali:~$
```

There are a multitude of configurations to improve cracking speed, not mentioned in this guide. However, we encourage you to take a look at the [hashcat documentation](https://hashcat.net/wiki/) for your specific cases.

## Troubleshooting

In the event setup isn't going as planned, we'll install [clinfo](https://packages.debian.org/testing/clinfo) for detailed troubleshooting information.

```console
kali@kali:~$ sudo apt install -y clinfo
kali@kali:~$
kali@kali:~$ clinfo
Number of platforms                               1
  Platform Name                                   NVIDIA CUDA
  Platform Vendor                                 NVIDIA Corporation
  Platform Version                                OpenCL 1.2 CUDA 10.1.120
  Platform Profile                                FULL_PROFILE
  Platform Extensions                             cl_khr_global_int32_base_atomics cl_khr_global_int32_extended_atomics cl_khr_local_int32_base_atomics cl_khr_local_int32_extended_atomics cl_khr_fp64 cl_khr_byte_addressable_store cl_khr_icd cl_khr_gl_sharing cl_nv_compiler_options cl_nv_device_attribute_query cl_nv_pragma_unroll cl_nv_copy_opts cl_nv_create_buffer
  Platform Extensions function suffix             NV

  Platform Name                                   NVIDIA CUDA
...
kali@kali:~$
kali@kali:~$ clinfo | wc -l
116
kali@kali:~$
```

#### OpenCL Loaders

It may be necessary to check for additional packages that may be conflicting with our setup. Let's first check to see what **OpenCL Loader** we have installed. The NVIDIA OpenCL Loader and the generic OpenCL Loader will both work for our system.

```console
kali@kali:~$ dpkg -l |  grep -i icd
ii  nvidia-egl-icd:amd64                 430.64-5                        amd64        NVIDIA EGL installable client driver (ICD)
ii  nvidia-opencl-icd:amd64              430.64-5                        amd64        NVIDIA OpenCL installable client driver (ICD)
ii  nvidia-vulkan-icd:amd64              430.64-5                        amd64        NVIDIA Vulkan installable client driver (ICD)
ii  ocl-icd-libopencl1:amd64             2.2.12-2                        amd64        Generic OpenCL ICD Loader
ii  ocl-icd-opencl-dev:amd64             2.2.12-2                        amd64        OpenCL development files
kali@kali:~$
```

If **mesa-opencl-icd** is installed, we should remove it:

```console
kali@kali:~$ dpkg -l |  grep -i mesa-opencl-icd
ii  mesa-opencl-icd:amd64                19.3.2-1                        amd64        free implementation of the OpenCL API -- ICD runtime
kali@kali:~$
kali@kali:~$ sudo apt remove mesa-opencl-icd
kali@kali:~$
```

Since we have determined that we have a compatible ICD loader installed, we can easily determine which loader is currently being used.

```console
kali@kali:~$ clinfo | grep -i "icd loader"
ICD loader properties
  ICD loader Name                                 OpenCL ICD Loader
  ICD loader Vendor                               OCL Icd free software
  ICD loader Version                              2.2.12
  ICD loader Profile                              OpenCL 2.2
kali@kali:~$
```

As expected, our setup is using the open source loader that was installed earlier. Now, let's get some detailed information about the system.

#### Querying GPU Information

We'll use [nvidia-smi](https://packages.debian.org/testing/nvidia-smi) once again, but with a much more verbose output.

```console
kali@kali:~$ nvidia-smi -i 0 -q

==============NVSMI LOG==============

Timestamp                           : Fri Feb 14 13:26:21 2020
Driver Version                      : 430.64
CUDA Version                        : 10.1

Attached GPUs                       : 1
GPU 00000000:07:00.0
    Product Name                    : GeForce GTX 1060 6GB
    Product Brand                   : GeForce
    Display Mode                    : Enabled
    Display Active                  : Enabled
    Persistence Mode                : Disabled
    Accounting Mode                 : Disabled
    Accounting Mode Buffer Size     : 4000
...
    Temperature
        GPU Current Temp            : 49 C
        GPU Shutdown Temp           : 102 C
        GPU Slowdown Temp           : 99 C
...
    Clocks
        Graphics                    : 139 MHz
        SM                          : 139 MHz
        Memory                      : 405 MHz
        Video                       : 544 MHz
...
    Processes
        Process ID                  : 815
            Type                    : G
            Name                    : /usr/lib/xorg/Xorg
            Used GPU Memory         : 132 MiB
        Process ID                  : 994
            Type                    : G
            Name                    : xfwm4
            Used GPU Memory         : 2 MiB
kali@kali:~$
```

It looks like our GPU is being recognized correctly, so let's use [glxinfo](https://dri.freedesktop.org/wiki/glxinfo/) to determine if 3D Rendering is enabled.

```console
kali@kali:~$ sudo apt install -y mesa-utils
kali@kali:~$
kali@kali:~$ glxinfo | grep -i "direct rendering"
direct rendering: Yes
kali@kali:~$
```

The combination of these tools should assist the troubleshooting process greatly. If you still experience issues, we recommend searching for similar setups and any nuances that may affect your specific system.
