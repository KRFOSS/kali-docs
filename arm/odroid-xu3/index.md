---
title: 오드로이드-XU3
description:
icon:
weight:
author: ["steev",]
번역: ["xenix4845"]
---

[오드로이드-XU3](https://www.hardkernel.com/main/products/prdt_info.php?g_code=g140448267127)는 옥타코어 개발 보드입니다. 4개의 A15 코어와 4개의 A7 코어, 그리고 4GB RAM을 갖추고 있어 오드로이드-XU3는 빠른 ARM 장치입니다. 칼리 리눅스는 외장 마이크로 SD 카드나 eMMC 모듈에 설치할 수 있습니다.

기본적으로 칼리 리눅스 오드로이드-XU3 이미지는 다른 대부분의 플랫폼과 유사하게 [**kali-linux-default** 메타패키지](/docs/general-use/metapackages/)를 포함합니다. 추가 도구를 설치하려면 [메타패키지 페이지](/docs/general-use/metapackages/)를 참조하세요.

<!--
## Kali on the ODROID-XU3 microSD card - User Instructions

If you're unfamiliar with the details of [downloading and validating a 칼리 리눅스 image](/docs/introduction/download-official-kali-linux-images/), or for [using that image to create a bootable device](/docs/usb/live-usb-install-with-windows/), it's strongly recommended that you refer to the more detailed procedures described in the specific articles on those subjects.

To install a pre-built image of the standard build of 칼리 리눅스 on your ODROID-XU3, follow these instructions:

1. Get a fast microSD card or eMMC module with at least 16GB capacity. Class 10 cards are highly recommended.
2. Download _and validate_ the `Kali ODROID-XU3` image from the [downloads](/get-kali/) area. The process for validating an image is described in more detail on [Downloading 칼리 리눅스](/docs/introduction/download-official-kali-linux-images/).
3. Use the **[dd](https://manpages.debian.org/testing/coreutils/dd.1.en.html)** utility to image this file to your microSD card (same process as [making a Kali USB](/docs/usb/live-usb-install-with-windows/).

In our example, we assume the storage device is located at `/dev/sdX`. Do _not_ simply copy these value, **change this to the correct drive path**.

{{% notice info %}}
This process will wipe out your microSD card. If you choose the wrong storage device, you may wipe out your computers hard disk or eMMC module.
{{% /notice %}}

```console
$ xzcat kali-linux-2025.1-odroid-xu3-armhf.img.xz | sudo dd of=/dev/sdX bs=4M status=progress
```

This process can take a while, depending on your PC, your microSD card's speed, and the size of the 칼리 리눅스 image.

Once the _dd_ operation is complete, boot up the ODROID-XU3 with the microSD card plugged in.

You should be able to [log in to Kali](/docs/introduction/default-credentials/).

## Kali on the ODROID-XU3 eMMC - User Instructions

If you want to install Kali on your ODROID-XU3's eMMC module, there are 2 different ways to do so.

If you have the [USB adapter for eMMC module](https://pine64.com/product/usb-adapter-for-emmc-module/?v=0446c16e2e66) then you can simply follow the same steps as you would for the microSD card.

If you do not have the USB adapter for eMMC module, you can use a bootable microSD card to write the Kali image to eMMC. The instructions are similar to the microSD card, and as with above, we need to make sure that we have the correct device. The easiest way to tell which device you want to use, is look in /dev at the `mmcblkX` devices. The device that has a `boot0` and `boot1` is the eMMC. For example, if `/dev/mmcblk1boot0` exists it would mean that we want to use `/dev/mmcblk1` as our device. One important difference is that we **do** need to include the number of the device, unlike above when using `sdX`.

{{% notice info %}}
This process will wipe out your eMMC module. If you choose the wrong storage device, you may wipe out your microSD card.
{{% /notice %}}

```console
$ xzcat kali-linux-2025.1-odroid-xu3-armhf.img.xz | sudo dd of=/dev/mmcblk1 bs=4M status=progress
```

This process can take a while, depending on your PC, your eMMC's speed, and the size of the 칼리 리눅스 image.

Once the _dd_ operation is complete, boot up the ODROID-XU3 with the eMMC plugged in.

You should be able to [log in to Kali](/docs/introduction/default-credentials/).

## Kali on ODROID-XU3 - Image Customization

If you want to customize the Kali ODROID-XU3 image, including changes to the [packages](/docs/general-use/metapackages/) being installed, changing the [desktop environment](/docs/general-use/switching-desktop-environments/), increasing or decreasing the image file size or generally being adventurous, check out the [Kali-ARM Build-Scripts](https://gitlab.com/kalilinux/build-scripts/kali-arm) repository on GitLab, and follow the _README.md_ file's instructions. The script to use is `odroid-xu3.sh`.
-->

## 오드로이드-XU3/XU4용 칼리 - 빌드 스크립트 지침

칼리는 사전 빌드된 이미지를 다운로드용으로 제공하지 않지만, GitLab에서 [칼리-ARM 빌드 스크립트](https://gitlab.com/kalilinux/build-scripts/kali-arm) 저장소를 클론하여 _README.md_ 파일의 지침에 따라 직접 이미지를 생성할 수 있습니다. 사용할 스크립트는 `odroid-xu3.sh`입니다.

빌드 스크립트 실행이 완료되면 스크립트를 실행한 디렉토리의 `images` 폴더에 "img.xz" 파일이 생성됩니다. 이 시점부터는 사전 빌드된 이미지를 다운로드한 경우와 동일한 방식으로 진행하면 됩니다.

이러한 이미지를 생성하는 가장 쉬운 방법은 **기존 칼리 리눅스 환경 내에서** 작업하는 것입니다.

## 오드로이드-XU3/XU4용 칼리 - 사용자 지침

오드로이드-XU3/XU4에 칼리를 설치하려면 다음 지침을 따르세요:

1. 최소 16GB 용량의 빠른 마이크로 SD 카드를 준비하세요. Class 10 카드를 강력히 권장합니다.
2. **[dd](https://manpages.debian.org/testing/coreutils/dd.1.en.html)** 유틸리티를 사용하여 이 파일을 마이크로 SD 카드에 이미징하세요 ([칼리 USB 만들기](/docs/usb/live-usb-install-with-windows/)와 동일한 과정).

아래 예시에서는 저장 장치가 `/dev/sdX`에 위치한다고 가정합니다. 이 값을 단순히 복사하지 마시고, **올바른 드라이브 경로로 변경하세요**.

{{% notice info %}}
이 과정은 마이크로 SD 카드의 모든 데이터를 지웁니다. 잘못된 저장 장치를 선택하면 컴퓨터의 하드 디스크가 지워질 수 있습니다.
{{% /notice %}}

```console
$ xzcat images/kali-linux-2025.3-odroid-xu3-armhf.img.xz | sudo dd of=/dev/sdX bs=4M status=progress
```

이 과정은 PC, 마이크로 SD 카드 속도 및 칼리 리눅스 이미지 크기에 따라 시간이 소요될 수 있습니다.

_dd_ 작업이 완료되면, 마이크로 SD 카드를 꽂은 상태로 오드로이드-C2를 부팅하세요.

동일한 이미지 파일은 eMMC 또는 마이크로 SD 카드 모두에 사용할 수 있습니다.

[칼리에 로그인](/docs/introduction/default-credentials/)할 수 있어야 합니다.
