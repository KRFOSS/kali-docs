---
title: Banana Pro
description:
icon:
weight:
author: ["steev",]
---

[바나나 프로](http://www.lemaker.org/product-bananapro-specification.html)는 듀얼 코어 1GHz Cortex™-A7 프로세서와 Mali400MP2 GPU, 그리고 1GB DDR3 RAM을 탑재하고 있습니다. 칼리 리눅스는 외장 microSD 카드에서 구동할 수 있습니다.

기본적으로 칼리 리눅스 바나나 프로 이미지에는 다른 대부분의 플랫폼과 마찬가지로 [**kali-linux-default** 메타패키지](/docs/general-use/metapackages/)가 포함되어 있습니다. 추가 도구를 설치하고 싶으시다면 [메타패키지 페이지](/docs/general-use/metapackages/)를 참고하세요.

<!--
## Kali on Banana Pro - User Instructions

If you're unfamiliar with the details of [downloading and validating a Kali Linux image](/docs/introduction/download-official-kali-linux-images/), or for [using that image to create a bootable device](/docs/usb/live-usb-install-with-windows/), it's strongly recommended that you refer to the more detailed procedures described in the specific articles on those subjects.

To install a pre-built image of the standard build of Kali Linux on your Banana Pro, follow these instructions:

1. Get a fast microSD card with at least 16GB capacity. Class 10 cards are highly recommended.
2. Download _and validate_ the `Kali Banana Pro` image from the [downloads](/get-kali/) area. The process for validating an image is described in more detail on [Downloading Kali Linux](/docs/introduction/download-official-kali-linux-images/).
3. Use the **[dd](https://manpages.debian.org/testing/coreutils/dd.1.en.html)** utility to image this file to your microSD card (same process as [making a Kali USB](/docs/usb/live-usb-install-with-windows/).

In our example, we assume the storage device is located at `/dev/sdX`. Do _not_ simply copy these value, **change this to the correct drive path**.

{{% notice info %}}
This process will wipe out your microSD card. If you choose the wrong storage device, you may wipe out your computers hard disk.
{{% /notice %}}

```console
$ xzcat kali-linux-2025.1-banana-pro-armhf.img.xz | sudo dd of=/dev/sdX bs=4M status=progress
```

This process can take a while, depending on your PC, your microSD card's speed, and the size of the Kali Linux image.

Once the _dd_ operation is complete, boot up the Banana Pro with the microSD card plugged in.

You should be able to [log in to Kali](/docs/introduction/default-credentials/).

## Kali on the Banana Pro - Tips

The bootloader on the Banana Pro is u-boot, and in order to make changes to the kernel command line, the file to edit is `/etc/default/u-boot` and the option is `U_BOOT_PARAMETERS`. If you make any modifications to this file, you will want to then run `u-boot-update`.

## Kali on Banana Pro - Image Customization

If you want to customize the Kali Banana Pro image, including changes to the [packages](/docs/general-use/metapackages/) being installed, changing the [desktop environment](/docs/general-use/switching-desktop-environments/), increasing or decreasing the image file size or generally being adventurous, check out the [Kali-ARM Build-Scripts](https://gitlab.com/kalilinux/build-scripts/kali-arm) repository on GitLab, and follow the _README.md_ file's instructions. The script to use is `banana-pro.sh`.
-->

## 바나나 프로용 칼리 - 빌드 스크립트 가이드

칼리에서는 미리 빌드된 이미지를 다운로드 형태로 제공하지 않지만, [칼리-ARM 빌드 스크립트](https://gitlab.com/kalilinux/build-scripts/kali-arm) GitLab 저장소를 클론하여 _README.md_ 파일의 지침에 따라 직접 이미지를 생성할 수 있습니다. 이때 사용할 스크립트는 `banana-pro.sh`입니다.

빌드 스크립트 실행이 완료되면 스크립트를 실행한 디렉토리의 `images` 폴더에 "img.xz" 파일이 생성됩니다. 이 시점부터는 미리 빌드된 이미지를 다운로드한 경우와 동일한 방식으로 진행하면 됩니다.

이러한 이미지를 생성하는 가장 간편한 방법은 **기존의 칼리 리눅스 환경 내에서** 작업하는 것입니다.

## 바나나 프로용 칼리 - 사용자 가이드

바나나 프로에 칼리를 설치하려면 다음 지침을 따르세요:

1. 최소 16GB 용량의 고속 microSD 카드를 준비하세요. Class 10 카드를 강력히 추천합니다.
2. **[dd](https://manpages.debian.org/testing/coreutils/dd.1.en.html)** 유틸리티를 사용하여 이 파일을 microSD 카드에 기록하세요([칼리 USB 만들기](/docs/usb/live-usb-install-with-windows/)와 동일한 과정).

예시에서는 저장 장치가 `/dev/sdX`에 위치한다고 가정합니다. 이 값을 그대로 복사하지 말고, **실제 드라이브 경로로 변경하세요**.

{{% notice info %}}
이 과정은 microSD 카드의 모든 내용을 지웁니다. 잘못된 저장 장치를 선택하면 컴퓨터의 하드 디스크가 완전히 지워질 수 있습니다.
{{% /notice %}}

```console
$ xzcat images/kali-linux-2025.1-banana-pro-armhf.img.xz | sudo dd of=/dev/sdX bs=4M status=progress
```

이 과정은 PC 성능, microSD 카드 속도, 칼리 리눅스 이미지 크기에 따라 시간이 소요될 수 있습니다.

_dd_ 작업이 완료되면, microSD 카드를 꽂은 상태로 바나나 프로를 부팅하세요.

이제 [칼리에 로그인](/docs/introduction/default-credentials/)할 수 있을 것입니다.
