---
title: Mesa 21 & Direct3D 12를 사용한 3D 가속
description: WSL의 칼리 리눅스에서 3D 가속 활성화하기
icon: ti-pin
archived: "true"
weight:
author: ["arnaudr",]
번역: ["kmw0410"]
---

**이 페이지는 더이상 사용되지 않습니다**. WSL에서 칼리 리눅스를 실행하는데 필요한 모든 것은 Kali Rolling에서 사용할 수 있습니다, 패키지를 설치하기 위해 `kali-experimental` 브랜치에서 설치할 필요는 없습니다.

## 목차:

- [개요](#개요)
- [설치 단계](#설치-단계)

## 개요

Mesa 3D는 칼리 리눅스를 구동하는 그래픽 스택의 기본 부분입니다. Mesa3D의 버전은 21이며, 2021년 3월에 출시되었습니다, WSL에서 GPU 가속된 3D 그래픽을 지원합니다. 이 기능은 하드웨어에서 Direct3D 12를 지원하는 경우 사용할 수 있습니다. 그리고 대략적으로, WSL을 통해 Windows에서 칼리 리눅스를 실행하는 경우 GPU를 사용하는 GUI 앱은 훨씬 더 나은 성능을 발휘합니다.

현재로서는, WSL에서 GUI 앱 지원은 아직 공개적으로 출시되지 않았습니다. WSL의 "인사이더 빌드"의 일부로만 제공됩니다. 또한 이것은 새롭고 널리 테스트되지 않은 기능입니다.

이 페이지에서는, 칼리 리눅스의 `kali-experimental` 브랜치에서 최신 버전의 Mesa를 설치하는 방법을 설명합니다.

## 설치 단계

터미널을 열고, root 사용자로 로그인하세요:

```console
kali@kali:~$ sudo su
```

칼리 리눅스가 최신 상태인지 확인하세요:

```console
root@kali:~# apt update
[...]
root@kali:~#
root@kali:~# apt full-upgrade
[...]
```

`kali-experimental`을 APT 소스에 추가하세요:

```console
root@kali:~# cat <<EOF > /etc/apt/sources.list.d/kali-experimental.list
deb http://http.kali.org/kali kali-experimental main contrib non-free non-free-firmware
EOF
```

APT가 Kali experimental을 인식할 수 있도록 다시 업데이트하세요:

```console
root@kali:~# apt update
[...]
```

이제 우리는 Mesa와 DRM같은 그래픽 스택을 업그레이드할 수 있습니다:

```console
root@kali:~# apt install -t kali-experimental '?upgradable ?source-package("mesa|libdrm")'
[...]
The following additional packages will be installed:
  libdrm-amdgpu1 libllvm12
The following NEW packages will be installed:
  libllvm12
The following packages will be upgraded:
  libdrm-amdgpu1 libegl-mesa0 libgbm1 libgl1-mesa-dri libglapi-mesa libglx-mesa0 libxatracker2 mesa-va-drivers mesa-vdpau-drivers mesa-vulkan-drivers
10 upgraded, 1 newly installed, 0 to remove and 36 not upgraded.
```

잠시 시간을 내어 이 명령줄을 이해해 보겠습니다:
- `-t kali-experimental`: APT가 `kali-experimental`로 설치할 수 있도록 요청합니다.
- `?upgradable`: 패키지 업그레이드가 가능한 경우 선택합니다 (더 새로운 후보가 있는 경우).
- `?source-package("mesa|libdrm")`: `mesa`와 `libdrm`의 소스 패키지에 속한 패키지만 선택합니다.

그게 다입니다, 칼리 리눅스는 Mesa 3D 21을 사용합니다!