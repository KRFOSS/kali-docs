---
title: Linux에서 칼리 부팅 USB 만들기
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

## 필요한 것들

1. 실행할 시스템에 맞는 최신 칼리 빌드 이미지의 _검증된_ ISO 이미지 복사본: [공식 칼리 리눅스 이미지 다운로드](/docs/introduction/download-official-kali-linux-images/) 자세한 내용을 확인하세요.

2. Linux를 사용 중이라면 이미 설치되어 있는 `dd` 명령어를 사용하거나 [Etcher](https://www.balena.io/etcher/)를 사용할 수 있어요.

3. 4GB 이상의 USB 메모리. (SD 카드 슬롯이 있는 시스템은 비슷한 용량의 SD 카드를 사용할 수 있어요. 절차는 동일해요.)

## 칼리 리눅스 라이브 USB 설치 절차

이 절차의 세부 사항은 [Windows](/docs/usb/live-usb-install-with-windows/), [Linux](/docs/usb/live-usb-install-with-linux/), 또는 [macOS/OS X](/docs/usb/live-usb-install-with-mac/) 시스템에서 하는지에 따라 달라져요.

#### Linux에서 부팅 가능한 칼리 USB 드라이브 만들기 (DD)

Linux 환경에서 부팅 가능한 칼리 리눅스 USB 드라이브를 만드는 건 쉬워요. 칼리 ISO 파일을 다운로드하고 확인한 후, `dd` 명령어를 사용해서 다음 절차에 따라 USB 드라이브로 복사할 수 있어요. root 권한으로 실행하거나 sudo로 `dd` 명령어를 실행해야 한다는 점 참고하세요. 다음 예시는 Linux Mint 17.1 데스크톱을 가정해요 - 사용 중인 배포판에 따라 몇 가지 세부 사항이 약간 다를 수 있지만, 전반적인 아이디어는 매우 비슷할 거예요. Etcher를 사용하고 싶다면 Windows 사용자와 같은 방법을 따르세요. USB 드라이브는 /dev/sdb와 비슷한 경로를 가질 거예요.

{{% notice info %}}
경고: 칼리 리눅스를 USB 드라이브로 이미징하는 과정은 매우 쉽지만, 무엇을 하는지 이해하지 못하거나 잘못된 출력 경로를 지정하면 의도하지 않은 디스크 드라이브를 dd로 덮어쓸 수 있어요. 실행하기 전에 다시 한 번 확인하세요.

복수난수(覆水難收) "엎질러진 물은 다시 담을 수 없다"
{{% /notice %}}

1. 먼저 USB 드라이브에 이미지를 쓰기 위한 장치 경로를 확인해야 해요. USB 드라이브를 꽂지 **_않은 상태_**에서 터미널 창의 명령 프롬프트에서 `sudo fdisk -l` 명령을 실행하세요 (fdisk에 관리자 권한을 사용하지 않으면 출력이 나오지 않아요). 다음과 (_정확히 같지는 않지만_) 비슷한 출력이 나올 거예요. 세 개의 파티션(/dev/sda1, /dev/sda2, /dev/sda5)을 포함한 단일 드라이브 "/dev/sda"를 보여줘요:

![](Parallels-DesktopScreenSnapz007.png)
2. 이제 USB 드라이브를 시스템의 사용 가능한 USB 포트에 꽂고 같은 명령 "sudo fdisk -l"을 다시 한번 실행하세요. 이번에는 출력이 (다시 한번, _정확히 같지는 않지만_) 이런 식으로 나올 거예요. 이 예시에서는 이전에 없던 추가 장치 "/dev/sdb", 16GB USB 드라이브가 보여요:

![](FinderScreenSnapz002.png)
3. (조심스럽게!) USB 장치에 칼리 ISO 파일을 이미징해요. 아래 예시 명령은 작성하는 ISO 이미지의 이름이 "kali-linux-2025.1-live-amd64.iso"이고 현재 작업 디렉토리에 있다고 가정해요. 블록 크기 매개변수를 증가시킬 수 있고, dd 명령의 작업 속도를 높일 수 있지만, 시스템과 많은 다른 요소들에 따라 때때로 부팅할 수 없는 USB 드라이브가 생길 수 있어요. 권장 값인 "bs=4M"은 안전하고 신뢰할 수 있어요. 추가로, "conv=fsync" 매개변수는 명령이 반환되기 전에 데이터가 물리적으로 USB 드라이브에 기록되도록 해줘요:

{{% notice info %}}
명령어에서 '/dev/sdX'를 사용하지만, 이 '/dev/sdX'는 이전에 발견한 드라이브로 대체해야 해요. '/dev/sdX'는 어떤 장치도 덮어쓰지 않으며, 실수로 덮어쓰는 것을 방지하기 위해 문서에 안전하게 사용될 수 있어요. 이전 단계에서 확인한 올바른 장치 이름을 사용해주세요.
{{% /notice %}}

```console
kali@kali:~$ dd if=kali-linux-2025.1-live-amd64.iso of=/dev/sdX conv=fsync bs=4M
```

USB 드라이브 이미징은 시간이 꽤 걸릴 수 있어요. 아래 샘플 출력에서 볼 수 있듯이, 10분 이상 걸리는 것은 드문 일이 아니에요. 참을성을 가지세요!

`dd` 명령은 완료될 때까지 아무런 피드백을 제공하지 않지만, 드라이브에 접근 표시기가 있다면 가끔씩 깜빡이는 걸 볼 수 있을 거예요. 이미지를 `dd`로 복사하는 시간은 사용된 시스템의 속도, USB 드라이브 자체, 그리고 삽입된 USB 포트에 따라 달라져요. `dd`가 드라이브 이미징을 마치면 다음과 같은 출력을 보여줘요:

```plaintext
893+1 records in
893+1 records out
3748147200 bytes (3.7 GB, 3.5 GiB) copied, 998.442 s, 3.8 MB/s
```

이게 다예요, 정말로!

- - -

#### Linux에서 부팅 가능한 칼리 USB 드라이브 만들기 (상태 표시가 있는 DD)

또는 이미징을 위한 몇 가지 다른 옵션이 있어요.

첫 번째 옵션은 상태 표시기가 있는 `dd`예요. 하지만 이것은 최신 시스템에서만 사용 가능해요. 이를 위해 간단히 `status` 플래그를 추가해요:

{{% notice info %}}
명령어에서 '/dev/sdX'를 사용하지만, 이 '/dev/sdX'는 적절한 장치 라벨로 대체해야 해요. '/dev/sdX'는 어떤 장치도 덮어쓰지 않으며, 실수로 덮어쓰는 것을 방지하기 위해 문서에 안전하게 사용될 수 있어요. 올바른 장치 라벨을 사용해주세요.
{{% /notice %}}

```console
kali@kali:~$ dd if=kali-linux-2025.1-live-amd64.iso of=/dev/sdX conv=fsync bs=4M status=progress
```

또 다른 옵션은 `pv`를 사용하는 거예요. 대략적인 타이머를 얻기 위해 여기서도 `size` 플래그를 사용할 수 있어요. 사용 중인 이미지에 따라 크기를 변경하세요:

```console
kali@kali:~$ dd if=kali-linux-2025.1-live-amd64.iso | pv -s 2.8G | dd of=/dev/sdX conv=fsync bs=4M
```

#### Linux에서 부팅 가능한 칼리 USB 드라이브 만들기 (Etcher)

세 번째는 [Etcher](https://www.balena.io/etcher/)예요.

1. Etcher를 다운로드하고 실행해요.

2. "이미지 선택"으로 이미징할 칼리 리눅스 ISO 파일을 선택하고 덮어쓸 USB 드라이브가 올바른 것인지 확인해요. 준비되면 "Flash!" 버튼을 클릭해요.

![](kali-usb-install-windows.png)
3. Etcher가 이미지가 플래시되었다고 알리면 USB 드라이브를 안전하게 제거할 수 있어요.

이제 USB 장치를 사용하여 칼리 라이브/설치 환경으로 부팅할 수 있어요.
