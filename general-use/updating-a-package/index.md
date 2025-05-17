---
title: 패키지 업데이트하기
description:
icon:
weight:
author: ["serval123",]
번역: ["xenix4845"]
---

Kali Linux에서 패키지를 업데이트하려면 Kali Linux가 기반으로 하는 Debian에서 제공하는 패키지 관리 도구를 사용할 수 있어요. 주요 패키지 관리 도구는 apt예요. 업데이트는 종종 취약점에 대한 패치를 포함하므로 시스템을 정기적으로 업데이트하는 것은 보안에 중요해요. 다음과 같은 방법으로 패키지를 업데이트할 수 있어요:

## 모든 패키지 업데이트하기

Kali Linux의 모든 패키지를 업데이트하려면, 터미널에서 다음 명령어를 실행하면 돼요.

```console
kali@kali:~$ sudo apt update
```
이 명령어는 패키지 목록을 업데이트해요.

```console
kali@kali:~$ sudo apt full-upgrade
```
설치된 모든 패키지를 최신 버전으로 업그레이드해요.

## 특정 패키지 업그레이드하기

단일 특정 패키지만 업데이트하는 것도 가능해요.

```console
kali@kali:~$ sudo apt install --only-upgrade package_name
```
`package_name`을 업데이트하려는 패키지 이름(예: nmap)으로 바꾸세요. 이 명령어는 지정된 패키지의 최신 버전을 설치하여 효과적으로 업데이트해요. `--only-upgrade` 옵션은 새 패키지 설치를 시도하지 않고 선택한 패키지만 업그레이드되도록 해요. 이 명령어를 실행하면 지정된 패키지가 저장소에서 사용 가능한 최신 버전으로 업데이트돼요.