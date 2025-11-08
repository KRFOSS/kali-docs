---
title: 커널 구성
description:
icon:
weight:
author: ["arnaudr", "gamb1t",]
번역: ["xenix4845"]
---

칼리 리눅스 커널은 "일반적인" 커널과 약간 다르게 구성되어 있어요. 침투 테스트 목적으로, 우리는 범용 Linux 배포판과 다른 일부 기본값을 사용하기로 결정했으며, 또한 여기저기 커널에 패치를 적용했어요. 이 페이지에서는 이러한 변경 사항들을 설명할 거예요.

{{% notice info %}}
기본값은 `kali-tweaks` 도구를 통해 수정할 수 있지만, 패치는 새 커널을 생성하지 않으면 되돌릴 수 없어요.
{{% /notice %}}

각 헤더 옆에는 해당 구성이 커널 패치인지 또는 [sysctl](https://en.wikipedia.org/wiki/Sysctl)에 의해 동작이 수정되었는지 표시되어 있어요. 이러한 커널 패치를 직접 탐색하고 싶다면, [여기, 우리의 GitLab](https://gitlab.com/kalilinux/packages/linux/-/blob/kali/master/debian/patches/series#L38)에서 찾을 수 있어요.

### WiFi 인젝션 (패치)

WiFi 기반 침투 테스트를 돕기 위해 [WiFi 인젝션 패치](https://gitlab.com/kalilinux/packages/linux/-/blob/kali/master/debian/patches/features/all/kali-wifi-injection.patch)를 제공해요.

### dmesg 제한 없음 (sysctl)

커널 로그는 기본적으로 제한이 없어서, 권한이 없는 사용자도 `dmesg` 명령을 실행하여 커널 로그(또는 "커널 링 버퍼"라고도 함)를 검사할 수 있어요. `dmesg`를 권한이 있는 명령으로 유지하고 싶다면, `kali-tweaks`를 사용하여 이 동작을 복원할 수 있어요.

### 권한이 필요한 포트 (sysctl)

전통적으로, 1024 미만의 IPv4 포트는 "권한이 필요한 포트"라고 불리며, 권한이 있는 사용자만 사용할 수 있어요. 칼리 리눅스에서는 이 기능이 기본적으로 비활성화되어 있어, 모든 포트는 "권한이 필요 없는" 상태예요. 즉, 어떤 사용자든 1024 미만의 포트를 포함한 모든 포트에 바인딩하는 프로그램을 실행할 수 있어요.

이는 칼리가 더 이상 기본적으로 루트 사용자가 아니며, 루트가 아닌 사용자의 경우 netcat이나 Metasploit과 같은 일부 도구가 권한이 필요한 포트를 사용하려고 할 수 있기 때문에 변경되었어요. 이렇게 하면 `sudo`를 자주 실행할 필요가 줄어들어요. 1024 이하의 포트를 권한이 필요한 상태로 두고 싶다면, `kali-tweaks`를 통해 설정할 수 있어요.

### 기타 (패치)

- [carl9170 카드에 대한 스니퍼 모드 활성화](https://gitlab.com/kalilinux/packages/linux/-/blob/kali/master/debian/patches/features/all/wireless-carl9170-Enable-sniffer-mode-promisc-flag-t.patch)
- [Odroid VU7 장치를 위한 터치스크린 지원 추가](https://gitlab.com/kalilinux/packages/linux/-/blob/kali/master/debian/patches/features/all/dwav-usb-mt-driver.patch)