---
title: 베어 메탈 설치의 그래픽 문제
description: 칼리 리눅스가 설치된 물리적 하드웨어의 GPU 및 커널 모듈의 단계별 문제 해결 방법
icon:
weight: 12
author: ["Soroush Nekoozadeh"]
번역: ["kmw0410"]
---

이 문서는 칼리 리눅스를 가상 머신이 아닌 베어메탈 시스템(물리 하드웨어)에 설치했을 때 발생하는 여러 일반적인 그래픽 문제에 대해 다뤄요. NVIDIA 그래픽 카드가 있는 사용자가 자주 만나는 검은 화면, 로그인 실패, 또는 커널 업데이트 또는 드라이버 설치 후 발생하는 시스템 불안정성이 자주 발생해요.

## 커널 업데이트와 DKMS 모듈 문제

칼리에서 커널 업데이트가 빈번하게 이루어질 경우, 전체 업그레이드를 실행하는 사용자는 새 버전의 커널 헤더가 설치되지 않으면 dkms 기반 모듈(NVIDIA, VirtualBox, 일부 Wi-Fi 드라이버)이 작동하지 않을 수 있어요. 이는 NVIDIA 그래픽을 사용하는 시스템에서 커널 업그레이드 후 가장 흔히 발생해요: 부팅 시 그래픽 세션이 시작되지 않고 깜빡이는 커서나 대시만 표시될 수 있어요.

본 가이드는 커널 헤더와 DKMS 모듈을 동기화 상태로 유지하는 안전하고 반복 가능한 방법과, 독점 NVIDIA 드라이버 설치를 위한 시스템 준비 방법을 제공해요.

{{% notice info %}}
이 가이드는 물리적 하드웨어에 칼리가 설치되어 있다고 가정해요(라이브 이미지가 아님). 롤링 릴리즈를 위해 작성되었으므로 필요한 경우 패키지 이름과 커널 버전을 사용 환경에 맞게 조정하세요.
{{% /notice %}}

#### 1 — 헤더 자동 설치와 DKMS 재빌드를 위한 APT 훅 추가

`/etc/apt/apt.conf.d/99-kernel-headers-dkms` 파일을 생성하세요. 이 설정은 APT가 `/lib/modules` 디렉터리에서 가장 높은 커널 버전을 확인하고, 해당 버전에 필요한 `linux-headers` 패키지가 누락된 경우 설치를 시도하며, DKMS가 설치되어 있으면 `dkms-autoinstall`을 실행하도록 해요. 이 훅은 보수적으로 동작해요: 헤더 설치 실패나 모듈 빌드 실패는 무시되므로 시스템 업그레이드가 차단되지 않아요.

안전하고 최소한의 예시 (root 권한 또는 sudo로 실행):

```console
kali@kali:~$ sudo tee /etc/apt/apt.conf.d/99-kernel-headers-dkms >/dev/null <<'EOF'
DPkg::Post-Invoke {"bash -c 'set -e; newk=$(ls -1 /lib/modules 2>/dev/null | sort -V | tail -1); if [ -n $newk ] && [ ! -d /usr/src/linux-headers-$newk ]; then echo [apt] Installing headers for $newk >&2; apt-get -y install linux-headers-$newk || true; fi; if command -v dkms >/dev/null; then dkms autoinstall -k $newk || true; fi'";};
EOF
kali@kali:~$
```

이는 커널 업그레이드가 도착했을 때 해당 헤더가 아직 설치되지 않은 일반적인 상황을 방지하는 데 도움이 돼요.

#### 2 — 일회성/수동: 헤더 설치 및 최신 커널을 위해 DKMS 재빌드

새 커널을 방금 설치했고 다음 APT 실행을 기다리지 않고 즉시 단계를 실행하려면:

```console
# 가장 높은 버전의 커널을 선택 (`uname -r`이 아님)
kali@kali:~$ newk=$(ls -1 /lib/modules | sort -V | tail -1)

# 해당 커널용 헤더 설치
kali@kali:~$ sudo apt-get update
[...]
kali@kali:~$ sudo apt-get install -y "linux-headers-$newk" || echo "linux-headers-$newk not available"
[...]
# 해당 커널용 DKMS 모듈 재빌드
kali@kali:~$ if command -v dkms >/dev/null; then
  sudo dkms autoinstall -k "$newk"
fi
```

팁: 만약 `apt-get install`로 헤더 패키지를 찾을 수 없다면, APT 소스에 올바른 저장소(롤링 대 아카이브)가 포함되어 있는지, 그리고 커널 패키지가 실제로 일치하는 헤더를 제공하는지 확인하세요.

#### 3 — 선택: GRUB 커널 명령줄 조정
부팅 시 소음을 줄이거나 펌웨어/ACPI 문제를 해결하렴녀 커널 명령줄은 변경할 수 있어요. `/etc/default/grub` 파일을 변경하고 `GRUB_CMDLINE_LINUX_DEFAULT`를 업데이트한 후 `update-grub`을 실행하고 재부팅하여 테스트하세요.

예시 (부팅 디테일 수준 변경 및 더 엄격한 ACPI 처리 추가):

```console
kali@kali:~$ sudo sed -i 's/^GRUB_CMDLINE_LINUX_DEFAULT=.*/GRUB_CMDLINE_LINUX_DEFAULT="quiet splash acpi=strict loglevel=3"/' /etc/default/grub
kali@kali:~$ sudo update-grub
[...]
kali@kali:~$
```
경고: 하드웨어를 이해하지 못하는 경우 ACPI를 비활성화하지 마세요. ACPI를 비활성화하면 커널이 플랫폼 펌웨어가 상호작용하지 못하게 되어 장치(GPU 포함)가 제대로 작동하지 않을 수 있어요. 부팅 디버그 출력을 줄이는 것도 디버깅을 어렵게 만들 수 있어요.

#### 4 — NVIDIA 드라이버 설치 전 `noveau` 비활성화

NVIDIA 전용 패키지는 `noveau`가 비활성화되어 있을 것을 가정해요. 지금부터 영구적으로 블랙리스트에 추가하려면:

```console
# 실행 중인 세션에서 nouveau 언로드 (사용 중인 경우 실패할 수 있음)
kali@kali:~$ sudo modprobe -r nouveau || true
[...]
# nouveau 블랙리스트 영구 적용
kali@kali:~$ echo "blacklist nouveau" | sudo tee /etc/modprobe.d/blacklist-nouveau.conf

# 변경 사항을 다음 부팅 시 적용하기 위해 initramfs 재빌드
kali@kali:~$ sudo update-initramfs -u
[...]
# 부팅 시 nouveau가 로드되지 않도록 재부팅
kali@kali:~$ sudo reboot
```

경고: 시스템이 그래픽 환경 부팅에 오픈소스 드라이버를 필요로 하는 경우, 블랙리스트 적용 전에 복구 계획(TTY 접근, 라이브 USB 또는 복구 미디어)를 마련하세요.

복구: 시스템이 그래픽 환경으로 부팅되지 않을 경우 블랙리스트를 해제하려면 TTY로 전환(Ctrl+Alt+F3)하거나 라이브 USB를 사용해 다음 명령을 실행하세요:

```bash
kali@kali:~$ sudo rm /etc/modprobe.d/blacklist-nouveau.conf
kali@kali:~$ sudo update-initramfs -u
[...]
sudo reboot
```

#### 5 — 이미 업그레이드한 후 깜빡이는 커서가 보이는 경우의 빠른 조치 방법

이미 업데이트를 완료했는데 시스템이 깜빡이는 커서로 부팅된다면, TTY(Ctrl+Alt+F3..F6)에서 다음 단계를 따르세요:


```bash
sudo apt update && sudo apt upgrade -y
sudo apt install -y "linux-headers-$(uname -r)" || echo "headers not available for $(uname -r)"
```

3. DKMS가 설치되어 있따면, 실행 중인 커널용 모듈을 재빌드 해야해요:

```bash
sudo dkms autoinstall -k "$(uname -r)" || true
```

4. 재부팅:

```bash
sudo reboot
```

## Wayland에서의 GNOME 문제

GNOME 버전 49부터 X11 세션에 대한 내장 지원은 더 이상 권장되지 않으며 기본적으로 비활성화 되어있어요. 이 변경으로 인해 이전에 X11 세션에 의존하던 시스템에서는 로그인 문제가 발생할 수 있으며, 검은 화면이 나타나거나 로그인 인터페이스가 표시되지 않고 시스템 로그에 다음과 같은 오류가 기록될 수 있어요:


```bash
Unit gnome-session-x11@gnome-login.target not found.
```

권장 해결책은 Wayland가 올바르게 활성화되고 구성되었는지 확인하는 것이에요. 먼저 GDM 구성에서 Wayland가 비활성화되지 않았는지 확인하세요:

```bash
kali@kali:~$ sudo grep -r "Wayland" /etc/gdm3/
/etc/gdm3/daemon.conf:#WaylandEnable=true
kali@kali:~$
```

NVIDIA 드라이버, 특히 버전 550.163.01 미만의 드라이버는 Wayland에서 GNOME을 로드할 때 여러 문제가 발생해요. 드라이버 문제를 해결하는 가장 빠른 방법은 `/etc/default/grub` 파일에 아래 옵션을 추가하는 거에요:

```bash
GRUB_CMDLINE_LINUX_DEFAULT="quiet splash acpi=strict loglevel=3 nvidia_drm.modeset=1"
```

위 해결책이 효과가 없고 NVIDIA 드라이버 버전이 540.x.x보다 오래된 경우, `nvidia_drm.fbdev=1` 옵션을 추가하세요. 이는 데비안(및 기타 리눅스 배포판)에서 NVIDIA 드라이버와 함께 사용되는 커널 부팅 매개변수로, NVIDIA 드라이버가 프레임버퍼(화면 출력)를 관리하도록 강제해요.

`/etc/default/grub` 파일을 수정한 후에는 다음 명령어를 실행하는 것을 잊지 마세요:

```console
kali@kali:~$ sudo update-grub
[...]
kali@kali:~$ sudo reboot
```