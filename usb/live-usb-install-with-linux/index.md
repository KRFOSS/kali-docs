---
title: Linux에서 칼리 부팅 USB 만들기
description:
icon:
weight: 50
author: ["g0tmi1k",]
번역: ["xenix4845"]
---

<!-- USB Stick, USB Drive, USB Disk, USB Thumb -->

칼리 리눅스를 시작하는 가장 빠른 방법이자 저희가 가장 좋아하는 방법은 USB 드라이브에서 "라이브"로 실행하는 거예요. 이 방법에는 여러 장점이 있어요:

- 비파괴적이에요 - 호스트 시스템의 하드 드라이브나 설치된 OS를 전혀 변경하지 않고, 다시 일반 모드로 돌아가려면 "칼리 라이브" USB 드라이브를 제거하고 시스템을 재시작하면 돼요.
- 휴대성이 좋아요 - 칼리 리눅스를 주머니에 넣고 다니다가 사용 가능한 시스템에서 몇 분 안에 실행할 수 있어요.
- 맞춤 설정이 가능해요 - [자신만의 맞춤형 칼리 리눅스 ISO 이미지](/docs/development/live-build-a-custom-kali-iso/)를 만들고 같은 방법으로 USB 드라이브에 넣을 수 있어요.
- 영구 저장이 가능해요 - 약간의 추가 작업으로 칼리 리눅스 "라이브" USB 드라이브에 [영구 저장소](/docs/usb/usb-persistence/)를 설정하면 수집한 데이터가 재부팅 후에도 저장돼요.

이를 위해서는 먼저 칼리 리눅스의 ISO 이미지로 설정된 부팅 가능한 USB 드라이브를 만들어야 해요.

### 필요한 것들

1. 실행할 시스템에 맞는 최신 칼리 빌드 이미지의 _검증된_ ISO 이미지 복사본.
  - 자세한 내용은 [공식 칼리 리눅스 이미지 다운로드](/docs/introduction/download-official-kali-linux-images/)를 확인하세요.

2. Linux를 사용 중이라면 종종 미리 설치되어 있는 `dd` 명령어를 사용하거나 [Etcher](https://www.balena.io/etcher/)를 사용할 수 있어요.

3. 4GB 이상의 USB 메모리.
  - SD 카드 슬롯이 있는 시스템은 비슷한 용량의 SD 카드를 사용할 수 있어요. 절차는 동일해요.

### 칼리 리눅스 라이브 USB 설치 절차

이 절차의 세부 사항은 [Windows](/docs/usb/live-usb-install-with-windows/), [Linux](/docs/usb/live-usb-install-with-linux/), 또는 [macOS/OS X](/docs/usb/live-usb-install-with-mac/) 시스템에서 하는지에 따라 달라져요.

- - -

## Linux에서 부팅 가능한 칼리 USB 드라이브 만들기 (DD)

Linux 환경에서 부팅 가능한 칼리 리눅스 USB 드라이브를 만드는 건 간단해요.
[칼리 ISO 파일을 다운로드하고 검증](/docs/introduction/download-official-kali-linux-images/)한 후, 다음 절차에 따라 `dd` 명령어를 사용해서 USB 드라이브로 복사할 수 있어요.

root 권한으로 실행하거나 sudo로 `dd` 명령어를 실행해야 한다는 점 참고하세요. 다음 예시는 Linux Mint 호스트를 가정해요 - 사용 중인 배포판에 따라 몇 가지 세부 사항이 약간 다를 수 있지만, 전반적인 아이디어는 매우 비슷할 거예요. [Etcher](#linux에서-부팅-가능한-칼리-usb-드라이브-만들기-etcher)를 사용하고 싶다면 Windows 사용자와 같은 방법을 따르세요. USB 드라이브는 /dev/sdb와 비슷한 경로를 가질 거예요.

{{% notice info %}}
경고: 무엇을 하는지 이해하지 못하거나 잘못된 출력 경로를 지정하면 의도하지 않은 디스크 드라이브를 `dd`로 쉽게 덮어쓸 수 있어요. 실행하기 전에 다시 한 번 확인하세요. 나중에는 너무 늦어요.

**경고를 진지하게 받아들이세요**.
{{% /notice %}}

1. 먼저 USB 드라이브에 이미지를 쓰기 위한 장치 경로를 확인해야 해요. USB 드라이브를 포트에 꽂지 **_않은 상태_**에서 터미널 창의 명령 프롬프트에서 `sudo fdisk -l` 명령을 실행하세요 (fdisk에 관리자 권한을 사용하지 않으면 출력이 나오지 않아요). 다음과 (_정확히 같지는 않지만_) 비슷한 출력이 나올 거예요. 드라이브들 - "/dev/sdX" - 과 그 파티션들 - /dev/sdX\[1-9\]을 보여줘요. 여기서는 세 개의 파티션을 가진 단일 드라이브 /dev/sda만 있어요:

```console
user@mint:~$ sudo fdisk -l
Disk /dev/sda: 2.73 TiB, 3000592982016 bytes, 5860533168 sectors
Disk model: WDC WD30EZRX-00D
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 4096 bytes
I/O size (minimum/optimal): 4096 bytes / 4096 bytes
Disklabel type: gpt
Disk identifier: 58D184D9-8780-4B6A-80F7-E1500B95A2AB

Device          Start        End    Sectors  Size Type
/dev/sda1        2048    1050623    1048576  512M EFI System
/dev/sda2     1050624 5858531327 5857480704  2.7T Linux filesystem
/dev/sda3  5858531328 5860532223    2000896  977M Linux swap
user@mint:~$
```

- - -

2. USB 드라이브를 시스템에 꽂고 같은 명령 "`sudo fdisk -l`"을 두 번째로 실행하세요. 출력은 다음과 (다시 한번, _정확히 같지는 않지만_) 비슷하게 나올 거예요. 이전에 없었던 추가 장치가 표시될 거예요. USB 드라이브의 경로는 대체로 목록의 마지막에 있을 거예요. 어쨌든, 이전에 없던 것 중 하나일 거예요.
예시에서는 이전에 없었던 **/dev/sdb**가 새로 생긴 것을 볼 수 있어요. 64GB USB 드라이브예요:

```console
user@mint:~$ sudo fdisk -l
Disk /dev/sda: 2.73 TiB, 3000592982016 bytes, 5860533168 sectors
Disk model: WDC WD30EZRX-00D
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 4096 bytes
I/O size (minimum/optimal): 4096 bytes / 4096 bytes
Disklabel type: gpt
Disk identifier: 58D184D9-8780-4B6A-80F7-E1500B95A2AB

Device          Start        End    Sectors  Size Type
/dev/sda1        2048    1050623    1048576  512M EFI System
/dev/sda2     1050624 5858531327 5857480704  2.7T Linux filesystem
/dev/sda3  5858531328 5860532223    2000896  977M Linux swap


Disk /dev/sdb: 58.44 GiB, 62746787840 bytes, 122552320 sectors
Disk model: Extreme
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: gpt
Disk identifier: 1D24569E-BDCD-4912-9689-8BFC38EFB130

Device      Start       End   Sectors  Size Type
/dev/sdb1      40    409639    409600  200M EFI System
/dev/sdb2  411648 122550271 122138624 58.2G Microsoft basic data
user@mint:~$
```

- - -

3. (조심스럽게!) USB 장치에 칼리 ISO 파일을 이미징하세요. 작성하고 있는 ISO 이미지의 이름이 "kali-linux-2025.2-live-amd64.iso"이고 현재 작업 디렉토리에 있다고 가정할 거예요.

```console
user@mint:~$ file kali-linux-2025.2-live-amd64.iso
kali-linux-2025.2-live-amd64.iso: ISO 9660 CD-ROM filesystem data (DOS/MBR boot sector) 'Kali Linux amd64' (bootable)
user@mint:~$
```

DD의 블록크기 매개변수는 증가할 수 있으며, dd 명령어의 작업 속도를 높일 수 있지만, 시스템과 여러 다른 요인에 따라 때때로 부팅할 수 없는 USB 드라이브를 생성할 수 있어요. 권장 값인 "bs=4M"은 보수적이고 신뢰할 수 있어요.

추가로, "conv=fsync" 매개변수는 명령이 반환되기 전에 데이터가 물리적으로 USB 드라이브에 기록되도록 해줘요.

{{% notice info %}}
명령어에서 '`/dev/sdX`'를 사용하지만, 이 '`/dev/sdX`'는 이전에 발견한 드라이브로 대체해야 해요.

**이전 단계에서 올바른 장치 이름을 사용하세요**.
{{% /notice %}}

시스템의 나이에 따라 DD 버전이 상태 업데이트/피드백을 제공할 수 있어요 (2017년 이후):

이를 위해 간단히 `status` 플래그를 추가해요:

```console
user@mint:~$ sudo dd if=kali-linux-2025.2-live-amd64.iso of=/dev/sdX conv=fsync bs=4M status=progress
```

- - -

그렇지 않으면, 구형 시스템의 경우:

```console
user@mint:~$ sudo dd if=kali-linux-2025.2-live-amd64.iso of=/dev/sdX conv=fsync bs=4M
```

USB 드라이브 이미징은 시간이 꽤 걸릴 수 있어요. 아래 샘플 출력에서 볼 수 있듯이, 10분 이상 걸리는 것은 드문 일이 아니니 참을성을 가지세요!

이 `dd` 명령어 사용법은 완료될 때까지 아무런 피드백을 제공하지 않지만, 드라이브에 접근 표시기가 있다면 가끔씩 깜빡이는 걸 볼 수 있을 거예요. 이미지를 `dd`로 복사하는 시간은 사용된 시스템의 속도, USB 드라이브 자체, 그리고 삽입된 USB 포트에 따라 달라져요. `dd`가 드라이브 이미징을 마치면 다음과 같은 출력을 보여줘요:

```plaintext
893+1 records in
893+1 records out
3748147200 bytes (3.7 GB, 3.5 GiB) copied, 998.442 s, 3.8 MB/s
```

- - -

이게 다예요, 정말로!

## Linux에서 부팅 가능한 칼리 USB 드라이브 만들기 (Etcher)

플래시를 위한 그래픽 옵션을 원한다면 [Etcher](https://www.balena.io/etcher/)를 권장해요.

1. Etcher를 다운로드하고 실행해요.
2. "이미지 선택"으로 이미징할 칼리 리눅스 ISO 파일을 선택하고 덮어쓸 USB 드라이브가 올바른 것인지 확인해요. 준비되면 "Flash!" 버튼을 클릭해요.
![](kali-usb-install-windows.png)
3. Etcher가 이미지가 플래시되었다고 알리면 USB 드라이브를 안전하게 제거할 수 있어요.

이제 USB 장치를 사용하여 칼리 라이브/설치 환경으로 부팅할 수 있어요.
3. Etcher가 이미지가 플래시되었다고 알리면 USB 드라이브를 안전하게 제거할 수 있어요.

이제 USB 장치를 사용하여 칼리 라이브/설치 환경으로 부팅할 수 있어요.
