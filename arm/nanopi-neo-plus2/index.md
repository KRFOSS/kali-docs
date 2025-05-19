---
title: NanoPi NEO Plus2
description:
icon:
weight:
author: ["steev",]
번역: ["xenix4845"]
---

[NanoPi NEO Plus2](http://nanopi.io/nanopi-neo-plus2.html)는 Allwinner H5, 쿼드 코어 Cortex™-A53(ARMv8 64비트) 프로세서와 트리플 코어 Mali-450 MP4 GPU 및 1GB DDR3 RAM을 탑재하고 있습니다. NanoPi NEO Plus2에는 8GB eMMC가 있지만 기본 Kali 설치에는 너무 작기 때문에, 외장 microSD 카드에서 실행합니다.

기본적으로 칼리 리눅스 NanoPi NEO Plus2 이미지는 다른 대부분의 플랫폼과 유사하게 [**kali-linux-default** 메타패키지](/docs/general-use/metapackages/)를 포함합니다. 추가 도구를 설치하려면 [메타패키지 페이지](/docs/general-use/metapackages/)를 참조하세요.

<!--
## Kali on NanoPi NEO Plus2 microSD card - User Instructions

If you're unfamiliar with the details of [downloading and validating a 칼리 리눅스 image](/docs/introduction/download-official-kali-linux-images/), or for [using that image to create a bootable device](/docs/usb/live-usb-install-with-windows/), it's strongly recommended that you refer to the more detailed procedures described in the specific articles on those subjects.

To install a pre-built image of the standard build of 칼리 리눅스 on your NanoPi NEO Plus2, follow these instructions:

1. Get a fast microSD card with at least 16GB capacity. Class 10 cards are highly recommended.
2. Download _and validate_ the `Kali NanoPi NEO Plus2` image from the [downloads](/get-kali/) area. The process for validating an image is described in more detail on [Downloading 칼리 리눅스](/docs/introduction/download-official-kali-linux-images/).
3. Use the **[dd](https://manpages.debian.org/testing/coreutils/dd.1.en.html)** utility to image this file to your microSD card (same process as [making a Kali USB](/docs/usb/live-usb-install-with-windows/).

In our example, we assume the storage device is located at `/dev/sdX`. Do _not_ simply copy these value, **change this to the correct drive path**.

{{% notice info %}}
This process will wipe out your microSD card. If you choose the wrong storage device, you may wipe out your computers hard disk.
{{% /notice %}}

```console
$ xzcat kali-linux-2025.1-nanopi-neo-plus2-arm64.img.xz | sudo dd of=/dev/sdX bs=4M status=progress
```

This process can take a while, depending on your PC, your microSD card's speed, and the size of the 칼리 리눅스 image.

Once the _dd_ operation is complete, boot up the NanoPi NEO Plus2 with the microSD card plugged in.

You should be able to [log in to Kali](/docs/introduction/default-credentials/).

## Kali on the NanoPi NEO Plus2 - Tips

The NanoPi NEO Plus2 will attempt to boot from the microSD card first if one is plugged in.

The wireless chipset is an Ampak AP6210, which is a rebranded Cypress (formerly Broadcom) Wireless card, so enterprising users may be able to get [nexmon](https://github.com/seemoo-lab/nexmon) working, if the work was put in.

If you want to change boot arguments/the kernel command line, you will need to edit the `/boot/boot.cmd` file, and then run `mkimage -C none -A arm -T script -d /boot/boot.cmd /boot/boot.scr`.

## Kali on NanoPi NEO Plus2 - Image Customization

If you want to customize the Kali NanoPi NEO Plus2 image, including changes to the [packages](/docs/general-use/metapackages/) being installed, changing the [desktop environment](/docs/general-use/switching-desktop-environments/), increasing or decreasing the image file size or generally being adventurous, check out the [Kali-ARM Build-Scripts](https://gitlab.com/kalilinux/build-scripts/kali-arm) repository on GitLab, and follow the _README.md_ file's instructions. The script to use is `nanopi-neo-plus2.sh`.
-->

## NanoPi NEO Plus2용 Kali - 빌드 스크립트 지침

Kali는 사전 빌드된 이미지를 다운로드용으로 제공하지 않지만, GitLab에서 [Kali-ARM 빌드 스크립트](https://gitlab.com/kalilinux/build-scripts/kali-arm) 저장소를 클론하여 _README.md_ 파일의 지침에 따라 직접 이미지를 생성할 수 있습니다. 사용할 스크립트는 `nanopi-neo-plus2.sh`입니다.

빌드 스크립트 실행이 완료되면 스크립트를 실행한 디렉토리의 `images` 폴더에 "img.xz" 파일이 생성됩니다. 이후 과정은 사전 빌드된 이미지를 다운로드한 경우와 동일합니다.

이러한 이미지를 생성하는 가장 쉬운 방법은 **기존 칼리 리눅스 환경 내에서** 작업하는 것입니다.

## NanoPi NEO Plus2용 Kali - 사용자 지침

NanoPi NEO Plus2에 Kali를 설치하려면 다음 지침을 따르세요:

1. 최소 16GB 용량의 빠른 microSD 카드를 준비하세요. Class 10 카드를 강력히 권장합니다.
2. **[dd](https://manpages.debian.org/testing/coreutils/dd.1.en.html)** 유틸리티를 사용하여 이 파일을 microSD 카드에 이미징하세요 ([Kali USB 만들기](/docs/usb/live-usb-install-with-windows/)와 동일한 과정).

아래 예시에서는 저장 장치가 `/dev/sdX`에 위치한다고 가정합니다. 이 값을 단순히 복사하지 마시고, **올바른 드라이브 경로로 변경하세요**.

{{% notice info %}}
이 과정은 microSD 카드의 모든 데이터를 지웁니다. 잘못된 저장 장치를 선택하면 컴퓨터의 하드 디스크가 지워질 수 있습니다.
{{% /notice %}}

```console
$ xzcat images/kali-linux-2025.1-nanopi-neo-plus2-arm64.img.xz | sudo dd of=/dev/sdX bs=4M status=progress
```

이 과정은 PC, microSD 카드 속도 및 칼리 리눅스 이미지 크기에 따라 시간이 소요될 수 있습니다.

_dd_ 작업이 완료되면, microSD 카드를 꽂은 상태로 NanoPi NEO Plus2를 부팅하세요.

[Kali에 로그인](/docs/introduction/default-credentials/)할 수 있어야 합니다.

## NanoPi NEO Plus2용 Kali - 팁

NanoPi NEO Plus2는 microSD 카드가 꽂혀 있다면 먼저 이 카드에서 부팅을 시도합니다.

무선 칩셋은 Ampak AP6210으로, 이는 Cypress(이전 Broadcom) 무선 카드의 리브랜딩 제품입니다. 따라서 노력을 들인다면 진취적인 사용자들은 [nexmon](https://github.com/seemoo-lab/nexmon)을 작동시킬 수 있을 것입니다.

부팅 인수/커널 명령줄을 변경하고 싶다면, `/boot/boot.cmd` 파일을 편집한 다음 `mkimage -C none -A arm -T script -d /boot/boot.cmd /boot/boot.scr`를 실행해야 합니다.
