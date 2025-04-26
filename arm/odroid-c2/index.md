---
title: ODROID-C2
description:
icon:
weight:
author: ["steev",]
---

[ODROID-C2](https://wiki.odroid.com/odroid-c2/odroid-c2)는 Amlogic S905, 쿼드 코어 Cortex™-A53(ARMv8 64비트) 프로세서와 트리플 코어 Mali-450 GPU 및 2GB DDR3(32비트/912Mhz) RAM을 탑재하고 있습니다. Kali Linux는 외장 microSD 카드 또는 eMMC 모듈에서 실행할 수 있습니다.

기본적으로 Kali Linux ODROID-C2 이미지는 다른 대부분의 플랫폼과 유사하게 [**kali-linux-default** 메타패키지](/docs/general-use/metapackages/)를 포함합니다. 추가 도구를 설치하려면 [메타패키지 페이지](/docs/general-use/metapackages/)를 참조하세요.

<!--
## Kali on ODROID-C2 microSD card - User Instructions

If you're unfamiliar with the details of [downloading and validating a Kali Linux image](/docs/introduction/download-official-kali-linux-images/), or for [using that image to create a bootable device](/docs/usb/live-usb-install-with-windows/), it's strongly recommended that you refer to the more detailed procedures described in the specific articles on those subjects.

To install a pre-built image of the standard build of Kali Linux on your ODROID-C2, follow these instructions:

1. Get a fast microSD card or eMMC module with at least 16GB capacity. Class 10 cards are highly recommended.
2. Download _and validate_ the `Kali ODROID-C2` image from the [downloads](/get-kali/) area. The process for validating an image is described in more detail on [Downloading Kali Linux](/docs/introduction/download-official-kali-linux-images/).
3. Use the **[dd](https://manpages.debian.org/testing/coreutils/dd.1.en.html)** utility to image this file to your microSD card (same process as [making a Kali USB](/docs/usb/live-usb-install-with-windows/).

In our example, we assume the storage device is located at `/dev/sdX`. Do _not_ simply copy these value, **change this to the correct drive path**.

{{% notice info %}}
This process will wipe out your microSD card. If you choose the wrong storage device, you may wipe out your computers hard disk or eMMC module.
{{% /notice %}}

```console
$ xzcat kali-linux-2025.1-odroid-c2-arm64.img.xz | sudo dd of=/dev/sdX bs=4M status=progress
```

This process can take a while, depending on your PC, your microSD card's speed, and the size of the Kali Linux image.

Once the _dd_ operation is complete, boot up the ODROID-C2 with the microSD card plugged in.

You should be able to [log in to Kali](/docs/introduction/default-credentials/).

## Kali on the ODROID-C2 eMMC - User Instructions

If you want to install Kali on your ODROID-C2's eMMC module, there are 2 different ways to do so.

If you have the [USB adapter for eMMC module](https://www.hardkernel.com/shop/usb3-0-emmc-module-writer/) then you can simply follow the same steps as you would for the microSD card.

{{% notice info %}}
The eMMC modules and USB adapter for eMMC module on the Pine64 devices and ODROID devices can be used interchangeably.
{{% /notice %}}

If you do not have the USB adapter for eMMC module, you can use a bootable microSD card to write the Kali image to eMMC. The instructions are similar to the microSD card, and as with above, we need to make sure that we have the correct device. The easiest way to tell which device you want to use, is look in /dev at the `mmcblkX` devices. The device that has a `boot0` and `boot1` is the eMMC. For example, if `/dev/mmcblk1boot0` exists it would mean that we want to use `/dev/mmcblk1` as our device. One important difference is that we **do** need to include the number of the device, unlike above when using `sdX`.

{{% notice info %}}
This process will wipe out your eMMC module. If you choose the wrong storage device, you may wipe out your computers hard disk or microSD card.
{{% /notice %}}

```console
$ xzcat kali-linux-2025.1-odroid-c2-arm64.img.xz | sudo dd of=/dev/mmcblk1 bs=4M status=progress
```

This process can take a while, depending on your PC, your eMMC's speed, and the size of the Kali Linux image.

Once the _dd_ operation is complete, boot up the ODROID-C2 with the eMMC plugged in.

You should be able to [log in to Kali](/docs/introduction/default-credentials/).

## Kali on the ODROID-C2 - Tips

The bootloader on the ODROID-C2 is u-boot, and in order to make changes to the kernel command line, the file to edit is `/etc/default/u-boot` and the option is `U_BOOT_PARAMETERS`. If you make any modifications to this file, you will want to then run `u-boot-update`.

USB on the ODROID-C2 will autosuspend if there is nothing plugged in to a USB port at boot time, so one possible change you might want to add is `usbcore.autosuspend=-1` if you want to plug in a USB device **after** the ODROID-C2 has booted.

If both a microSD card and an eMMC are plugged in, the ODROID-C2 will attempt to boot from the microSD card first.

## Kali on ODROID-C2 - Image Customization

If you want to customize the Kali ODROID-C2 image, including changes to the [packages](/docs/general-use/metapackages/) being installed, changing the [desktop environment](/docs/general-use/switching-desktop-environments/), increasing or decreasing the image file size or generally being adventurous, check out the [Kali-ARM Build-Scripts](https://gitlab.com/kalilinux/build-scripts/kali-arm) repository on GitLab, and follow the _README.md_ file's instructions. The script to use is `odroid-c2.sh`.
-->

## ODROID-C2용 Kali - 빌드 스크립트 지침

Kali는 사전 빌드된 이미지를 다운로드용으로 제공하지 않지만, GitLab에서 [Kali-ARM 빌드 스크립트](https://gitlab.com/kalilinux/build-scripts/kali-arm) 저장소를 클론하여 _README.md_ 파일의 지침에 따라 직접 이미지를 생성할 수 있습니다. 사용할 스크립트는 `odroid-c2.sh`입니다.

빌드 스크립트 실행이 완료되면 스크립트를 실행한 디렉토리의 `images` 폴더에 "img.xz" 파일이 생성됩니다. 이 시점부터는 사전 빌드된 이미지를 다운로드한 경우와 동일한 방식으로 진행하면 됩니다.

이러한 이미지를 생성하는 가장 쉬운 방법은 **기존 Kali Linux 환경 내에서** 작업하는 것입니다.

## ODROID-C2용 Kali - 사용자 지침

ODROID-C2에 Kali를 설치하려면 다음 지침을 따르세요:

1. 최소 16GB 용량의 빠른 microSD 카드를 준비하세요. Class 10 카드를 강력히 권장합니다.
2. **[dd](https://manpages.debian.org/testing/coreutils/dd.1.en.html)** 유틸리티를 사용하여 이 파일을 microSD 카드에 이미징하세요 ([Kali USB 만들기](/docs/usb/live-usb-install-with-windows/)와 동일한 과정).

아래 예시에서는 저장 장치가 `/dev/sdX`에 위치한다고 가정합니다. 이 값을 단순히 복사하지 마시고, **올바른 드라이브 경로로 변경하세요**.

{{% notice info %}}
이 과정은 microSD 카드의 모든 데이터를 지웁니다. 잘못된 저장 장치를 선택하면 컴퓨터의 하드 디스크가 지워질 수 있습니다.
{{% /notice %}}

```console
$ xzcat images/kali-linux-2025.1-odroid-c2-arm64.img.xz | sudo dd of=/dev/sdX bs=4M status=progress
```

이 과정은 PC, microSD 카드 속도 및 Kali Linux 이미지 크기에 따라 시간이 소요될 수 있습니다.

_dd_ 작업이 완료되면, microSD 카드를 꽂은 상태로 ODROID-C2를 부팅하세요.

동일한 이미지 파일은 eMMC 또는 microSD 카드 모두에 사용할 수 있습니다.

[Kali에 로그인](/docs/introduction/default-credentials/)할 수 있어야 합니다.

## ODROID-C2용 Kali - 팁

ODROID-C2의 부트로더는 u-boot이며, 커널 명령줄을 변경하려면 `/etc/default/u-boot` 파일을 편집하고 `U_BOOT_PARAMETERS` 옵션을 수정해야 합니다. 이 파일을 수정한 후에는 `u-boot-update` 명령을 실행해야 합니다.

ODROID-C2에서는 부팅 시 USB 포트에 아무것도 연결되어 있지 않으면 USB가 자동으로 절전 모드로 전환됩니다. 따라서 ODROID-C2가 부팅된 **후에** USB 장치를 연결하고 싶다면, `usbcore.autosuspend=-1` 옵션을 추가하는 것이 좋습니다.

microSD 카드와 eMMC가 모두 연결되어 있을 경우, ODROID-C2는 먼저 microSD 카드에서 부팅을 시도합니다.
