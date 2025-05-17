---
title: SSH 인증을 위한 유비키(Yubikey) 설정하기
description:
icon:
weight:
author: ["g0tmi1k",]
번역: ["xenix4845"]
---

이 문서는 SSH 인증을 위해 유비키(Yubikey)를 설정하는 방법을 설명해요.

#### 사전 준비

유비키 개인화 도구(Yubikey Personalization Tool)와 스마트 카드 데몬(Smart Card Daemon)을 설치하세요.

```console
kali@kali:~$ sudo apt install -y yubikey-personalization scdaemon
```

#### 유비키 감지하기

먼저, 시스템이 [완전히 최신 상태](/docs/general-use/updating-kali/)인지 확인해야 해요:

```console
kali@kali:~$ pcsc_scan
Scanning present readers...
Reader 0: Yubico Yubikey 4 OTP+U2F+CCID 00 00
  Card state: Card inserted,
Possibly identified card (using /usr/share/pcsc/smartcard_list.txt):
    Yubico Yubikey 4 OTP+CCID
```

#### 설정

유비키가 스마트 카드로 인식되도록 하기 위해, CCID 모드로 설정해야 해요:

```console
kali@kali:~$ sudo ykpersonalize -m 86
The USB mode will be set to: 0x86

Commit? (y/n) [n]: y
```

이렇게 수정한 후에는 GPG가 유비키를 스마트 카드로 인식할 수 있어요:

```console
kali@kali:~$ gpg --card-status
Reader ...........: Yubico Yubikey 4 OTP U2F CCID 00 00
Version ..........: 2.1
Manufacturer .....: Yubico
Key attributes ...: rsa2048 rsa2048 rsa2048
Max. PIN lengths .: 127 127 127
PIN retry counter : 3 0 3
```

이제 기본 설정된 PIN을 변경해야 해요.

{{% notice info %}}
기본 PIN은 123456이고, 기본 관리자 PIN은 12345678이에요.
{{% /notice %}}

```console
kali@kali:~$ gpg --change-pin
gpg: OpenPGP card no. F8482212202010006041587850000 detected

1 - change PIN
2 - unblock PIN
3 - change Admin PIN
4 - set the Reset Code
Q - quit

Your selection? 1 # 새 PIN 입력
PIN changed.

1 - change PIN

Your selection? 3 # 새 관리자 PIN 입력
PIN changed.

Your selection? q
```
