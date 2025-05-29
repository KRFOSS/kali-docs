---
title: NVIDIA GPU 드라이버 설치
description:
icon:
weight: 50
author: ["g0tmi1k",]
번역: ["xenix4845"]
---

{{% notice info %}}
라이브 부팅은 현재 지원되지 않아요. 다음 문서는 VM이든 베어메탈이든 설치된 버전의 칼리 리눅스를 가정하고 있어요.
{{% /notice %}}

이 문서는 NVIDIA GPU 드라이버와 CUDA 지원을 설치하는 방법을 설명하며, 이를 통해 많이 사용되는 침투 테스트 도구와의 통합을 가능하게 해요. 우리는 NVIDIA용 오픈소스 드라이버인 **nouveau**를 사용하지 **않고**, 대신 NVIDIA에서 제공하는 비공개 소스를 설치할 거예요.

이 가이드는 **[독립 그래픽 카드(데스크톱 사용자)](#dedicated-cards)**와 **[옵티머스(랩톱과 노트북 사용자)](#optimus-cards)**를 다룰 거예요.

가상 머신에서 이 작업을 시도하지 **않는** 것을 권장해요. [가능하긴](https://mathiashueber.com/windows-virtual-machine-gpu-passthrough-ubuntu/) 하지만 간단하지 않으며, Linux에 대한 깊은 이해가 있는 경우에만 수행해야 해요. 이 가이드에서는 모든 환경과 설정에 대해 다룰 수 없는 항목이 너무 많기 때문에 다루지 않아요.

## 선행 조건

먼저, NVIDIA 카드가 [CUDA](https://developer.nvidia.com/cuda-gpus)를 지원하는지 확인해야 해요.

{{% notice info %}}
<a href="https://developer.nvidia.com/cuda-gpus">CUDA 컴퓨팅 기능</a> > 5.0을 가진 GPU가 권장되지만, 더 낮은 버전의 GPU도 작동해요.
{{% /notice %}}

- - -

그 다음, 네트워크 저장소에서 [`contrib` 및 `non-free*` 구성 요소가 활성화](/docs/general-use/kali-linux-sources-list-repositories/)되어 있고 시스템이 [완전히 최신 상태](/docs/general-use/updating-kali/)인지 확인하세요.
또한 시스템에 적절한 커널 헤더가 설치되어 있는지 확인하세요:

```console
kali@kali:~$ grep "contrib non-free" /etc/apt/sources.list
deb http://http.kali.org/kali kali-rolling main contrib non-free non-free-firmware
kali@kali:~$
kali@kali:~$ sudo apt update
[...]
kali@kali:~$
kali@kali:~$ sudo apt -y full-upgrade -y
[...]
kali@kali:~$
kali@kali:~$ sudo apt install linux-headers-$(uname -r) -y
kali@kali:~$
kali@kali:~$ [ -f /var/run/reboot-required ] && sudo reboot -f
```

## 독립 그래픽 카드

설치된 정확한 GPU를 확인하고 사용 중인 커널 모듈을 확인해 보세요. `lspci` 명령에는 고유한 PCI 버스 주소가 포함되어 있다는 점에 유의하세요. 반드시 올바른 주소를 포함시켜야 해요 `lspci -s XX.XX.X -v`:

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

## 옵티머스 카드

옵티머스(랩톱 및 노트북)의 경우, 기본 카드로 NVIDIA가 표시되지 않아요. NVIDIA가 전혀 나열되지 않을 수도 있어요. 다음과 같이 기본 카드가 무엇인지 확인할 수 있어요:

```console
kali@kali:~$ lspci | grep -i vga
00:02.0 VGA compatible controller: Intel Corporation HD Graphics 620 (rev 02)
kali@kali:~$
```

- - -

NVIDIA 카드를 감지하려면 `nvidia-detect`를 설치해야 해요:

```console
kali@kali:~$ sudo apt install -y nvidia-detect
[...]
kali@kali:~$ nvidia-detect
Detected NVIDIA GPUs:
01:00.0 3D controller [0302]: NVIDIA Corporation GM108M [GeForce 940MX] [10de:134d] (rev a2)

Checking card:  NVIDIA Corporation GM108M [GeForce 940MX] (rev a2)
Uh oh. Failed to identify your Debian suite.

kali@kali:~$
kali@kali:~$ lspci -s 01:00.0 -v
01:00.0 3D controller: NVIDIA Corporation GM108M [GeForce 940MX] (rev a2)
        Subsystem: Lenovo GM108M [GeForce 940MX]
        Flags: bus master, fast devsel, latency 0, IRQ 132, IOMMU group 10
        Memory at 93000000 (32-bit, non-prefetchable) [size=16M]
        Memory at 80000000 (64-bit, prefetchable) [size=256M]
        Memory at 90000000 (64-bit, prefetchable) [size=32M]
        I/O ports at 5000 [size=128]
        Capabilities: <access denied>
        Kernel driver in use: nouveau
        Kernel modules: nouveau
kali@kali:~$
```

{{% notice info %}}
`nvidia-detect` 패키지는 Kali가 [롤링 배포판](/docs/general-use/kali-branches/)이기 때문에 일부 지점에서 실패할 수 있어요. 이 패키지는 안정적인 릴리스를 필요로 해요.
{{% /notice %}}

## 설치

`lspci`의 `Kernel driver in use` 및 `Kernel modules`가 어떻게 **nouveau**를 사용하고 있는지 보세요. 이는 NVIDIA 카드용 오픈소스 드라이버를 의미해요. 이제 비공개 소스 **드라이버**와 **CUDA 툴킷**(GPU를 활용할 수 있는 도구)으로 전환할 거예요.

{{% notice info %}}
빠른 강의: 커널 모듈, 의존성 및 kbuild 이해하기

`nvidia-driver` [메타패키지](https://wiki.debian.org/metapackage)를 설치하기 전에, 이것이 무엇인지 알아보세요. 이는 드라이버(즉, 커널 모듈)에 필요한 모든 파일, 바이너리 및 라이브러리를 함께 설치하는 패키지 모음이에요. 의존성 중 하나 이상(`nvidia-kernel-dkms`)이 드라이버를 외부 커널 모듈로 빌드하므로(즉, 커널에 내장되지 않은 외부 모듈), [빌드가 성공하려면 커널 헤더가 설치되어 있어야 해요](https://www.kernel.org/doc/html/latest/kbuild/modules.html#how-to-build-external-modules). 이 빌드 작업을 수행하는 시스템은 [kbuild](https://www.kernel.org/doc/html/latest/kbuild/modules.html#how-to-build-external-modules)로 알려져 있어요.
{{% /notice %}}

말한 대로, 커널 헤더와 kbuild 인프라를 설치해 보세요:

```console
kali@kali:~$ sudo apt install -y linux-headers-amd64
kali@kali:~$
```

드라이버를 설치하는 동안 시스템이 새로운 커널 모듈을 생성했으므로, 이후에 시스템을 재부팅하는 것이 좋아요:

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

## Dots Per Inch (DPI) 및 Pixels Per Inch (PPI)

Kali가 다시 시작된 후, 특정 요소들이 예상과 다르게 보일 수 있어요:

- 특정 요소가 **작게** 보이면, 이는 [HiDPI](/docs/general-use/hidpi/) 때문일 수 있어요
- 그러나 특정 요소가 **크게** 보이면, 이는 [DPI](/docs/general-use/fixing-dpi/)가 올바르지 않기 때문일 수 있어요

## 드라이버 설치 확인

이제 시스템이 준비되었을 테니, 드라이버가 올바르게 로드되었는지 확인해 봅시다. [nvidia-smi](https://developer.nvidia.com/nvidia-system-management-interface) 도구와 `lspci`를 다시 실행하여 빠르게 확인할 수 있어요:

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
[...]
        Kernel driver in use: nvidia
        Kernel modules: nvidia

kali@kali:~$
```

우리의 하드웨어가 감지되었고 이제 **nouveau** 드라이버가 아닌 **nvidia** 드라이버를 사용하고 있는 것을 볼 수 있어요!

## Hashcat

드라이버와 GPU가 올바르게 표시되었으니, 이제 (CUDA 툴킷을 사용한) 벤치마킹을 시작해 볼 수 있어요. 그러나 먼저, [hashcat](/tools/hashcat/)과 CUDA가 함께 작동하는지 확인해 보세요:

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

모든 것이 작동하는 것 같으니, hashcat의 내장 벤치마크 테스트를 실행해 보세요.

#### 벤치마킹

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
[...]
Started: Tue Jul 21 17:12:39 2020
Stopped: Tue Jul 21 17:16:10 2020
kali@kali:~$
```

해시 크래킹 속도를 향상시키는 다양한 설정이 있지만, 이 가이드에서는 다루지 않아요. 그러나 특정 사례에 대해서는 [hashcat 문서](https://hashcat.net/wiki/)를 살펴보는 것을 권장해요.

## 문제 해결

설정이 계획대로 진행되지 않는 경우, 상세한 문제 해결 정보를 위해 [clinfo](https://packages.debian.org/testing/clinfo)를 설치해 보세요:

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
[...]
kali@kali:~$
kali@kali:~$ clinfo | wc -l
116
kali@kali:~$
```

#### OpenCL 로더

설정과 충돌하는 추가 패키지가 있는지 확인해야 할 수 있어요. 먼저 설치된 **OpenCL 로더**가 무엇인지 확인해 보세요. NVIDIA OpenCL 로더와 일반 OpenCL 로더 모두 우리 시스템에서 작동해요:

```console
kali@kali:~$ dpkg -l |  grep -i icd
ii  nvidia-egl-icd:amd64                 430.64-5                        amd64        NVIDIA EGL installable client driver (ICD)
ii  nvidia-opencl-icd:amd64              430.64-5                        amd64        NVIDIA OpenCL installable client driver (ICD)
ii  nvidia-vulkan-icd:amd64              430.64-5                        amd64        NVIDIA Vulkan installable client driver (ICD)
ii  ocl-icd-libopencl1:amd64             2.2.12-2                        amd64        Generic OpenCL ICD Loader
ii  ocl-icd-opencl-dev:amd64             2.2.12-2                        amd64        OpenCL development files
kali@kali:~$
```

**mesa-opencl-icd**가 설치되어 있다면, 제거해야 해요:

```console
kali@kali:~$ dpkg -l |  grep -i mesa-opencl-icd
ii  mesa-opencl-icd:amd64                19.3.2-1                        amd64        free implementation of the OpenCL API -- ICD runtime
kali@kali:~$
kali@kali:~$ sudo apt remove mesa-opencl-icd
kali@kali:~$
```

호환되는 ICD 로더가 설치되어 있다는 것을 확인했으니, 현재 어떤 로더가 사용되고 있는지 쉽게 확인할 수 있어요:

```console
kali@kali:~$ clinfo | grep -i "icd loader"
ICD loader properties
  ICD loader Name                                 OpenCL ICD Loader
  ICD loader Vendor                               OCL Icd free software
  ICD loader Version                              2.2.12
  ICD loader Profile                              OpenCL 2.2
kali@kali:~$
```

예상대로 우리 설정은 이전에 설치한 오픈소스 로더를 사용하고 있어요. 이제 시스템에 대한 자세한 정보를 얻어 볼게요.

#### GPU 정보 쿼리하기

[nvidia-smi](https://packages.debian.org/testing/nvidia-smi)를 다시 사용하되, 더 자세한 출력으로 사용해 볼게요:

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
[...]
    Temperature
        GPU Current Temp            : 49 C
        GPU Shutdown Temp           : 102 C
        GPU Slowdown Temp           : 99 C
[...]
    Clocks
        Graphics                    : 139 MHz
        SM                          : 139 MHz
        Memory                      : 405 MHz
        Video                       : 544 MHz
[...]
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

GPU가 올바르게 인식되고 있는 것 같으니, [glxinfo](https://dri.freedesktop.org/wiki/glxinfo/)를 사용하여 3D 렌더링이 활성화되어 있는지 확인해 보세요:

```console
kali@kali:~$ sudo apt install -y mesa-utils
kali@kali:~$
kali@kali:~$ glxinfo | grep -i "direct rendering"
direct rendering: Yes
kali@kali:~$
```

이러한 도구들의 조합은 문제 해결 과정을 크게 도울 거예요. 여전히 문제가 발생한다면, 비슷한 설정과 특정 시스템에 영향을 미칠 수 있는 특별한 사항들을 검색해 보는 것을 권장해요.

{{% notice info %}}
다음은 사용자가 생성한 내용이에요. 일반적으로 Kali 팀은 비 apt 도구 및 다운로드 사용을 권장하지 않아요. 이는 비 apt 드라이버가 필요한 경우이지만, 이전 문서가 작동하지 않을 때만 수행해야 해요.
{{% /notice %}}

#### Hashcat이 GPU를 감지하지 못하는 문제

위 단계를 따랐는데도 `Hashcat`이 GPU를 감지하지 못한다면 다음을 수행하세요:

1. https://www.nvidia.com/Download/index.aspx?lang=en-us 에 가서 설치할 적절한 GPU 드라이버를 선택하세요.
2. `sudo su -`를 실행하세요.
3. `init 3`을 실행하세요(Linux 데스크톱을 비활성화하고 텍스트 인터페이스로 전환).
4. 이미 `apt`, `nala` 등과 같은 패키지 관리자를 사용하여 Nvidia 드라이버를 설치한 경우 먼저 제거해야 해요. `sudo apt remove nvidia*`를 수행하면 이전에 설치된 모든 Nvidia 드라이버가 제거돼요. 그렇지 않으면 다음 단계를 실행할 때 경고가 표시되고 설치가 중단돼요.
5. `sudo ./Nvidia-<your version>.run`으로 드라이버 파일을 설치하세요. 설치 과정을 따르고 적절한 옵션을 선택하세요.
6. 설치가 성공하면 `sudo reboot`를 입력하여 재부팅하세요.

이제 `hashcat -I`를 실행하고 모든 것이 잘 되면 `Hashcat`이 이제 GPU를 감지하고 출력은 다음과 비슷할 거예요:

```sh
kali@kali:~$ hashcat -I

OpenCL Info:
============

OpenCL Platform ID #1
  Vendor..: NVIDIA Corporation
  Name....: NVIDIA CUDA
  Version.: OpenCL 3.0 CUDA 12.4.131

  Backend Device ID #1
    Type...........: GPU
    Vendor.ID......: 32
    Vendor.........: NVIDIA Corporation
    Name...........: NVIDIA GeForce GTX 1650
    Version........: OpenCL 3.0 CUDA
    Processor(s)...: 14
[...]
```

아직 끝나지 않았어요. 이 방법을 따르고 나면 랩톱 디스플레이가 어두워지고 외부 모니터 디스플레이만 보일 수 있어요. 이는 새로 업데이트된 드라이버로 인해 시스템이 주 그래픽 드라이버로 `전용 GPU`, 즉 `Nvidia`를 사용하기 때문에 예상된 결과예요. Discord의 `hackterr` 덕분에 이 문제를 해결하기 위해 `랩톱 디스플레이`의 랩톱 디스플레이 카드를 `Nvidia` 또는 `통합 AMD` GPU로 설정하고 외부 디스플레이에는 `Nvidia`를 유지할 수 있어요. 저는 후자를 선택했어요.
- `/etc/X11/xorg.conf` 파일에 다음을 추가하세요. (이 작업에는 `sudo`가 필요해요)

```sh
Section "Device"
 Identifier "Device1"
 Driver "modesetting"
 VendorName "Advanced Micro Devices"
 BusID "PCI:5:00.0" # run lspci | grep -i vga # to see your bus ID for integrated GPU. In my case, it was 5:00.0
EndSection
```