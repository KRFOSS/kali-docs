---
title: Win-KeX 심리스 모드
description: Win-KeX SL (Seamless Mode)
icon: ti-pin
weight: 50
author: ["Re4son",]
번역: ["xenix4845"]
---

Win-KeX의 심리스 모드(SL)는 Windows 데스크톱 화면 상단에 Kali Linux 패널을 실행합니다.

패널을 통해 시작된 애플리케이션은 Microsoft Windows 애플리케이션과 데스크톱을 공유합니다.

심리스 모드는 Linux와 Windows 앱 사이의 시각적 분리를 제거하여, Kali Linux에서 침투 테스트를 실행하고 그 결과를 Windows 앱으로 바로 복사하여 최종 보고서를 작성할 수 있는 훌륭한 플랫폼을 제공합니다.

Win-KeX는 심리스 데스크톱 통합을 위해 [VcXsrv Windows X Server](https://sourceforge.net/projects/vcxsrv/)를 활용합니다.

![](../win-kex/win-kex-sl.png)

## 사전 요구 사항

- VcXsrv는 [Visual C++ Redistributable for Visual Studio 2015](https://www.microsoft.com/en-US/download/details.aspx?id=48145) (`vcredist140`)가 필요합니다
  - 이는 표준 Windows 설치에 포함되어 있지만, 누락되었다는 오류가 발생하면 다운로드하여 설치하면 됩니다
- (선택 사항이지만 권장) [호스트에서 직접 VcXsrv 실행](https://sourceforge.net/p/vcxsrv/wiki/VcXsrv%20%26%20Win10/)
  - [VcXsrv 설정](https://github.com/microsoft/WSL/issues/4106#issuecomment-502920377)
  - 시작 -> 설정 -> 업데이트 및 보안 -> Windows 보안 -> Windows 보안 열기
    방화벽 및 네트워크 보호 -> 앱이 방화벽을 통해 통신하도록 허용 -> 설정 변경 -> "VcXsrv windows server"의 두 항목 모두 선택 -> 확인
  - [X 서버 보호를 위한 인바운드 방화벽 규칙 추가](https://x410.dev/cookbook/wsl/protecting-x410-public-access-for-wsl2-via-windows-defender-firewall/)
<!--
  This is due to a chance with either WSL or package, and VcXsrv gives errors:
[...]
(II) GLX: Initialized Win32 native WGL GL provider for screen 0

[xkb] Starting '"\\wsl.localhost\kali-linux\usr\lib\win-kex\VcXsrv\xkbcomp" -w 1 "-R\\wsl.localhost\kali-linux\usr\lib\win-kex\VcXsrv\xkbdata" -xkm "C:\Users\<username>\AppData\Local\Temp\xkb_a36312" -em1 "The XKEYBOARD keymap compiler (xkbcomp) reports:" -emp "> " -eml "Errors from xkbcomp are not fatal to the X server" "C:\Users\<username>\AppData\Local\Temp\server-3.xkm"' failed: Funzione non corretta.
(EE) Error compiling keymap (server-3) executing '"\\wsl.localhost\kali-linux\usr\lib\win-kex\VcXsrv\xkbcomp" -w 1 "-R\\wsl.localhost\kali-linux\usr\lib\win-kex\VcXsrv\xkbdata" -xkm "C:\Users\<username>\AppData\Local\Temp\xkb_a36312" -em1 "The XKEYBOARD keymap compiler (xkbcomp) reports:" -emp "> " -eml "Errors from xkbcomp are not fatal to the X server" "C:\Users\<username>B\AppData\Local\Temp\server-3.xkm"'
(EE) XKB: Couldn't compile keymap

(EE) XKB: Failed to load keymap. Loading default keymap instead.

[xkb] Starting '"\\wsl.localhost\kali-linux\usr\lib\win-kex\VcXsrv\xkbcomp" -w 1 "-R\\wsl.localhost\kali-linux\usr\lib\win-kex\VcXsrv\xkbdata" -xkm "C:\Users\<username>\AppData\Local\Temp\xkb_a36312" -em1 "The XKEYBOARD keymap compiler (xkbcomp) reports:" -emp "> " -eml "Errors from xkbcomp are not fatal to the X server" "C:\Users\<username>\AppData\Local\Temp\server-3.xkm"' failed: Funzione non corretta.
(EE) Error compiling keymap (server-3) executing '"\\wsl.localhost\kali-linux\usr\lib\win-kex\VcXsrv\xkbcomp" -w 1 "-R\\wsl.localhost\kali-linux\usr\lib\win-kex\VcXsrv\xkbdata" -xkm "C:\Users\<username>\AppData\Local\Temp\xkb_a36312" -em1 "The XKEYBOARD keymap compiler (xkbcomp) reports:" -emp "> " -eml "Errors from xkbcomp are not fatal to the X server" "C:\Users\<username>\AppData\Local\Temp\server-3.xkm"'
(EE) XKB: Couldn't compile keymap

XKB: Failed to compile keymap
Keyboard initialization failed. This could be a missing or incorrect setup of xkeyboard-config.

(EE)
Fatal server error:
(EE) Failed to activate virtual core keyboard: 2(EE)
(EE) Server terminated with error (1). Closing log file.
-->

## 사용법

### 세션 시작하기

- 일반 사용자로 심리스 모드에서 Win-KeX를 시작하려면: `kex --sl`

Win-KeX SL을 처음 시작할 때는 Windows Defender 방화벽을 통한 트래픽 허용 권한을 요청받을 때 **공용 네트워크**를 선택해야 합니다.

![](firewall.png)

이렇게 하면 심리스 모드에서 Win-KeX가 시작됩니다:

![](../win-kex/win-kex-sl.png)

Kali 패널은 화면 상단에 위치하며 Windows 시작 메뉴는 하단에 위치합니다.

- - -

**팁**: Kali 패널이 최대화된 창의 제목 표시줄을 가릴 수 있습니다.
이를 방지하려면 패널 환경설정에서 "자동으로 숨기기"로 설정하는 것이 좋습니다.

### 사운드 지원

- Win-KeX는 펄스 오디오 지원을 포함합니다
- 사운드 지원으로 Win-KeX를 시작하려면 `--sound` 또는 `-s`를 추가하세요. 예: `kex --win --sound`
- 사운드 지원과 함께 Win-KeX를 처음 시작할 때는 Windows Defender 방화벽을 통한 트래픽 허용 권한을 요청받을 때 **공용 네트워크**를 선택해야 합니다.

![](win-kex-pulseaudio_firewall.png)

### 다중 화면 지원

Win-KeX는 다중 화면 설정을 지원합니다.

"패널 환경설정"을 열어 패널 길이를 줄이고, "패널 잠금" 체크를 해제한 후 패널을 원하는 화면으로 이동할 수 있습니다.

### 세션 중지하기

- Win-KeX SL을 종료하려면, 패널의 "로그아웃" 버튼을 통해 세션에서 로그아웃하면 됩니다.
- 필요에 따라 Win-KeX SL 서버를 종료하려면 다음을 입력하세요: `kex --sl --stop`

Win-KeX를 즐기세요!
