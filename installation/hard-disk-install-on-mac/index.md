---
title: Mac 하드웨어에 칼리 리눅스 설치하기
description:
icon:
weight: 110
author: ["g0tmi1k",]
번역: ["xenix4845"]
---

{{% notice info %}}
중요! 최신 Mac 하드웨어(예: T2/M1 칩)는 리눅스가 전혀 또는 잘 작동하지 않아요. 이는 [리눅스 전반](https://github.com/Dunedan/mbp-2016-linux/)에 해당하며 칼리 리눅스에만 국한된 것이 아니에요.<br />
기기의 모델 및 연식에 따라 성공적인 경험이 달라질 수 있어요.<br />
{{% /notice %}}

Apple Mac 하드웨어(MacBook/MacBook Pro/MacBook Airs/iMacs/iMacs Pros/Mac Pro/Mac Minis 등)에 칼리 리눅스를 설치하는 것(단일 부팅)은 하드웨어가 지원된다면 간단할 수 있어요. 대부분의 경우 몇 가지 문제가 발생하므로 시행착오가 조금 필요해요.

이 가이드에서는 macOS/OS X를 칼리 리눅스로 대체하는 방법을 보여드릴게요. 하지만 macOS/OS X를 유지하고 싶다면 [이중 부팅](/docs/installation/dual-boot-kali-with-mac/) 가이드를 참조하세요.

예시에서는 macOS High Sierra(10.13)를 사용하는 Mac Mini(2011년 중반)에 칼리 리눅스를 설치할 거예요. 동일한 절차는 macOS Catalina(10.15)를 사용하는 Mac Book Air(2014년 초)에서도 테스트되었어요.

### 설치 전 준비사항

이 가이드는 다음을 가정해요:

- [칼리 리눅스 단일 부팅 설치 가이드](/docs/installation/hard-disk-install/)를 읽으셨어요. 이 가이드도 동일한 설치 전 준비사항(시스템 요구사항, 설정 가정 및 설치 미디어)을 가지고 있어요.
- 현재 macOS/OS X 10.7 이상을 실행 중이에요 _(최신 버전이 더 선호됨)_ 하드웨어가 인텔(PowerPC CPU가 아님)이라는 의미이기 때문이에요.
- Mac 하드웨어 모델 및 연식에 따라 다음을 발견할 수 있어요:
  - CD/DVD **또는** USB 드라이브를 사용하여 부팅할 때 **다른 결과**가 나타날 수 있어요.
    - **rEFInd**가 미리 설치되어 있으면 특히 오래된 하드웨어와 비EFI 장치에서 **USB 드라이브**로부터의 부팅 가능성을 높일 수 있어요.
  - 그래픽 설치 프로그램을 사용할 때 트랙패드가 작동하지 않을 수 있어요(하지만 칼리 리눅스가 설치된 후에는 작동해요).
  - 내장 무선 기능이 작동하지 않을 수 있는데, 이는 `firmware-b43-installer`가 [기본 이미지에 포함되어 있지 않기](https://gitlab.com/kalilinux/packages/kali-meta/-/commit/bdd4daa7be16e5114e21ade252638211e7d54813) 때문이에요.

{{% notice info %}}
하드 디스크의 기존 데이터를 모두 지울 것이므로 기기의 중요한 정보를 외부 매체에 백업하세요.
{{% /notice %}}

### 칼리 리눅스 설치 절차

1. 설치를 시작하려면 **칼리 리눅스 설치 미디어를 삽입**하고 **기기 전원을 켜세요**. 즉시 [부팅 메뉴가 표시될 때까지 **옵션(또는 Alt) ⌥** 키를 누르고 있으세요](https://support.apple.com/ko-kr/102603) (rEFInd가 설치된 경우 rEFInd, 그렇지 않으면 기본 macOS/OS X).
macOS/OS X 설정에 따라 **복구 HD**가 있을 수도 있고 없을 수도 있어요.

![](boot-mac.png)

- - -

2. 부팅 메뉴가 나타나면 모든 것이 예상대로 작동하는 경우 **두** 볼륨이 보여요:

- **EFI Boot** - **UEFI**를 지원하는 **최신 하드웨어**. **GUID 파티션 테이블(GPT)** 파티션이 사용되는 것이 일반적이에요.
- **Windows** - "비EFI" 부팅. **BIOS**를 사용하는 **오래된 하드웨어**에서 사용돼요. 여기서는 주로 **마스터 부트 레코드(MBR)** 파티션 테이블을 볼 수 있어요.

{{% notice info %}}
**하나의 볼륨**(EFI Boot)만 보이는 경우 설치 미디어가 이 기기에서 **지원되지 않아요**. 기기의 펌웨어 연식 때문일 수 있어요.<br />
**rEFInd**를 설치하고 다시 시도하는 것이 좋아요. rEFInd는 부트 매니저이기 때문이에요.
{{% /notice %}}

칼리 리눅스가 [데비안 기반](/docs/policy/kali-linux-relationship-with-debian/)임에도 불구하고, macOS/OS X는 항상 비EFI 부팅 미디어를 Windows로 감지해요. **EFI Boot** 볼륨을 선택하여 계속하는 것이 좋아요. 그러나 설치가 이 지점에서 멈추면 전원을 껐다 켜고 Windows(비EFI/BIOS인 칼리 리눅스)를 선택하세요. 성공 여부는 Mac 하드웨어의 모델과 연식에 따라 달라져요.

![](boot-mac-usb-efi.png)

### 칼리 리눅스 설치 절차

1. 이 시점부터 설치 절차는 [칼리 리눅스 하드 디스크 설치](/docs/installation/hard-disk-install/) 가이드와 동일해요.

2. 완료 후에는 재부팅하고 설치 미디어를 제거한 다음 칼리 리눅스를 즐기면 돼요.

### macOS/OS X 문제 해결

macOS/OS X에서 칼리 리눅스 설치에 문제가 있다면 다음과 같은 옵션을 시도해 볼 수 있어요:

- 최신 버전의 macOS/OS X([App store](https://support.apple.com/ko-kr/HT201541), [복구](https://support.apple.com/ko-kr/102655) 또는 [USB](https://support.apple.com/ko-kr/HT201372))를 설치하고 업데이트를 적용하여 펌웨어를 업그레이드해요.
- **rEFInd** 부트 매니저를 설치하여 기본 부트 매니저를 대체하세요.
- DVD를 사용하는 경우 드라이브가 회전을 멈춘 후 `ESC`를 눌러 **rEFInd를 새로고침**하세요.
- 칼리 리눅스를 부팅하려고 할 때 **EFI**에서 **BIOS 부팅**으로 전환하세요.
- **GPT** 드라이브에서 **하이브리드 MRB** 드라이브로 전환하세요 _(Live 이미지를 사용하면 도움이 될 수 있음)_.

### 설치 후 과정

이제 칼리 리눅스 설치를 완료했으니 시스템을 사용자 지정할 차례예요.

[일반 사용 섹션](/docs/general-use/)에서 더 많은 정보를 찾을 수 있으며, [사용자 포럼](https://forums.kali.org/)에서 칼리 리눅스를 최대한 활용하는 방법에 대한 팁도 찾을 수 있어요.
