---
title: USB 드라이브에 완전히 암호화된 독립형 칼리 리눅스 2025.1c 설치하기
description:
icon:
weight: 75
author: ["voidyourwarranty",]
번역: ["xenix4845"]
---

{{% notice info %}}
이 가이드는 고급 사용자를 위한 거예요. USB 드라이브에 완전히 암호화된 칼리 리눅스를 설치하는 복잡한 과정을 설명하고 있으니, 모든 단계를 정확히 이해하고 따라할 수 있는 기술적 지식이 필요해요.
또한 원본 문서에서 사용하는 칼리 ISO 버전은 너무 오래되었고 미러에 더 이상 없어서 최신버전으로 수정되었어요. 참고해주세요.
{{% /notice %}}

# USB 드라이브에 완전히 암호화된 독립형 칼리 리눅스 2025.1c 설치하기

이 지침을 통해 외부 USB 드라이브에 완전히 암호화된 독립형 칼리 리눅스 2025.1c를 설치할 수 있어요. 여기서 말하는 설치란
- luks를 사용하여 부트 및 스왑 파티션을 포함한 모든 영역을 암호화한 거예요,
- EFI 부팅 또는 레거시 부팅을 사용해 USB 드라이브에서 부팅할 수 있는 모든 64비트 Intel/AMD 머신에서 실행돼요,
- "라이브" 시스템을 포함하지 않아요. 즉, USB 드라이브에서 부팅하는 시스템이 RAM 디스크로 `chroot`할 필요가 없어요; 현재 칼리 설치는 내부 디스크에서와 마찬가지로 외부 드라이브에서 실행돼요; 특히 `apt-get install <something>`으로 추가 패키지를 설치할 수 있고, 설정 파일을 편집할 수 있으며, 이러한 모든 변경사항은 다음에 다른 기기에서 이 USB 드라이브로 부팅해도 영구적이에요; 사용자 지정 커널을 컴파일하고 USB 드라이브에 설치할 수도 있어요.

사용 사례로는 수리, 포렌식 또는 유지 관리 도구로 사용할 맞춤형 부팅 가능한 칼리 2025.1c 설치가 있어요. 네트워크를 특정 머신의 관점에서 검사하려면 일반적으로 포트 미러링을 제공하도록 일부 스위치를 재구성하고 트래픽을 프로브할 수 있는 세그먼트로 모든 트래픽을 전환해야 해요. 이제 대안이 있어요. 관련 기기를 USB 칼리 설치에서 간단히 재부팅하고 네트워크에 연결하면 일반적인 하드웨어와 일반적인 MAC 주소를 그대로 사용하지만 이번에는 칼리를 실행하며 바로 거기에 있어요.

또한 메인 컴퓨터가 Windows나 다른 리눅스를 실행하고 있고 가끔 칼리가 필요한 경우 이중 부팅 설정을 피할 수 있어요. 칼리가 필요할 때마다 USB 드라이브에서 부팅하면 되고, 그 외에는 기존 설치를 그대로 둘 수 있어요.

물론 정확히 무엇을 하고 있는지 알고 있는 경우에만 다음 지침을 사용하세요. 작업하는 기기의 하드디스크를 읽을 수 없게 만들어 모든 데이터를 잃을 수 있어요.

## 개요

이러한 설치를 만드는 것은 까다롭고 상당한 수동 개입이 필요해요:
- 먼저 기존 리눅스 설치를 사용하여 USB 드라이브를 파티션하고 여러 파티션을 설정하며, 일부는 암호화돼요:
    1. `ext4` 파일 시스템이 있는 `/boot` 파티션은 luks 버전 1로 암호화돼요. 다행히도 부트 로더 `grub2`는 luks1 암호화된 파티션을 마운트하고 부팅할 수 있어요.
    2. `grub2`가 들어갈 부트 섹터.
    3. `vfat` 파일 시스템이 있는 EFI 파티션.
    4. luks2로 암호화된 `swap` 파티션.
    5. `btrfs` 파일 시스템이 있는 설치의 루트(`/`) 파티션. 더 세분화된 구조는 서브볼륨을 생성하여 달성해요.
- 또한 칼리 "베어 메탈" 설치 프로그램이 있는 USB 드라이브가 필요해요. 이것은 데비안 유형 설치 프로그램이에요. 전문가 모드 설치를 중단하고 명령줄에서 개입하여 대상 USB 드라이브의 암호화된 파티션을 마운트한 다음 칼리 리눅스 2025.1c를 설치하고 `/boot` 파티션에 초기 RAM 디스크를 생성하여 `grub2`의 키를 포함하여 암호화된 파티션을 마운트할 수 있게 해야 해요. 하지만 이 단계에서 `grub2` 부트 로더를 설치해서는 안 돼요. 그렇게 하면 설치를 수행하는 기기의 EFI 부팅 정보를 변경하여 의도하지 않은 이중 부팅 설정을 생성할 수 있으며, 따라서 부트 로더가 없는 완전한 설치만 남게 돼요.
- 그런 다음 구조 시스템이 있는 USB 드라이브가 필요해요. 칼리 "베어 메탈" 설치 프로그램으로는 이를 수행할 수 없었지만 "Xubuntu 20.04 LTS" 라이브 USB 매체를 사용해요. 이를 통해 `grub2`를 `--removable` 모드로 설치할 수 있어요. 이렇게 하면 `grub2`가 작업 중인 기기의 EFI 부팅 정보를 수정하지 않고 대상 USB 드라이브에만 영향을 미치게 돼요.

## 준비

### 칼리 리눅스 2025.1c 베어 메탈 설치 프로그램

{{% notice info %}}
원본 문서에서는 `kali-linux-2021.4-installer-amd64.iso`를 사용하였지만 해당 버전은 더 이상 미러에서 구할 수 없어서 최신 버전으로 수정되었어요.
{{% /notice %}}

먼저 [칼리 리눅스 2025.1c 베어 메탈 설치 프로그램](https://kali.org/get-kali/#kali-installer-images)의 ISO 이미지를 얻어요. 제 경우 ISO 이미지 파일은 `kali-linux-2025.1c-installer-amd64.iso`예요. 기존 리눅스 머신에서 작업해요. 제 경우 Ubuntu 20.04 LTS예요.

빈 USB 펜 드라이브를 삽입하고 ISO 이미지를 복사해요. USB 펜 드라이브를 연결하면 해당 블록 장치가 무엇인지 확인해야 해요. 일반적으로 `/dev/sdx`일 거예요. 여기서 `x`는 `a`, `b` 등의 문자 중 하나예요. 리눅스 배포판이 USB 펜 드라이브를 자동 마운트하면 `df`, `mount` 또는 `lsblk`가 연결된 장치 파일을 보여줘요. 진행하기 전에 드라이브가 마운트 해제되었는지 확인해요(예: `sudo umount /dev/sdxn`. 여기서 `n`은 자동 마운트된 드라이브의 모든 파티션 번호를 나타내요).
드라이브가 자동 마운트되지 않은 경우, 연결 전후에 `ls /dev`를 사용하여 장치 파일 이름을 확인할 수 있어요.

제 Xubuntu 20.04 LTS 머신에서 첫 번째 USB 펜 드라이브는 `/dev/sda`와 연결되며, 따라서 ISO 이미지를 펜 드라이브에 복사하려면 다음을 실행해요:

```console
$ sudo dd if=kali-linux-2025.1c-installer-amd64.iso of=/dev/sda status=progress
```

(참고로 Ubuntu와 같은 시스템에서는 `root` 사용자로 작업하는 대신 `sudo`를 통해 명령을 실행하여 루트 권한을 얻을 수 있어요)

### Xubuntu 20.04 LTS 설치 프로그램

또한 [Xubuntu 20.04 LTS 라이브 설치 프로그램](https://xubuntu.org/download)의 ISO 이미지가 두 번째 USB 펜 드라이브에 필요해요. 제 경우 ISO 이미지 파일은 `xubuntu-20.04-desktop-amd64.iso`예요. 동일한 방식으로 두 번째 USB 펜 드라이브(최소 4GB 크기)에 복사해요. 첫 번째 USB 펜 드라이브를 분리하고 두 번째 USB 펜 드라이브를 연결해요:

```console
$ sudo dd if=xubuntu-20.04-desktop-amd64.iso of=/dev/sda status=progress
```

### 대상 USB 드라이브 파티션 나누기

마지막으로 칼리 리눅스 2025.1c 설치를 수행할 USB 드라이브를 삽입해요. 이를 위해 USB 3.1 gen 2 인클로저에 128GB NVMe M.2 SSD를 사용해요. 두 번째 펜 드라이브를 분리하고 대상 USB 드라이브를 연결해요. 위와 마찬가지로 대상 USB 드라이브가 연결된 장치 `/dev/sdx`를 확인하고 해당 파티션이 마운트되지 않았는지 확인해요. 제 경우 대상 USB 드라이브는 다시 `/dev/sda`와 연결돼요.

그런 다음 `sudo gdisk /dev/sda`를 사용하여 다음과 같은 파티션을 생성해요:

```plaintext
/dev/sda1,   4.0 GiB, type 8301, Linux reserved, will become /boot
/dev/sda2,   2.0 MiB, type ef02, BIOS boot, will contain grub2
/dev/sda3, 128.0 MiB, type ef00, EFI system partition
/dev/sda4,   8.0 GiB, type 8200, Linux swap
/dev/sda5,  <something big>, type 8300, Linux filesystem, will be the main partition
```

여기서 스왑에 8GB를 할당했으며 이는 제 목적에 충분해요. 이 파티션이 너무 작으면 디스크로 절전(하이버네이션)을 수행할 수 없어요. 칼리 리눅스 USB 드라이브를 다른 기기에 연결하려는 경우 하이버네이션을 잊어야 할 거예요. 시스템이 절전 상태에서 깨어나지 않을 가능성이 높기 때문이에요.

주 파티션은 원하는 만큼 크게 설정할 수 있어요. 추가 파티션이 필요한 경우 현재 노트에서 `/dev/sda5`를 처리하는 것과 동일하게 처리할 수 있어요.

`gdisk`를 사용하는 대신 명령줄에서 대상 USB 드라이브를 다시 파티션할 수도 있어요:

```console
$ sgdisk --zap-all /dev/sda
$ sgdisk --new=1:0:+4096M /dev/sda
$ sgdisk --new=2:0:+2M /dev/sda
$ sgdisk --new=3:0:+128M /dev/sda
$ sgdisk --new=4:0:+8192M /dev/sda
$ sgdisk --new=5:0:0 /dev/sda
$ sgdisk --typecode=1:8301 --typecode=2:ef02 --typecode=3:ef00 --typecode=4:8200 --typecode=5:8300 /dev/sda
$ sgdisk --change-name=1:/boot --change-name=2:GRUB --change-name=3:EFI-SP --change-name=4:swap --change-name=5:rootfs /dev/sda
$ sgdisk --hybrid 1:2:3 /dev/sda
$ sgdisk --print /dev/sda
```
(참고로 파티션 5에 대한 크기 힌트 `:0`은 남은 모든 여유 공간을 요청한다는 것을 나타내요)

다음으로 파티션 1, 4 및 5에 대해 luks 암호화를 설정해요. 참고로 부트 로더 `grub2`는 luks 버전 1만 마운트할 수 있어요:

```console
$ sudo cryptsetup luksFormat --type=luks1 /dev/sda1
$ sudo cryptsetup luksFormat /dev/sda4
$ sudo cryptsetup luksFormat /dev/sda5
```

스왑 및 루트 파티션을 잠금 해제하는 키는 `/boot` 파티션의 초기 RAM 디스크에 배치되므로 세 파티션 모두에 동일한 암호를 설정할 수 있어요. 다른 암호를 사용하는 건 보안을 강화하지 않아요. 이제 세 파티션을 잠금 해제해요:

```console
$ sudo cryptsetup open /dev/sda1 LUKS_BOOT
$ sudo cryptsetup open /dev/sda4 LUKS_SWAP
$ sudo cryptsetup open /dev/sda5 LUKS_ROOT
```

`ls /dev/mapper` 명령은 잠금 해제된 파티션과 연결된 세 장치를 보여줘요. 이제 파티션 1, 3, 4 및 5를 포맷할 수 있어요. 즉, 파일 시스템을 생성해요:

```console
$ sudo mkfs.ext4 -L boot /dev/mapper/LUKS_BOOT
$ sudo mkfs.vfat -F 16 -n EFI-SP /dev/sda3
$ sudo mkswap -L swap /dev/mapper/LUKS_SWAP
$ sudo mkfs.btrfs -L root /dev/mapper/LUKS_ROOT
```

마지막으로 매핑된 장치를 제거해요(기기가 파티션을 암호 해독하는 방법을 잊게 돼요):

```console
$ sudo cryptsetup close LUKS_BOOT
$ sudo cryptsetup close LUKS_SWAP
$ sudo cryptsetup close LUKS_ROOT
```
## 칼리 리눅스 2025.1c 설치

### 설치 프로그램 설정

이제 컴퓨터의 BIOS를 USB 드라이브에서 부팅하도록 설정해요. EFI 모드에서 부팅하면 EFI 모드에서만 부팅 가능한 USB 드라이브를 얻을 수 있어요. 레거시 BIOS 모드에서 부팅하면 레거시 및 EFI 모드에서 부팅 가능한 USB 드라이브를 생성할 수 있어요.

칼리 2025.1c 설치 프로그램이 있는 USB 펜 드라이브를 연결하고 컴퓨터를 부팅해요. 설치 중 네트워크 액세스가 필요해요.

*고급 옵션* 및 *그래픽 전문가 설치*를 선택해요. 설치 메뉴의 다음 항목을 실행해요(이 외에는 실행하지 않아요):
- *언어 선택*
- *키보드 구성*
- *설치 미디어 감지 및 마운트*
- *debconf 사전 구성 파일 로드*
- *설치 미디어에서 설치 구성 요소 로드*, `crypto-dm-modules`, `fdisk-udeb`, `mbr-udeb`, `parted-udeb`, `rescue-mode`를 선택해요.
- *네트워크 하드웨어 감지*
- *네트워크 구성*, 이 단계에서 네트워크 액세스가 있는지 확인해요.
- *사용자 및 암호 설정*, *shadow 암호*를 선택하고 `sudo`를 통해 루트 권한을 얻을 수 있는 주요 사용자를 설정해요.
- *시계 구성*, *ntp*를 선택하면 네트워크에 연결되면 기기가 폴링을 시작하므로 설치에 바람직하지 않을 수 있어요.

*디스크 감지*를 실행하지 않아요. 대신 [Ctrl]+[Alt]+[F3]을 누르고 [Enter]를 눌러 루트 권한이 있는 텍스트 콘솔을 열어요. 이제 칼리 리눅스 2025.1c를 설치할 대상 USB 드라이브를 연결해요. 연결 전후에 `ls /dev`를 사용하여 연결된 장치 파일을 확인해요. 제 경우 USB 펜 드라이브는 `/dev/sda`이고 대상 USB 드라이브는 `/dev/sdb`예요.

세 암호화된 파티션을 잠금 해제해요:

```console
$ cryptsetup open /dev/sdb1 LUKS_BOOT
$ cryptsetup open /dev/sdb4 LUKS_SWAP
$ cryptsetup open /dev/sdb5 LUKS_ROOT
```
### 파티션 나누기

이제 [Ctrl]+[Alt]+[F5]를 눌러 그래픽 모드에서 설치를 계속해요.

- *디스크 감지*
- *디스크 파티션 나누기*, 수동.

`LUKS_BOOT` 아래의 `4.3 GB ...` 줄에 커서를 놓고 [Enter]를 눌러요. `Ext4`로 사용, 포맷, `/boot`에 마운트, 옵션 `noatime`을 선택하고 *파티션 설정 완료*를 선택해요.
`LUKS_ROOT` 아래의 줄에 커서를 놓고 [Enter]를 눌러요. `btrfs`로 사용, 포맷, `/`에 마운트, 옵션 `noatime`을 선택하고 *파티션 설정 완료*를 선택해요.
`LUKS_SWAP` 파티션은 이미 인식되어 `swap`으로 표시되었어요.

*파티션 완료 및 디스크에 변경 사항 쓰기*를 선택해요.

그런 다음 다시 [Ctrl]+[Alt]+[F3]을 눌러 루트 콘솔로 돌아가요.

`df`를 호출하면 두 개의 비스왑 암호화된 파티션이 `/target` 및 `/target/boot`에 각각 마운트되었음을 보여줘요. 기기가 EFI 모드에서 부팅된 경우 `/target/boot/efi`도 있지만 잘못된 거예요. 첫 번째 내부 하드디스크의 EFI 파티션을 가리키고 있어요. 대신 대상 USB 드라이브의 EFI 파티션이어야 해요. 아래에서 자세히 설명해요.

### Btrfs 조정

데비안 설치 프로그램은 `btrfs` 파티션에 `@rootfs` 서브볼륨을 생성했어요. 최상위 서브볼륨을 마운트하고 추가 서브볼륨 `@`, 기본 서브볼륨 `@home`, `@root`, `@snapshots`, `@var`를 생성하며 `@`를 기본으로 설정해요:

```console
$ mkdir -p /mnt/point/
$ mount -o subvol=/ /dev/mapper/LUKS_ROOT /mnt/point
$ cd /mnt/point/
$ btrfs subvolume create @
$ btrfs subvolume create @home
$ btrfs subvolume create @root
$ btrfs subvolume create @snapshots
$ btrfs subvolume list .
$ btrfs subvolume set-default 257 . # where 257 is the subvolume ID that was displayed for @
$ cd /
$ umount /mnt/point
$ umount /target/boot/efi # only required when booted in EFI mode
$ umount /target/boot
$ umount /target
$ mount -o subvol=@ /dev/mapper/LUKS_ROOT /target
$ mkdir -p /target/boot
$ mkdir -p /target/etc
$ mkdir -p /target/media
$ mkdir -p /target/snapshots
$ mount /dev/mapper/LUKS_BOOT /target/boot
$ mount -o subvol=@rootfs /dev/mapper/LUKS_ROOT /mnt/point
$ cp /mnt/point/etc/fstab /target/etc/fstab
```

여기서 `btrfs subvolume list`는 현재 볼륨의 서브볼륨 ID를 보여주며, `set-default` 명령에서 `@`에 할당된 번호를 사용해요.

`@` 서브볼륨을 기본으로 설정하여 `/`에 마운트되도록 했어요. 이는 `snapper` 명명 규칙을 따르는 거예요. 추가 서브볼륨을 얼마나 생성할지는 취향에 따라 달라요. `@home`, `@root`, `@snapshots`를 생성하여 나중에 `/home`, `/root`, `/snapshots`에 마운트해요. `/var`를 별도의 서브볼륨으로 설정하는 것을 선호하는 사람도 있지만, 이는 아래에서 작동하지 않아요.

### 파일 시스템 테이블

`umount /target/boot/efi`는 설치 미디어가 EFI 모드에서 부팅된 경우에만 필요해요. 여기서 핵심은 USB 드라이브에 설치하고 있음에도 불구하고 지금까지 마운트된 EFI 파티션이 컴퓨터의 내부 하드디스크의 EFI 파티션이라는 점이에요. 이 잘못된 마운트는 나중에 부트 로더를 설치할 때 심각한 문제를 일으킬 수 있어요.

그런 다음 `blkid -s PARTUUID -o value /dev/sdb3`를 호출하여 대상 USB 드라이브의 EFI 파티션의 UUID를 확인하고 종이에 적어둬요. `vim /target/etc/fstab`를 호출하여 파일 시스템 테이블을 다음과 같이 수정해요.

먼저 `/`에 대한 줄에서 `subvol=@rootfs`를 `subvol=@`로 변경해요. 또한 다음 줄을 추가해요:

```plaintext
PARTUUID=<whatever> /boot/efi vfat umask=0077 0 1
```

방금 적어둔 UUID를 사용해요. 이를 통해 설치가 장치 번호와 상관없이 올바른 EFI 파티션을 찾고 마운트할 수 있어요. 그런 다음 방금 생성한 `btrfs` 서브볼륨을 마운트하기 위해 줄을 추가해요. SSD 작업에 유용한 마운트 옵션도 추가했어요. 제 경우 다른 줄은 다음과 같아요:

```plaintext
/dev/mapper/LUKS_ROOT /               btrfs   defaults,noatime,ssd,compress=lzo,subvol=@              0 0
/dev/mapper/LUKS_BOOT /boot           ext4    defaults,noatime                                                    0 1
/dev/mapper/LUKS_ROOT /home           btrfs   defaults,noatime,ssd,compress=lzo,subvol=@home      0 2
/dev/mapper/LUKS_ROOT /root           btrfs   defaults,noatime,ssd,compress=lzo,subvol=@root      0 3
/dev/mapper/LUKS_ROOT /snapshots      btrfs   defaults,noatime,ssd,compress=lzo,subvol=@snapshots 0 4
/dev/mapper/LUKS_SWAP none            swap    sw                                                                          0 0
```

(여기서 한 줄만 `LUKS_BOOT`라고 표시되어 있어요). 편집기 `nano`에서 [Ctrl]+[o]를 눌러 파일을 저장하고 [Enter]로 확인한 다음 [Ctrl]+[x]를 눌러 편집기를 종료해요. 이제 최상위 서브볼륨을 제거하고 적절한 위치에 올바른 EFI 파티션을 마운트해요:

```console
$ umount /mnt/point
$ mkdir -p /target/boot/efi
$ mount /dev/sdb3 /target/boot/efi
$ mount -o subvol=@home /dev/mapper/LUKS_ROOT /target/home
$ mount -o subvol=@root /dev/mapper/LUKS_ROOT /target/root
```

설치에 영향을 받을 수 있는 추가 `btrfs` 서브볼륨을 생성한 경우 여기에서도 마운트해요.

### 실제 설치

다시 [Ctrl]+[Alt]+[F5]를 눌러 그래픽 화면으로 이동하고 설치를 계속해요.

- *기본 시스템 설치*, 커널 *linux-image-5.14.0-kali4-amd64*, *generic initrd*를 선택해요.
- *패키지 관리자 구성*.
- *소프트웨어 선택 및 설치*, 기본값을 유지해요.

하지만 부트 로더를 설치하지 않고

- *부트 로더 없이 계속*

일부 사람들은 휴대용 드라이브에 리눅스를 설치하려고 시도했을 때 설치가 작업 중인 기기의 내부 하드디스크의 부트 섹터를 수정했다고 보고해요. 위에서 `/target/boot/efi`에 올바른 파티션을 마운트함으로써 이를 방지했을 거라고 생각해요. 하지만 설치 프로그램이 수행할 `grub2` 설치를 사용할 수 없어요. 이는 `--removable` 플래그가 없기 때문이에요. 따라서 부트 로더 설치를 나중으로 미뤄요.

### 초기 RAM 디스크 설정

그런 다음 다시 루트 콘솔로 돌아가 [Ctrl]+[Alt]+[F3]을 눌러 `grub2`가 결국 설치될 때 부트 파티션을 암호 해독할 수 있도록 해요.

새로 설치된 시스템으로 루트를 변경하고 모든 파티션과 서브볼륨을 마운트해요:

```console
$ for n in dev proc sys run etc/resolv.conf; do mount --bind /$n /target/$n; done
$ chroot /target
$ mount -a
```

`mount -a`가 오류를 보고하면 이는 위에서 수정한 파일 시스템 테이블의 오타 때문일 수 있어요.

`/etc/resolv.conf`는 설치 프로그램이 네트워크를 설정할 때 얻은 DNS 정보를 잃지 않기 위해 있어요. 대상 시스템에서 `grub2`를 설치하고 업데이트하며 초기 RAM 디스크에 암호 해독 도구를 추가하기 위해 필요한 모든 도구를 설치해요:

```console
$ apt-get install grub-common grub-efi-amd64 os-prober
$ apt-get install cryptsetup-initramfs
```

다음으로, 세 암호화된 파티션을 잠금 해제할 수 있는 랜덤 luks 키 파일을 생성하고 초기 RAM 디스크가 구성되기 전에 몇 가지 구성 파일을 설정해요:

```console
$ echo "KEYFILE_PATTERN=/etc/luks/*.keyfile" >>/etc/cryptsetup-initramfs/conf-hook
$ echo "UMASK=0077" >>/etc/initramfs-tools/initramfs.conf
$ mkdir -p /etc/luks
$ dd if=/dev/urandom of=/etc/luks/boot_os.keyfile bs=4096 count=1
$ chmod u=rx,go-rwx /etc/luks
$ chmod u=r,go-rwx /etc/luks/boot_os.keyfile
$ cryptsetup luksAddKey /dev/sdb1 /etc/luks/boot_os.keyfile
$ cryptsetup luksAddKey /dev/sdb4 /etc/luks/boot_os.keyfile
$ cryptsetup luksAddKey /dev/sdb5 /etc/luks/boot_os.keyfile
$ echo "LUKS_BOOT UUID=$(blkid -s UUID -o value /dev/sdb1) /etc/luks/boot_os.keyfile luks,discard" >>/etc/crypttab
$ echo "LUKS_SWAP UUID=$(blkid -s UUID -o value /dev/sdb4) /etc/luks/boot_os.keyfile luks,discard" >>/etc/crypttab
$ echo "LUKS_ROOT UUID=$(blkid -s UUID -o value /dev/sdb5) /etc/luks/boot_os.keyfile luks,discard" >>/etc/crypttab
$ /usr/sbin/update-initramfs -u -k all
```

부팅 과정에서 `grub2`는 암호화된 `/boot` 파티션이 있음을 감지해요. `/boot`와 처음 연결한 암호를 묻는 메시지가 표시돼요. 그런 다음 `/boot` 파티션에는 방금 생성한 키 파일을 포함하는 초기 RAM 디스크가 포함된 커널이 있으며 이를 사용하여 모든 암호화된 파티션을 잠금 해제할 수 있어요.

암호적으로 가장 약한 링크는 `/boot` 파티션의 암호 보호예요. 그것이 깨지면 다른 파티션도 손상돼요. 그리고 이는 단지 luks1이에요.

### 설치 완료

마지막으로 [Ctrl]+[Alt]+[F5]를 눌러 그래픽 설치 프로그램으로 돌아가

- *설치 완료*를 UTC로 설정된 시스템 시계로 완료해요.

결국 *계속*을 클릭하여 시스템을 부팅하지만 USB 펜 드라이브와 USB 설치 대상을 제거하고 컴퓨터를 꺼요.

## 부트 로더 생성

EFI 부팅 가능한 USB 드라이브를 생성하려면 다음에 컴퓨터를 부팅할 때 EFI 모드여야 해요. 필요한 경우 BIOS/UEFI 설정으로 이동하여 EFI 모드를 켜요.

이제 Xubuntu 20.04.3 LTS 라이브 설치 미디어가 포함된 USB 펜 드라이브를 연결하고 부팅하여 *Xubuntu 설치 없이 사용해보기*를 선택하여 라이브 시스템으로 이동해요. 그런 다음 설치 대상이 되는 USB 드라이브를 연결해요.

Xubuntu 라이브 시스템이 네트워크 액세스가 있는지 확인해요(이는 그래픽 인터페이스의 `NetworkManager`에 의해 수행돼요). 터미널을 열고 `ls /sys/firmware/efi/efivars`를 호출하여 EFI 모드에 있는지 확인해요. 이 디렉토리가 없으면 레거시 BIOS 부팅 모드에 있는 거예요.

다시 `ls /dev` 및 `df`를 사용하여 설치 대상이 연결된 장치 `/dev/sdx`를 확인해요. Xubuntu 라이브 이미지는 `/media/cdrom`에 마운트되어 있으므로 이것이 *아니에요*. 설치 대상 USB 드라이브에는 생성한 5개의 파티션이 포함되어 있어요. 다음에서는 다시 `/dev/sdb`가 설치 대상에 해당한다고 가정해요.

Xubuntu 라이브 시스템은 세 개의 luks 파티션을 마운트하려고 시도해요. 세 번 클릭하여 이를 취소해요.

암호화된 파티션을 잠금 해제하고 필요한 파티션을 수동으로 마운트해요:

```console
$ sudo -i
$ cryptsetup open /dev/sdb1 LUKS_BOOT
$ cryptsetup open /dev/sdb5 LUKS_ROOT
$ cd /
$ mkdir -p /target
$ mount /dev/mapper/LUKS_ROOT /target
$ mount /dev/mapper/LUKS_BOOT /target/boot
$ mount /dev/sdb3 /target/boot/efi
```

그런 다음 새로 설치된 시스템으로 루트를 변경하고 모든 것을 마운트해요:

```console
$ for n in dev dev/pts proc sys sys/firmware/efi/efivars run etc/resolv.conf; do mount --bind /$n /target/$n; done
$ chroot /target
$ mount -a
```

이제 `vim /etc/default/grub`를 호출하여 다음 줄을 추가해요:

```plaintext
GRUB_ENABLE_CRYPTODISK=y
```

[Ctrl]+[o] 및 [Enter]로 파일을 저장하고 [Ctrl]+[x]로 편집기를 종료해요. 그런 다음 `grub2`를 제거 가능한 모드로 설치해요:

```console
$ grub-install --removable /dev/sdb
$ update-grub
```
Xubuntu 라이브 시스템이 있는 파티션에 대한 grub 드라이브가 없다는 오류가 발생할 수 있어요. 이를 무시해도 돼요. [Ctrl]+[d]를 눌러 `chroot` 세션을 종료한 다음 Xubuntu의 그래픽 인터페이스를 사용하여 컴퓨터를 종료해요. USB 드라이브 설치가 완료되었어요.

## 사용법

### 새 칼리 리눅스 2025.1c USB 드라이브 부팅 방법

USB 드라이브에서 레거시 또는 EFI 모드로 부팅하면 `grub2`가 암호를 묻는 메시지를 표시해요(`/boot` 파티션의 암호). 그런 다음 새 칼리 리눅스 2025.1c로 바로 부팅해요.

하지만 일부 오래된 UEFI 기기에서는 `grub2` 셸로 이동해요. 그래도 `exit` [Enter]를 입력하면 암호를 묻는 메시지가 표시돼요.

### 칼리와 네트워킹

칼리 리눅스는 네트워크 연결에 대해 매우 보수적이어서 새 설치에는 추가 설정이 필요할 수 있어요. USB 드라이브를 다른 기기에서 부팅하려는 경우 기기별로 네트워크를 설정할 가능성이 높아요.

WLAN을 그래픽 인터페이스의 `NetworkManager` 제어 하에 두려면 `sudo vim /etc/NetworkManager/NetworkManager.conf`를 호출하고 `[ifupdown]` 아래에 `managed=true`를 설정해야 해요. `sudo systemctl restart NetworkManager`가 충분해야 하지만 재부팅 후에만 작동했던 것으로 기억해요.

### 하드웨어 제한

데비안 스타일 설치 프로그램에 대해 전문가가 아니지만, 전체 커널과 모든 모듈을 설치하면서도 선택된 펌웨어 드라이버만 대상 설치에 복사하는 것 같아요. 설치 프로그램이 실행 중인 하드웨어를 검사한 다음 해당 기기에 적합한 펌웨어를 설치하는 것으로 보여요.

중국산 무명 Intel Core i5-10210U 기반 노트북(통합 Intel 그래픽 및 사운드 포함)에서 테스트 설치를 수행했으며 해당 기기에서 사운드가 작동해요. 하지만 동일한 설치를 Lenovo T14 AMD Gen 1(AMD Ryzen 5 Pro 4650U, 통합 Radeon 그래픽 및 Realtek ALC257 오디오)에서 부팅하면 사운드가 작동하지 않아요. 따라서 일부 하드웨어별 수동 구성이 필요할 수 있어요. 다행히도 제 경우 이더넷과 WLAN은 지금까지 시도한 모든 하드웨어에서 바로 작동해요.

마지막으로 설치는 디스크로 절전(하이버네이션)을 허용하지만 한 하드웨어에서 시스템을 절전 상태로 두고 다른 기기에서 깨우려고 하면 작동하지 않을 수 있어요.

### 사용자 지정 커널 컴파일

사용자 지정 커널 컴파일은 [이 칼리 지침](/docs/development/recompiling-the-kali-linux-kernel/)에 설명된 대로 약간의 조정만으로 작동해요.

지금까지 수행한 설치를 시작으로 다음 패키지가 필요해요:

```console
$ sudo apt-get install build-essential libncurses5-dev fakeroot xz-utils libelf-dev libssl-dev dwarves
```

현재 커널 소스를 설치하고 이를 `~/src`에 다음과 같이 압축 해제해요:

```console
$ sudo apt-get install linux-source-5.15
$ mkdir -p ~/src/
$ cd ~/src/
$ tar -xzf /usr/src/linux-source-5.15.tar.xz
```
사용자 지정 커널 구성 파일이 있는 경우 이를 `~/src/linux-source-5.15/.config`에 복사해요. 실행 중인 커널의 구성을 시작점으로 사용하려면 `/boot` 파티션에서 구성을 가져와요:

```console
$ cp /boot/config-5.14.0-kali4-amd64 ~/src/linux-source-5.15/.config
```

그런 다음 커널을 다음과 같이 구성해요:

```console
$ cd ~/src/linux-source-5.15/
$ make menuconfig
```

그리고 다음과 같이 컴파일해요. 여기서는 데비안 스타일 커널 패키지를 빌드하며, 이는 모든 패치 등을 자동으로 처리해요:

```console
$ make clean
$ make deb-pkg LOCALVERSION=-custom KDEB_PKGVERSION=$(make kernelversion)-1
$ ls ../*.deb
```

새로 생성된 데비안 커널 패키지를 설치하기만 하면 돼요. 초기 RAM 디스크가 포함되어 있으며 luks 암호화된 파티션을 암호 해독할 수 있으며 모든 것을 `/boot` 파티션에 배치하여 `grub2`가 찾을 수 있어요. 커널의 정확한 버전은 마지막으로 업그레이드하고 소스를 압축 해제한 시점의 칼리 2025.1c 업데이트 상태에 따라 달라요. 위의 `ls ../*.deb` 명령은 생성된 데비안 패키지의 전체 이름을 보여줘요:

```console
$ sudo dpkg -i ~/src/linux-image-5.15.5-custom_5.15.5-1_amd64.deb
```

`grub2`를 다시 설치하거나 다른 수동 조정을 수행할 필요가 없어요. 암호화된 부팅 절차는 여전히 작동해요.

### 암호화에 대한 비고

- USB 드라이브를 검사할 수 있는 사람에게는 `grub2` 부트 파티션, EFI 파티션 및 세 개의 luks 암호화된 파티션이 있다는 것이 명백할 거예요. *그럴듯한 부인*은 없어요. 이는 luks 파티션이 표준화된 헤더를 가지고 있기 때문이에요.
- luks 헤더에는 암호로 보호된 AES 키가 포함되어 있으므로 AES 키 자체를 공격할 필요가 없어요. 데이터 센터에서 병렬 사전 공격을 실행할 수 있어요. 이는 luks 암호화가 *단일 요소*를 사용하기 때문이에요. 이를 개선하려면 AES 키를 다른 매체에 저장하여 *이중 요소*를 얻을 수 있어요. 하지만 이는 `grub2`로 부팅할 수 없어요.
- `/boot` 파티션의 암호를 공격하면 다른 파티션도 손상돼요. 이는 단지 luks1이에요.
- `/boot` 파티션 암호화는 *악성 메이드 공격*을 방지해요. 암호화되지 않은 `/boot`가 있으면 공격자가 USB 드라이브를 수정할 수 있어요. 이는 더 이상 불가능해요.
- `grub2` 부트 로더를 대체 프로그램으로 교체하여 암호를 묻고 네트워크에 액세스하여 암호를 유출할 수 있어요. 이를 알 수 없어요.
- USB 드라이브를 부팅할 때 하드웨어를 신뢰해야 해요.

## 감사의 말

현재 지침은 다음 없이는 불가능했을 거예요. 

- [Ubuntu Full Disk Encryption Howto 2019(영문)](https://help.ubuntu.com/community/Full_Disk_Encryption_Howto_2019)
