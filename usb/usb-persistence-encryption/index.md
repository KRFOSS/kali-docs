---
title: 칼리 리눅스 라이브 USB 드라이브에 암호화된 영구 저장소 추가하기
description:
icon:
weight: 150
author: ["g0tmi1k",]
번역: ["xenix4845"]
---

이 워크숍에서는 USB 장치에서 칼리 리눅스를 부팅할 때 사용할 수 있는 다양한 기능을 살펴볼 거예요. 영구 저장소, LUKS 암호화된 영구 저장소 생성, 그리고 USB 드라이브의 "LUKS 날리기" 기능까지 알아볼 거예요. 기본 칼리 리눅스 ISO(1.0.7 버전 이상)는 USB 암호화된 영구 저장소를 지원해요.

{{% notice info %}}
이 페이지 전체에서 '/dev/sdX'를 사용하고 있지만, '/dev/sdX'는 실제 장치 라벨로 대체해야 해요. '/dev/sdX'는 실수로 덮어쓰는 것을 방지하기 위해 문서에 안전하게 사용될 수 있어요. 올바른 장치 라벨을 사용해주세요.
{{% /notice %}}

**0x01 - 칼리 ISO를 USB 드라이브(우리의 경우 /dev/sdX)에 이미징하는 것으로 시작하세요**. 완료되면 _parted /dev/sdX print_를 사용하여 USB 파티션 구조를 검사할 수 있어요:

{{% notice info %}}
쉽게 사용하려면 root 계정을 사용하세요. "sudo su"로 할 수 있어요.
{{% /notice %}}

```console
kali@kali:~$ dd if=kali-linux-2025.1-live-amd64.iso of=/dev/sdX conv=fsync bs=4M
```

**0x02 - USB 드라이브에 추가 파티션을 만들고 포맷하세요**. 우리 예제에서는 칼리 라이브 파티션 위의 빈 공간에 영구 저장소 파티션을 만들어요:

```console
kali@kali:~$ fdisk /dev/sdX <<< $(printf "n\np\n\n\n\nw")
```

fdisk가 완료되면 새 파티션이 `/dev/sdX3`에 생성되었을 거예요. `lsblk /dev/sdX` 명령으로 확인할 수 있어요.

**0x03 - LUKS로 파티션 암호화하기:**

```console
kali@kali:~$ cryptsetup --verbose --verify-passphrase luksFormat /dev/sdX3
```

**0x04 - 암호화된 파티션 열기:**

```console
kali@kali:~$ cryptsetup luksOpen /dev/sdX3 my_usb
```

**0x05 - ext4 파일시스템 생성 및 라벨 지정하기**:

```console
kali@kali:~$ mkfs.ext4 -L persistence /dev/mapper/my_usb
kali@kali:~$ e2label /dev/mapper/my_usb persistence
```

**0x06 - 파티션을 마운트하고 변경사항이 재부팅 시에도 유지되도록 persistence.conf 파일을 생성하세요:**

```console
kali@kali:~$ mkdir -p /mnt/my_usb
kali@kali:~$ mount /dev/mapper/my_usb /mnt/my_usb
kali@kali:~$ echo "/ union" | sudo tee /mnt/my_usb/persistence.conf
kali@kali:~$ umount /dev/mapper/my_usb
```

**0x07 - 암호화된 파티션 닫기:**

```console
kali@kali:~$ cryptsetup luksClose /dev/mapper/my_usb
```

이제 USB 드라이브를 꽂고 라이브 USB 암호화된 영구 저장소 모드로 재부팅할 준비가 되었어요.

## 여러 개의 영구 저장소

이 시점에서 다음과 같은 파티션 구조가 있을 거예요:

```console
kali@kali:~$ parted /dev/sdX print
```

USB 드라이브에 암호화되거나 암호화되지 않은 추가 영구 저장소를 추가할 수 있으며, 부팅 시 원하는 영구 저장소를 선택할 수 있어요. 암호화되지 않은 저장소를 하나 더 만들어볼게요. "work"라는 이름으로 라벨을 지정할 거예요.

**0x01 - "work" 데이터를 저장할 추가 4번 파티션을 만드세요**. 5GB 공간을 더 할당할 거예요:

```console
kali@kali:~$ parted /dev/sdX
GNU Parted 2.3
Using /dev/sdX
Welcome to GNU Parted! Type 'help' to view a list of commands.
(parted) print
Model: SanDisk SanDisk Ultra (scsi)
Disk /dev/sdX: 31.6GB
Sector size (logical/physical): 512B/512B
Partition Table: msdos

Number  Start   End     Size    Type     File system  Flags
 1      32.8kB  2988MB  2988MB  primary               boot, hidden
 2      2988MB  3050MB  64.9MB  primary  fat16
 3      3050MB  10.0GB  6947MB  primary

(parted) mkpart primary 10000 15000
(parted) quit
Information: You may need to update /etc/fstab.
```

**0x02 - 네 번째 파티션을 포맷하고 "work"라는 라벨을 지정하세요**:

```console
kali@kali:~$ mkfs.ext4 /dev/sdX4
kali@kali:~$ e2label /dev/sdX4 work
```

**0x03 - 새 파티션을 마운트하고 persistence.conf 파일을 생성하세요:**

```console
kali@kali:~$ mkdir -p /mnt/usb
kali@kali:~$ mount /dev/sdX4 /mnt/usb
kali@kali:~$ echo "/ union" > /mnt/usb/persistence.conf
kali@kali:~$ umount /mnt/usb
```

컴퓨터를 재부팅하고 USB에서 부팅하도록 설정하세요. 부팅 메뉴가 나타나면 persistence-label 매개변수를 편집하여 원하는 영구 저장소를 가리키도록 하세요!

## 칼리에서 데이터 긴급 자체 파괴

침투 테스터로서, 우리는 종종 노트북에 저장된 민감한 데이터를 가지고 여행해야 해요. 물론 가능한 모든 곳에서 전체 디스크 암호화를 사용하며, 여기에는 가장 민감한 자료를 포함하는 칼리 리눅스 기기도 포함돼요. 안전 조치로 파괴용 비밀번호를 설정해봅시다:

```console
kali@kali:~$ sudo apt install -y cryptsetup-nuke-password
kali@kali:~$ dpkg-reconfigure cryptsetup-nuke-password
```

구성된 파괴용 비밀번호는 initrd에 저장되며 부팅 시 잠금 해제할 수 있는 모든 암호화된 파티션에서 사용할 수 있어요.

**LUKS 키 슬롯을 백업하고 암호화하세요:**

```console
kali@kali:~$ cryptsetup luksHeaderBackup --header-backup-file luksheader.back /dev/sdX3
kali@kali:~$ openssl enc -e -aes-256-cbc -in luksheader.back -out luksheader.back.enc
```

이제 암호화된 저장소로 부팅하고, 실제 암호 해제 비밀번호 대신 파괴용 비밀번호를 입력하세요. 이렇게 하면 암호화된 저장소의 모든 정보가 쓸모없게 되요. 이 작업이 완료되면 데이터에 접근할 수 없는지 확인하세요.

**이제 데이터를 복원해볼게요**. LUKS 키 슬롯의 백업을 복호화하고 암호화된 파티션에 복원할 거예요:

```console
kali@kali:~$ openssl enc -d -aes-256-cbc -in luksheader.back.enc -out luksheader.back
kali@kali:~$ cryptsetup luksHeaderRestore --header-backup-file luksheader.back /dev/sdX3
```

이제 슬롯이 복원되었어요. 이제 재부팅하고 일반 LUKS 비밀번호를 입력하면 시스템이 원래 상태로 돌아와요.
