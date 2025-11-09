---
title: ARM 빌드 스크립트
description:
icon:
weight: 71
author: ["steev",]
번역: ["xenix4845"]
---

이 저장소에는 칼리 공식 사이트에서 내려받을 수 있는 [미리 만든 ARM 이미지](/get-kali/)를 만드는 데 쓰는 **그대로의** 스크립트가 담겨 있어요.

공식 이미지로 배포하지 않는 **추가 디바이스용 스크립트**와, 예전 기록을 위해 남겨 둔 **아카이브 스크립트**도 함께 있으니 필요하면 살펴보세요.

더 궁금한 게 있다면 [/docs/arm/](/docs/arm/) 페이지를 참고하면 돼요.

---

### 어떻게 빌드하나요?

* 스크립트는 칼리 리눅스 **arm64 / x64 / x86** 에서만 테스트됐어요. *(개인적으로는 **x64**를 추천해요.)*
* 이미지를 만들기 전에 **반드시** `./common.d/build_deps.sh`를 실행해 의존 패키지를 먼저 깔아주세요.
* **8 GB RAM** 이상이 필요해요. 부족하면 스왑(SWAP)을 만들어야 해요.

예를 들어, `[라즈베리 파이 4](/docs/arm/raspberry-pi-4/)` 이미지를 만드는 과정은 아래와 같아요.

```console
$ cd ~/
$ git clone https://gitlab.com/kalilinux/build-scripts/kali-arm
$ cd ~/kali-arm/
$ sudo ./common.d/build_deps.sh
$ sudo ./raspberry-pi.sh
```

* 빌드 시간은 **CPU·RAM·디스크·네트워크** 상황에 따라 달라요. 4코어 CPU + 8 GB RAM + SSD인 VM에서 [로컬 미러](/docs/community/setting-up-a-kali-linux-mirror/)를 쓰면 스크립트 하나에 보통 **100 분**쯤 걸려요.
* **x64 / arm64** 환경이라면 스크립트가 끝난 뒤 `~/kali-arm/images/`에 `kali-linux-2025.3-raspberry-pi-xfce-armhf.img.xz`가 생겨요.
* **x86** 환경은 메모리가 부족해 압축하지 못해요. 대신 같은 경로에 `kali-linux-2025.3-raspberry-pi-xfce-armhf.img`(무압축)이 생겨요.

  * 용량을 줄이고 싶다면 원하는 방식으로 직접 압축해 주세요.

---

### 도움말이 필요해요

모든 스크립트는 `--help` 옵션을 지원해요. `raspberry-pi.sh`를 예로 들어 볼게요.

```console
$ ./raspberry-pi.sh --help
 Usage commands:
# 아키텍처 선택 (arm64, armel, armhf)
./raspberry-pi.sh --arch arm64  또는  ./raspberry-pi.sh -a armhf

# 데스크톱 환경 (xfce, gnome, kde, i3, lxde, mate, e17, none)
./raspberry-pi.sh --desktop kde  또는  ./raspberry-pi.sh --desktop=kde

# 최소 이미지 – 데스크톱 없음
./raspberry-pi.sh --minimal      또는  ./raspberry-pi.sh -m

# 슬림 이미지 – 데스크톱·CLI 툴 모두 제외
./raspberry-pi.sh --slim         또는  ./raspberry-pi.sh -s

# 디버그 모드 + 로그(./logs/<file>.log)
./raspberry-pi.sh --debug        또는  ./raspberry-pi.sh -d

# 추가 검증 실행
./raspberry-pi.sh --extra        또는  ./raspberry-pi.sh -x

# 도움말(이 화면)
./raspberry-pi.sh --help         또는  ./raspberry-pi.sh -h
$
```

---

### 내 설정으로 바꿔볼까요?

`builder.txt`를 수정하면 예를 들어 **사내 미러**를 쓰도록 설정할 수 있어요.

```console
$ echo 'mirror="http://192.168.1.100/kali"' > ./builder.txt
```

바꿀 수 있는 항목은 아래와 같아요. 필요할 때 `#` 주석을 풀어서 써보세요.

```plaintext
# 칼리 릴리스 버전
#version=${version:-$(cat .release)}

# 호스트 이름
#hostname=kali

# 로케일(언어·지역)
#locale="en_US.UTF-8"

# rootfs에 추가할 빈 공간(MiB)
#free_space="300"

# /boot 파티션 크기(MiB)
#bootsize="128"

# 이미지 압축 형식(xz / none)
#compress="xz"

# 파일시스템 형식(ext3 / ext4)
#fstype="ext4"

# IPv6 끌까요? (yes / no)
#disable_ipv6="yes"

# 스왑 파일 만들기 (yes / no)
#swap="no"

# DNS 서버
#nameserver="8.8.8.8"

# 압축 시 사용할 CPU 코어 수
# 0: 제한 없음, -1: 전체 코어 - 1
#cpu_cores="4"

# CPU 사용률 제한(%) – 0·100은 제한 없음
#cpu_limit="85"

# 사용자 정의 미러
#mirror="http://http.kali.org/kali"

# 사용자 미러가 외부에서 접근 안 될 때 공식 미러로 바꿀까요?
#replace_mirror="http://http.kali.org/kali"

# 저장소 컴포넌트(main, contrib, non-free, non-free-firmware)
#components="main,contrib,non-free,non-free-firmware"

# 사용할 스위트 (kali-rolling, kali-dev, kali-dev-only, kali-last-snapshot)
#suite="kali-last-snapshot"

# 빌드엔 다른 스위트, 완성된 이미지는 kali-rolling으로 교체할까요?
#replace_suite="kali-rolling"

# 기본 파일명 예시
# 라즈베리 파이 스크립트라면
#   "kali-linux-202X-WXX-raspberry-pi-arm64"
# 칼리 공식 릴리스 요건: 이름은 kali-linux 로 시작, 아키텍처로 끝나야 해요.
#image_name="kali-linux-$(date +%Y)-W$(date +%U)-${hw_model}-${variant}"
```
