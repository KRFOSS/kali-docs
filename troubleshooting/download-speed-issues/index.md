---
title: "다운로드 속도 문제 진단"
description:
icon:
weight:
author: ["gamb1t"]
번역: ["zzunipark"]
---

### 다운로드 작동 방식

Kali Linux는 커뮤니티 및 공식 미러 네트워크를 통해 배포됩니다. 즉, Kali Linux를 [다운로드](/get-kali/)할 때 실제 다운로드가 시작되기 전에 다음과 같은 단계가 수행됩니다.

1. [cdimage.kali.org](http://cdimage.kali.org/README?mirrorlist)에 요청이 전송됩니다.
2. 사용자의 위치 등을 파악하여 가장 적합한 미러로 리디렉션합니다. 예를 들어, 미국에서는 버클리 대학교 미러로 연결될 수 있습니다.
3. 선택된 미러에 다운로드 요청이 제출되면 실제로 파일 다운로드가 시작됩니다.

### 연결된 미러 확인 방법

실제로 어느 미러로 연결되었는지 확인하는 방법은 두 가지가 있습니다.

1. [다운로드](/get-kali/)를 클릭한 뒤 브라우저의 다운로드 탭에서 해당 항목을 우클릭하고 URL을 복사합니다. 대부분의 웹 브라우저에서 동작합니다.
2. `curl` 명령을 사용하여 확인합니다:

```console
kali@kali:~$ curl -i https://cdimage.kali.org/kali-2025.3/kali-linux-2025.3-installer-amd64.iso
HTTP/1.1 302 Found
Server: nginx
Date: Wed, 18 Jun 2025 10:52:17 GMT
Content-Type: text/html; charset=utf-8
Content-Length: 0
Connection: keep-alive
Cache-Control: private, no-cache
Link: <https://kali.download/base-images/kali-2025.3/kali-linux-2025.3-installer-amd64.iso>; rel=duplicate; pri=1; geo=ae
Link: <https://ask4.mm.fcix.net/kali-images/kali-2025.3/kali-linux-2025.3-installer-amd64.iso>; rel=duplicate; pri=2; geo=gb
Link: <https://ftp.hands.com/kali-images/kali-2025.3/kali-linux-2025.3-installer-amd64.iso>; rel=duplicate; pri=3; geo=gb
Location: https://mirror.vinehost.net/kali-images/kali-2025.3/kali-linux-2025.3-installer-amd64.iso

kali@kali:~$
```

여기서 `mirror.vinehost.net`으로 리디렉션된 것을 볼 수 있습니다.

### 버그 제출

다운로드 속도가 현저히 느리거나 미러가 작동하지 않는 경우, 미러 정보 및 속도를 알려주시면 문제 해결에 큰 도움이 됩니다. 다음과 같이 `wget`을 사용하여 다운로드 속도를 측정할 수 있습니다. Windows 사용자의 경우 웹 브라우저에서 속도를 확인하거나 [Windows용 wget](https://medium.com/nerd-for-tech/using-wget-command-in-windows-10-environment-d766b8f526e9)을 설치할 수 있습니다.

```console
kali@kali:~$ wget https://kali.download/base-images/kali-2025.3/kali-linux-2025.3-installer-amd64.iso
--2025-06-18 10:53:17--  https://kali.download/base-images/kali-2025.3/kali-linux-2025.3-installer-amd64.iso
Resolving kali.download (kali.download)... 2606:4700::6811:fdef, 2606:4700::6811:feef, 104.17.254.239, ...
Connecting to kali.download (kali.download)|2606:4700::6811:fdef|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 4478939136 (4.2G) [application/octet-stream]
Saving to: ‘kali-linux-2025.3-installer-amd64.iso’

kali-linux-2025.3-installer-amd64.iso   6%[====>                                                                     ] 196.46M  31.6MB/s    eta 81s

kali@kali:~$
```

위 예제에서는 약 31.6 MB/s의 속도로 다운로드가 진행됩니다. 측정된 속도와 미러 URL을 포함하여 [버그 리포트](/docs/community/submitting-issues-kali-bug-tracker/)를 제출해주세요.

- - -

프랑스에서의 또 다른 예시를 살펴봅시다:

```console
kali@kali:~$ curl -i https://cdimage.kali.org/kali-2025.3/kali-linux-2025.3-installer-amd64.iso
HTTP/1.1 302 Found
Server: nginx
Date: Wed, 18 Jun 2025 10:55:33 GMT
Content-Type: text/html; charset=utf-8
Content-Length: 0
Connection: keep-alive
Cache-Control: private, no-cache
Link: <https://kali.download/base-images/kali-2025.3/kali-linux-2025.3-installer-amd64.iso>; rel=duplicate; pri=1; geo=ae
Location: https://archive-4.kali.org/kali-images/kali-2025.3/kali-linux-2025.3-installer-amd64.iso

kali@kali:~$ wget https://archive-4.kali.org/kali-images/kali-2025.3/kali-linux-2025.3-installer-amd64.iso
--2025-06-18 10:56:00--  https://archive-4.kali.org/kali-images/kali-2025.3/kali-linux-2025.3-installer-amd64.iso
Resolving archive-4.kali.org (archive-4.kali.org)... 2001:41d0:2:f566::, 176.31.228.102
Connecting to archive-4.kali.org (archive-4.kali.org)|2001:41d0:2:f566::|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 4478939136 (4.2G) [application/octet-stream]
Saving to: ‘kali-linux-2025.3-installer-amd64.iso’

kali-linux-2025.3-installer-amd64.iso   0%[                                                                          ] 615.75K   610KB/s

kali@kali:~$
```

이제 다운로드 속도와 미러 링크를 알았으니, 모든 정보를 포함해 [버그 리포트](/docs/community/submitting-issues-kali-bug-tracker/)를 제출할 수 있습니다.

### 다른 미러 사용하기

[커뮤니티 미러 목록](/docs/community/kali-linux-mirrors/)에서 원하는 미러의 링크를 복사한 뒤, URL 중 **README** 부분을 `kali-2025.3/kali-linux-2025.3-installer-amd64.iso`(또는 사용 중인 ISO 경로)로 변경하여 사용하면 됩니다. 앞서 `curl` 예제에서 `kali-images/` 뒤에 나오는 부분을 참고하세요.
