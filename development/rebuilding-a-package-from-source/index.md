---
title: 소스 패키지 재빌드하기
description:
icon:
weight: 60
author: ["g0tmi1k",]
번역: ["xenix4845"]
---

칼리 리눅스는 [패키지 수준에서 쉽게 커스터마이징](/docs/development/live-build-a-custom-kali-iso/)할 수 있고, 개별 [패키지](https://pkg.kali.org/)를 수정하고 소스 코드에서 재빌드하여 커스텀 ISO나 데스크톱 설치에 포함시키는 것도 마찬가지로 간단해요.

이를 달성하는 것은 간단한 3단계 과정이에요:

- **apt**를 사용하여 패키지 소스 다운로드
- 필요에 따라 수정
- 데비안 도구를 사용하여 패키지 재빌드

이 예시에서는 mifare-format 도구에 몇 가지 추가 하드코딩된 Mifare 액세스 키를 추가하기 위해 [libfreefare](https://github.com/nfc-tools/libfreefare) 패키지를 재빌드할 거예요.

무엇보다도 먼저 `/etc/apt/sources.list`의 `deb-src` 라인이 주석 처리되지 않았는지 확인하세요.

## 패키지 소스 다운로드하기

```console
kali@kali:~$ # Get the source package
kali@kali:~$ sudo apt update
kali@kali:~$ apt source libfreefare
kali@kali:~$ cd libfreefare-0.4.0/
```

## 패키지 소스 코드 편집하기

패키지의 소스 코드에 필요한 변경사항을 만드세요. 우리의 경우 예시 파일인 mifare-classic-format.c를 수정해요:

```console
kali@kali:~$ vim examples/mifare-classic-format.c
```

## 빌드 의존성 확인하기

패키지가 가질 수 있는 빌드 의존성을 확인하세요. 패키지를 빌드하기 전에 이들을 설치해야 해요:

```console
kali@kali:~$ dpkg-checkbuilddeps
```

출력은 이미 설치된 패키지에 따라 다음과 유사할 거예요. **dpkg-checkbuilddeps**가 아무 출력도 반환하지 않으면 모든 의존성이 이미 충족되어 빌드를 진행할 수 있다는 의미예요:

```plaintext
dpkg-checkbuilddeps: Unmet build dependencies: dh-autoreconf libnfc-dev libssl-dev
```

## 빌드 의존성 설치하기

**dpkg-checkbuilddeps**의 출력에 표시된 대로 필요한 경우 빌드 의존성을 설치하세요:

```console
kali@kali:~$ sudo apt install -y dh-autoreconf libnfc-dev libssl-dev
```

## 수정된 패키지 빌드하기

모든 의존성이 설치되면 **dpkg-buildpackage** 명령어만으로 새 버전을 빌드할 수 있어요:

```console
kali@kali:~$ dpkg-buildpackage
```

## 새 패키지 설치하기

빌드가 오류 없이 완료되면 **dpkg**로 새로 생성된 패키지를 설치할 수 있어요:

```console
kali@kali:~$ sudo dpkg -i ../libfreefare*.deb
```
