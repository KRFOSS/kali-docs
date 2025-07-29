---
title: 만료된 칼리 리눅스 서명 키로 인해 발생한 API 오류 해결하기
description:
icon:
date: 2025-07-15
weight:
author: ["serval123",]
번역: ["kmw0410"]
keywords: ["",]
og_description:
---

## 문제

GPG 키는 패키지를 업데이트하는 동안 진위성, 무결성 및 신뢰를 보장하기 위해 저장소에 서명하는 데 사용돼요. 칼리 팀은 2~3년마다 APT 저장소 서명에 사용되는 GPG 키의 유효 기간을 연장하거나 새 키로 교체해요. 이로 인해 오랫동안 kali-archive-keyring 패키지를 업데이트하지 않은 사용자는 오류가 발생할 수 있어요. 오류는 다음과 같이 표시돼요:

```console
kali@kali:~$ sudo apt update
[sudo] password for kali:
Get:1 https://http.kali.org/kali kali-rolling InRelease [41.5 kB]
Err:1 https://http.kali.org/kali kali-rolling InRelease
  Sub-process /usr/bin/sqv returned an error code (1), error message is: Missing key 827C8569F2518CC677FECA1AED65462EC8D5E4C5, which is needed to verify signature.
Fetched 41.5 kB in 3s (16.5 kB/s)
82 packages can be upgraded. Run 'apt list --upgradable' to see them.
Warning: An error occurred during the signature verification. The repository is not updated and the previous index files will be used. OpenPGP signature verification failed: https://http.kali.org/kali kali-rolling InRelease: Sub-process /usr/bin/sqv returned an error code (1), error message is: Missing key 827C8569F2518CC677FECA1AED65462EC8D5E4C5, which is needed to verify signature.
Warning: Failed to fetch https://http.kali.org/kali/dists/kali-rolling/InRelease  Sub-process /usr/bin/sqv returned an error code (1), error message is: Missing key 827C8569F2518CC677FECA1AED65462EC8D5E4C5, which is needed to verify signature.
Warning: Some index files failed to download. They have been ignored, or old ones used instead.

```
## 이슈 예방하기

앞으로 이 문제를 방지하려면:

시스템을 정기적으로 업데이트하세요, 특히 kali-archive-keyring 패키지를요.

만약 사용 중인 칼리 설치본이 2년 이상 되었다면, 더 이상 지원되지 않을 수 있어요. 계속해서 업데이트를 받으려면 최신 릴리즈로 [업그레이드](https://www.kali.org/docs/general-use/updating-kali/)하는 것을 고려하세요.


이 문제를 해결하는 또 다른 방법은 최신 키를 받아서 apt가 찾을 수 있는 위치에 저장하는 것이에요.

```console
kali@kali:~$ sudo wget https://archive.kali.org/archive-keyring.gpg -O /usr/share/keyrings/kali-archive-keyring.gpg

```
