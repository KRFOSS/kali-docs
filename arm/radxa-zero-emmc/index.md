---
title: 락사 제로 (eMMC)
description:
icon:
weight:
author: ["steev",]
번역: ["xenix4845"]
---

[락사 제로](https://wiki.radxa.com/Zero)는 쿼드 코어 1.8GHz 프로세서와 512MB, 1GB, 2GB, 또는 4GB의 LPDDR4 RAM을 갖추고 있습니다. 여러 eMMC 버전이 제공되며, 최소 32GB를 권장합니다.

기본적으로 칼리 리눅스 락사 제로 이미지는 다른 대부분의 플랫폼과 유사하게 [**kali-linux-default** 메타패키지](/docs/general-use/metapackages/)를 포함합니다. 추가 도구를 설치하려면 [메타패키지 페이지](/docs/general-use/metapackages/)를 참조하세요.

{{% notice info %}}
락사 제로 eMMC 이미지는 2GB 및 4GB 모델에서만 작동합니다. 512MB 및 1GB 모델은 eMMC가 없기 때문입니다.
{{% /notice %}}

<!-- 2022.2 didn't have an image, 2022.3 will 
## Kali on Radxa Zero - User Instructions

If you're unfamiliar with the details of [downloading and validating a 칼리 리눅스 image](/docs/introduction/download-official-kali-linux-images/), or for [using that image to create a bootable device](/docs/usb/live-usb-install-with-windows/), it's strongly recommended that you refer to the more detailed procedures described in the specific articles on those subjects.

To install a pre-built image of the standard build of 칼리 리눅스 on your Raspberry Pi Zero 2 W, follow these instructions:

1. Get a fast microSD card with at least 16GB capacity. Class 10 cards are highly recommended.
2. Download _and validate_ the `Kali Radxa Zero` image from the [downloads](/get-kali/) area. The process for validating an image is described in more detail on [Downloading 칼리 리눅스](/docs/introduction/download-official-kali-linux-images/).
3. Use the **[dd](https://manpages.debian.org/testing/coreutils/dd.1.en.html)** utility to image this file to your microSD card (same process as [making a Kali USB](/docs/usb/live-usb-install-with-windows/).

In our example, we assume the storage device is located at `/dev/sdX`. Do _not_ simply copy these value, **change this to the correct drive path**.

{{% notice info %}}
This process will wipe out your microSD card. If you choose the wrong storage device, you may wipe out your computers hard disk.
{{% /notice %}}

```console
$ xzcat kali-linux-2025.1-radxa-zero-emmc-arm64.img.xz | sudo dd of=/dev/sdX bs=4M status=progress
```

This process can take a while, depending on your PC, your microSD card's speed, and the size of the 칼리 리눅스 image.

Once the _dd_ operation is complete, boot up the Radxa Zero with the microSD card plugged in.

You should be able to [log in to Kali](/docs/introduction/default-credentials/).

## Kali on Radxa Zero (sdcard) - Image Customization

If you want to customize the Kali Radxa Zero sdcard image, including changes to the [packages](/docs/general-use/metapackages/) being installed, changing the [desktop environment](/docs/general-use/switching-desktop-environments/), increasing or decreasing the image file size or generally being adventurous, check out the [Kali-ARM Build-Scripts](https://gitlab.com/kalilinux/build-scripts/kali-arm) repository on GitLab, and follow the _README.md_ file's instructions. The script to use is `radxa-zero-sdcard.sh`.
-->
## 락사 제로(eMMC)용 칼리 - 빌드 스크립트 지침

칼리는 사전 빌드된 이미지를 다운로드용으로 제공하지 않지만, GitLab에서 [칼리-ARM 빌드 스크립트](https://gitlab.com/kalilinux/build-scripts/kali-arm) 저장소를 클론하여 _README.md_ 파일의 지침에 따라 직접 이미지를 생성할 수 있습니다. 사용할 스크립트는 `radxa-zero-emmc.sh`입니다.

빌드 스크립트 실행이 완료되면 스크립트를 실행한 디렉토리의 `images` 폴더에 "img.xz" 파일이 생성됩니다. 이 시점부터는 사전 빌드된 이미지를 다운로드한 경우와 동일한 방식으로 진행하면 됩니다.

이러한 이미지를 생성하는 가장 쉬운 방법은 **기존 칼리 리눅스 환경 내에서** 작업하는 것입니다.

## 락사 제로(eMMC)용 칼리 - 사용자 지침

락사 제로는 칼리 리눅스 이미지를 eMMC에 기록하기 위해 마스크롬(maskrom) 모드로 진입해야 합니다.

락사 제로(eMMC)에 칼리를 설치하려면 다음 지침을 따르세요:

{{% notice info %}}
이 과정은 eMMC의 모든 데이터를 지웁니다.
{{% /notice %}}

Windows:

<!-- TODO: Do installation in Windows and document it -->

Linux(마스크롬 모드):

마스크롬 모드에서 락사 제로의 eMMC에 기록하기 위해 Radxa 웹사이트에서 몇 가지 파일을 다운로드하고 Amlogic 부트 도구를 설치해야 합니다.

 - [radxa-zero-erase-emmc.bin](https://dl.radxa.com/zero/images/loader/radxa-zero-erase-emmc.bin) - eMMC를 자동으로 지운 다음 eMMC를 USB 저장 장치로 표시합니다.
 - [rz-udisk-loader.bin](https://dl.radxa.com/zero/images/loader/rz-udisk-loader.bin) - eMMC 장치를 USB 대용량 저장 장치로 노출합니다.

1. sudo apt update
2. sudo apt install python3-pip
3. sudo pip3 install pyamlboot
4. 락사 제로를 마스크롬 모드로 컴퓨터에 연결
  - 락사 제로 하단에 있는 USB 부팅 버튼을 찾으세요
  - 버튼을 누른 상태에서 락사 제로를 컴퓨터에 연결하세요
  - 전원 LED가 켜지면 버튼을 놓아도 됩니다
5. `lsusb` 명령으로 컴퓨터가 마스크롬 모드의 락사 제로를 인식하는지 확인
  - lsusb
```console
kali@kali:~$ lsusb | grep Amlogic
Bus 001 Device 048: ID 1b8e:c003 Amlogic, Inc. GX-CHIP
```
6. sudo boot-g12.py radxa-zero-erase-emmc.bin
7. **[dd](https://manpages.debian.org/testing/coreutils/dd.1.en.html)** 유틸리티를 사용하여 새로 표시된 USB 장치에 이 파일을 이미징하세요 ([칼리 USB 만들기](/docs/usb/live-usb-install-with-windows/)와 동일한 과정).

아래 예시에서는 저장 장치가 `/dev/sdX`에 위치한다고 가정합니다. 이 값을 단순히 복사하지 마시고, **올바른 드라이브 경로로 변경하세요**.

{{% notice info %}}
이 과정은 eMMC의 모든 데이터를 지웁니다. 잘못된 저장 장치를 선택하면 컴퓨터의 하드 디스크가 지워질 수 있습니다.
{{% /notice %}}

```console
$ xzcat kali-linux-2025.3-radxa-zero-emmc-arm64.img.xz | sudo dd of=/dev/sdX bs=4M status=progress
```

이 과정은 PC, 락사 제로의 저장 장치 속도 및 칼리 리눅스 이미지 크기에 따라 시간이 소요될 수 있습니다.

_dd_ 작업이 완료되면, 락사 제로를 컴퓨터에서 분리한 다음 부팅하세요.

[칼리에 로그인](/docs/introduction/default-credentials/)할 수 있어야 합니다.

Linux(SD 카드로 부팅, eMMC에 쓰기):

{{% notice info %}}
락사 제로 **sdcard** 이미지를 eMMC에 사용할 수 없으며, 그 반대의 경우도 마찬가지입니다. 부트로더는 eMMC와 SD 카드에 따라 다른 위치에 기록되며, 이들은 **서로 호환되지 않습니다**.
{{% /notice %}}

이 방법에서는 먼저 마이크로 SD 카드에서 락사 제로를 부팅한 다음, **[dd](https://manpages.debian.org/testing/coreutils/dd.1.en.html)** 유틸리티를 사용하여 eMMC에 이미지를 기록합니다.

1. [락사 제로(sdcard)](/docs/arm/radxa-zero-sdcard/) 지침을 따르세요
2. 락사 제로를 무선 네트워크에 연결하세요
3. 사용하려는 이미지 파일을 마이크로 SD 카드에 복사하세요. 마이크로 SD 카드에 충분한 여유 공간이 있어야 합니다
4. **[dd](https://manpages.debian.org/testing/coreutils/dd.1.en.html)** 유틸리티를 사용하여 eMMC 장치에 이 파일을 이미징하세요 ([칼리 USB 만들기](/docs/usb/live-usb-install-with-windows/)와 동일한 과정).

아래 예시에서는 저장 장치가 `/dev/mmcblk0`에 위치한다고 가정합니다. 이 값을 단순히 복사하지 마시고, **올바른 드라이브 경로로 변경하세요**.

{{% notice info %}}
이 과정은 eMMC의 모든 데이터를 지웁니다. 잘못된 저장 장치를 선택하면 컴퓨터의 하드 디스크가 지워질 수 있습니다.
{{% /notice %}}

```console
$ xzcat kali-linux-2025.3-radxa-zero-emmc-arm64.img.xz | sudo dd of=/dev/mmcblk0 bs=4M status=progress
```

이 과정은 마이크로 SD 카드, 락사 제로의 저장 장치 속도 및 칼리 리눅스 이미지 크기에 따라 시간이 소요될 수 있습니다.

_dd_ 작업이 완료되면, 락사 제로의 전원을 끄고 마이크로 SD 카드를 분리한 다음, 락사 제로를 부팅하세요.

[칼리에 로그인](/docs/introduction/default-credentials/)할 수 있어야 합니다.
