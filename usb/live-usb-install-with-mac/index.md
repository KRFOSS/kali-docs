---
title: Mac에서 칼리 부팅 USB 만들기
description:
icon:
weight: 50
author: ["g0tmi1k",]
번역: ["xenix4845"]
---

칼리 리눅스를 시작하는 가장 빠른 방법이자 저희가 가장 좋아하는 방법은 USB 드라이브에서 "라이브"로 실행하는 거예요. 이 방법에는 여러 장점이 있어요:

- 비파괴적이에요 - 호스트 시스템의 하드 드라이브나 설치된 OS를 전혀 변경하지 않고, 다시 일반 모드로 돌아가려면 "칼리 라이브" USB 드라이브를 제거하고 시스템을 재시작하면 돼요.
- 휴대성이 좋아요 - 칼리 리눅스를 주머니에 넣고 다니다가 사용 가능한 시스템에서 몇 분 안에 실행할 수 있어요.
- 맞춤 설정이 가능해요 - [자신만의 맞춤형 칼리 리눅스 ISO 이미지](/docs/development/live-build-a-custom-kali-iso/)를 만들고 같은 방법으로 USB 드라이브에 넣을 수 있어요.
- 영구 저장이 가능해요 - 약간의 추가 작업으로 칼리 리눅스 "라이브" USB 드라이브에 [영구 저장소](/docs/usb/usb-persistence/)를 설정하면 수집한 데이터가 재부팅 후에도 저장돼요.

이를 위해서는 먼저 칼리 리눅스의 ISO 이미지로 설정된 부팅 가능한 USB 드라이브를 만들어야 해요.

### 필요한 것들

1. 실행할 시스템에 맞는 최신 칼리 빌드 이미지의 _검증된_ ISO 이미지 복사본.
  - 자세한 내용은 [공식 칼리 리눅스 이미지 다운로드](/docs/introduction/download-official-kali-linux-images/)를 확인하세요.

2. macOS/OS X를 사용 중이라면 미리 설치된 `dd` 명령어를 사용하거나 [Etcher](https://www.balena.io/etcher/)를 사용할 수 있어요.

3. 4GB 이상의 USB 메모리.
  - SD 카드 슬롯이 있는 시스템은 비슷한 용량의 SD 카드를 사용할 수 있어요. 절차는 동일해요.

### 칼리 리눅스 라이브 USB 설치 절차

이 절차의 세부 사항은 [Windows](/docs/usb/live-usb-install-with-windows/), [Linux](/docs/usb/live-usb-install-with-linux/), 또는 [macOS/OS X](/docs/usb/live-usb-install-with-mac/) 시스템에서 하는지에 따라 달라져요.

- - -

## macOS/OS X에서 부팅 가능한 칼리 USB 드라이브 만들기 (DD)

macOS/OS X는 UNIX를 기반으로 하기 때문에, macOS/OS X 환경에서 부팅 가능한 칼리 리눅스 USB 드라이브를 만드는 것은 Linux에서 하는 것과 비슷해요.
[칼리 ISO 파일을 다운로드하고 검증](/docs/introduction/download-official-kali-linux-images/)한 후, `dd` 명령어를 사용해서 USB 드라이브로 복사해요.

[Etcher](#macosos-x에서-부팅-가능한-칼리-usb-드라이브-만들기-etcher)를 사용하고 싶다면 Windows 사용자와 같은 방법을 따르면 돼요. USB 드라이브는 /dev/disk2와 같은 경로를 가질 거예요.

{{% notice info %}}
경고: 무엇을 하는지 이해하지 못하거나 잘못된 출력 경로를 지정하면 의도하지 않은 디스크 드라이브를 `dd`로 쉽게 덮어쓸 수 있어요. 실행하기 전에 다시 한 번 확인하세요. 나중에는 너무 늦어요.

**경고를 진지하게 받아들이세요**.
{{% /notice %}}

1. USB 드라이브를 시스템에 꽂지 **_않은 상태_**에서 터미널 창을 열고 명령 프롬프트에 `diskutil list` 명령어를 입력하세요.
시스템에 마운트된 디스크의 장치 경로(**/dev/disk0**, **/dev/disk1** 등과 같은)와 각 디스크의 파티션에 대한 정보가 함께 나열된 목록이 나타날 거예요.

```console
user@mbp ~ % diskutil list
/dev/disk0 (internal, physical):
   #:                       TYPE NAME                    SIZE       IDENTIFIER
   0:      GUID_partition_scheme                        *1.0 TB     disk0
   1:             Apple_APFS_ISC Container disk1         524.3 MB   disk0s1
   2:                 Apple_APFS Container disk3         994.7 GB   disk0s2
   3:        Apple_APFS_Recovery Container disk2         5.4 GB     disk0s3

/dev/disk3 (synthesized):
   #:                       TYPE NAME                    SIZE       IDENTIFIER
   0:      APFS Container Scheme -                      +994.7 GB   disk3
                                 Physical Store disk0s2
   1:                APFS Volume macOS - Data            547.8 GB   disk3s1
   2:                APFS Volume macOS                   11.2 GB    disk3s3
   3:              APFS Snapshot com.apple.os.update-... 11.2 GB    disk3s3s1
   4:                APFS Volume Preboot                 7.2 GB     disk3s4
   5:                APFS Volume Recovery                1.0 GB     disk3s5
   6:                APFS Volume VM                      3.2 GB     disk3s6

user@mbp ~ %
```

- - -

2. USB 드라이브를 Apple 컴퓨터의 USB 포트에 연결하세요.
macOS/OS X 버전과 USB에 이전에 있던 내용에 따라 일련의 프롬프트가 나타날 수 있어요.

(기기를 신뢰한다면) "허용"을 눌러주세요:

![](mac-usb-permission.png)

USB에 이전에 있던 데이터로 인해 macOS/OS X가 자동으로 마운트할 수 없어요.
이미 데이터를 백업했으므로 걱정하지 않고 "무시"를 선택할 거예요:

![](mac-usb-unreadable.png)

- - -

3. 이제 같은 명령어인 "`diskutil list`"를 두 번째로 실행하세요. 출력은 다음과 비슷하게 (정확히는 아니지만) 나타날 거예요. 이전에 없었던 추가 기기가 표시될 거예요. USB 드라이브의 경로는 대체로 목록의 마지막에 있을 거예요. 어쨌든, 이전에 없던 것 중 하나일 거예요.
예시에서는 이전에 없었던 **/dev/disk4**가 새로 생긴 것을 볼 수 있어요. 64GB USB 드라이브예요:

```console
user@mbp ~ % diskutil list
/dev/disk0 (internal, physical):
   #:                       TYPE NAME                    SIZE       IDENTIFIER
   0:      GUID_partition_scheme                        *1.0 TB     disk0
   1:             Apple_APFS_ISC Container disk1         524.3 MB   disk0s1
   2:                 Apple_APFS Container disk3         994.7 GB   disk0s2
   3:        Apple_APFS_Recovery Container disk2         5.4 GB     disk0s3

/dev/disk3 (synthesized):
   #:                       TYPE NAME                    SIZE       IDENTIFIER
   0:      APFS Container Scheme -                      +994.7 GB   disk3
                                 Physical Store disk0s2
   1:                APFS Volume macOS - Data            547.9 GB   disk3s1
   2:                APFS Volume macOS                   11.2 GB    disk3s3
   3:              APFS Snapshot com.apple.os.update-... 11.2 GB    disk3s3s1
   4:                APFS Volume Preboot                 7.2 GB     disk3s4
   5:                APFS Volume Recovery                1.0 GB     disk3s5
   6:                APFS Volume VM                      3.2 GB     disk3s6

/dev/disk4 (external, physical):
   #:                       TYPE NAME                    SIZE       IDENTIFIER
   0:      GUID_partition_scheme                        *62.7 GB    disk4
   1:       Microsoft Basic Data                         62.7 GB    disk4s1

user@mbp ~ %
```

- - -

4. 드라이브를 언마운트하세요 (이 예시에서 USB 드라이브는 **/dev/disk4**로 가정해요 - _이것을 **단순히** 복사하지 마시고, 여러분 시스템에서 올바른 경로를 직접 확인하세요!_):

```console
user@mbp ~ % diskutil unmountDisk /dev/disk4
Unmount of all volumes on disk4 was successful
user@mbp ~ %
```

- - -

5. (조심스럽게!) USB 장치에 칼리 ISO 파일을 이미징하세요. 작성하고 있는 ISO 이미지의 이름이 "kali-linux-2025.1-live-amd64.iso"이고 현재 작업 디렉토리에 있다고 가정할 거예요.

```console
user@mbp ~ % file kali-linux-2025.1-live-amd64.iso
kali-linux-2025.1-live-amd64.iso: ISO 9660 CD-ROM filesystem data (DOS/MBR boot sector) 'Kali Linux amd64' (bootable)
user@mbp ~ %
```

DD의 블록크기 매개변수는 증가할 수 있으며, dd 명령어의 작업 속도를 높일 수 있지만, 시스템과 여러 다른 요인에 따라 때때로 부팅할 수 없는 USB 드라이브를 생성할 수 있어요. 권장 값인 "bs=4M"은 보수적이고 신뢰할 수 있어요.

{{% notice info %}}
명령어에서 '`/dev/diskX`'를 사용하지만, '`/dev/diskX`'는 이전에 발견된 드라이브로 바꿔야 해요.

**이전 단계에서 올바른 기기 이름을 사용하세요**.
{{% /notice %}}

**쓰기 속도를 향상시키기 위해** "/dev/diskX"를 "/dev/**r**diskX" _(추가 `r`)_ 로 바꿀 거예요.

```console
$ sudo dd if=kali-linux-2025.1-live-amd64.iso of=/dev/rdiskX bs=4M status=progress
```

{{% notice info %}}
위 명령어를 실행할 때 다음과 같은 오류가 나타날 수 있어요: `dd: invalid number: '4M'` 또는 `dd: bs: illegal numeric value`.

이 경우, `4M`을 `4m`으로 변경하세요.
또한 블록크기(bs)를 증가시키면 쓰기 진행 속도가 빨라지지만, 불량 USB 드라이브가 생길 가능성도 높아져요. macOS/OS X에서 주어진 값을 사용하면 일관되게 신뢰할 수 있는 이미지가 만들어져요.

또 다른 잠재적 오류는 `status=progress`가 macOS 버전에서 작동하지 않는 경우예요. 이 경우, 이 부분을 제거하고 대신 `CTRL+T`를 사용하여 상태를 측정하세요.
{{% /notice %}}

USB 드라이브 이미징은 시간이 꽤 걸릴 수 있어요. 아래 샘플 출력에서 볼 수 있듯이, 30분 이상 걸리는 것도 드문 일이 아니니 참을성을 가지세요!

dd 명령어는 `status=progress`가 사용된 경우에만 쓰기 과정에서 피드백을 제공하며, 그렇지 않으면 완료될 때만 표시되지만, 드라이브에 접근 표시기가 있다면 가끔 깜박이는 것을 볼 수 있을 거예요. 이미지를 `dd`로 복사하는 시간은 사용된 시스템의 속도, USB 드라이브 자체, 그리고 삽입된 USB 포트에 따라 달라져요. dd가 드라이브 이미징을 마치면, 다음과 같은 출력이 나타날 거예요:

```plaintext
893+1 records in
893+1 records out
3748147200 bytes transferred in 915.043994 secs (4096139 bytes/sec)
```

DD가 완료된 후, macOS/OS X가 USB 기기를 다시 마운트하려고 시도할 수 있어요. 그렇다면 이전과 같은 팝업을 받게 될 거예요.

![](mac-usb-unreadable.png)

이번에는 기본 옵션인 "꺼내기"를 제안해요.

- - -

끝났어요!

## macOS/OS X에서 부팅 가능한 칼리 USB 드라이브 만들기 (Etcher)

플래시를 위한 그래픽 옵션을 원한다면 [Etcher](https://www.balena.io/etcher/)를 권장해요.

1. Etcher를 다운로드하고 실행하세요.

2. "이미지 선택"으로 이미징할 칼리 리눅스 ISO 파일을 선택하고 덮어쓸 USB 드라이브가 올바른 것인지 확인하세요. 준비되면 "Flash!" 버튼을 클릭하세요.

![](kali-usb-install-windows.png)

3. Etcher가 이미지가 플래시되었다고 알리면 USB 드라이브를 안전하게 제거할 수 있어요.

이제 USB 장치를 사용하여 칼리 라이브/설치 환경으로 부팅할 수 있어요.

## Apple에서 USB 부팅하기

macOS/OS X 시스템에서 다른 드라이브로 부팅하려면, 기기 전원을 켠 직후 **Option** 키를 눌러 부팅 메뉴를 불러오고 사용하려는 드라이브를 선택하세요.

자세한 내용은 [Apple의 지식 베이스](https://support.apple.com/kb/ht1310)를 참조하세요.
