---
title: 베어 메탈 설치의 그래픽 문제
description: 칼리 리눅스가 설치된 물리적 하드웨어의 GPU 및 커널 모듈의 단계별 문제 해결 방법
icon:
weight: 12
author: ["Soroush Nekoozadeh"]
번역: ["kmw0410"]
---
칼리에서 커널 업데이트가 빈번하게 이루어질 경우, 전체 업그레이드를 실행하는 사용자는 새 버전의 커널 헤더가 설치되지 않으면 dkms 기반 모듈(NVIDIA, VirtualBox, 일부 Wi-Fi 드라이버)이 작동하지 않을 수 있어요. 이는 NVIDIA 그래픽을 사용하는 시스템에서 커널 업그레이드 후 가장 흔히 발생해요: 부팅 시 그래픽 세션이 시작되지 않고 깜빡이는 커서나 대시만 표시될 수 있어요.

본 문서는 커널 헤더와 DKMS 모듈을 동기화 상태로 유지하는 안전하고 반복 가능한 방법과, 독점 NVIDIA 드라이버 설치를 위한 시스템 준비 방법을 설명해요.

{{% notice info %}}
이 가이드는 물리적 하드웨어에 칼리가 설치되어 있다고 가정해요(라이브 이미지가 아님). 롤링 릴리즈를 위해 작성되었으므로 필요한 경우 패키지 이름과 커널 버전을 사용 환경에 맞게 조정하세요.
{{% /notice %}}

## 1 — 헤더 자동 설치와 DKMS 재빌드를 위한 APT 훅 추가

`/etc/apt/apt.conf.d/99-kernel-headers-dkms` 파일을 생성하세요. 이 설정은 APT가 `/lib/modules` 디렉터리에서 가장 높은 커널 버전을 확인하고, 해당 버전에 필요한 `linux-headers` 패키지가 누락된 경우 설치를 시도하며, DKMS가 설치되어 있으면 `dkms-autoinstall`을 실행하도록 해요. 이 훅은 보수적으로 동작해요: 헤더 설치 실패나 모듈 빌드 실패는 무시되므로 시스템 업그레이드가 차단되지 않아요.

안전하고 최소한의 예시 (root 권한 또는 sudo로 실행):

```console
kali@kali:~$ sudo tee /etc/apt/apt.conf.d/99-kernel-headers-dkms >/dev/null <<'EOF'
DPkg::Post-Invoke {"bash -c 'set -e; newk=$(ls -1 /lib/modules 2>/dev/null | sort -V | tail -1); if [ -n $newk ] && [ ! -d /usr/src/linux-headers-$newk ]; then echo [apt] Installing headers for $newk >&2; apt-get -y install linux-headers-$newk || true; fi; if command -v dkms >/dev/null; then dkms autoinstall -k $newk || true; fi'";};
EOF
kali@kali:~$
```

이는 커널 업그레이드가 도착했을 때 해당 헤더가 아직 설치되지 않은 일반적인 상황을 방지하는 데 도움이 돼요.

## 2 — 일회성/수동: 헤더 설치 및 최신 커널을 위해 DKMS 재빌드

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

## 3 — 선택: GRUB 커널 명령줄 조정
부팅 시 소음을 줄이거나 펌웨어/ACPI 문제를 해결하렴녀 커널 명령줄은 변경할 수 있어요. `/etc/default/grub` 파일을 변경하고 `GRUB_CMDLINE_LINUX_DEFAULT`를 업데이트한 후 `update-grub`을 실행하고 재부팅하여 테스트하세요.

예시 (부팅 디테일 수준 변경 및 더 엄격한 ACPI 처리 추가):

```console
kali@kali:~$ sudo sed -i 's/^GRUB_CMDLINE_LINUX_DEFAULT=.*/GRUB_CMDLINE_LINUX_DEFAULT="quiet splash acpi=strict loglevel=3"/' /etc/default/grub
kali@kali:~$ sudo update-grub
[...]
kali@kali:~$
```
경고: 하드웨어를 이해하지 못하는 경우 ACPI를 비활성화하지 마세요. ACPI를 비활성화하면 커널이 플랫폼 펌웨어가 상호작용하지 못하게 되어 장치(GPU 포함)가 제대로 작동하지 않을 수 있어요. 부팅 디버그 출력을 줄이는 것도 디버깅을 어렵게 만들 수 있어요.

## 4 — NVIDIA 드라이버 설치 전 `noveau` 비활성화

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
sudo rm /etc/modprobe.d/blacklist-nouveau.conf
sudo update-initramfs -u
sudo reboot
```

## 5 — 이미 업그레이드한 후 깜빡이는 커서가 보이는 경우의 빠른 조치 방법

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
