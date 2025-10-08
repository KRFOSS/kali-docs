---
title: 칼리 리눅스 미러 구축하기
description:
icon:
weight:
author: ["g0tmi1k",]
번역: ["xenix4845"]
---

## 공개 칼리 리눅스 미러 설정 방법

{{% notice info %}}
미러(mirror)란 원본 서버의 데이터를 그대로 복제하여 제공하는 서버를 말해요. 지리적으로 가까운 미러를 사용하면 다운로드 속도가 빨라지고 원본 서버의 부하를 줄일 수 있어요.
{{% /notice %}}

아래의 설명은 공개적으로 접근 가능한 미러를 제공하고 이를 미러 리다이렉터(<https://http.kali.org> 및 <https://cdimage.kali.org>)에 통합하고 싶을 때 유용해요.
[ROKFOSS 프로젝트](https://docs.krfoss.org/mirror/%EB%A6%AC%EB%88%85%EC%8A%A4%20%EB%AF%B8%EB%9F%AC%20%EC%84%9C%EB%B2%84%20%EA%B5%AC%EC%B6%95%ED%95%98%EA%B8%B0/)에서는 다른 방법으로도 미러를 구축하는 방법을 안내하고 있어요. 필요하시면 읽어보세요.

**사설 미러**를 운영하고 싶다면, 문서 끝에 있는 전용 섹션을 참고하세요.

### 요구사항

<!--
  # Previously/historic values
  #
  # NB: the package repository can grow 250 GB bigger during a release cycle,
  # as kali-rolling diverges from kali-last-snapshot. Make sure to check the
  # size *before* a release, in order to get the upper bound. It's the upper
  # bound that mirror operators want to know.
  #
  ## /kali, aka. the main package repository
  - 2025.3     : 567 GB
  - Pre-2024.3 : 653 GB
  - Pre-2024.1 : 813 GB
  - 2023.3     : 739 GB
  - 2023.1     : 592 GB
  - 2023-02-15 : 711 GB
  - 2022.3     : 429 GB
  - 2022.1     : 1.3 TB
  - 2021.3     : 1.1 TB
  - 2021.1     : 1.1 TB
  - Early 2020 : 850 GB
  - 2015       : 450 GB

  ## /kali-images, aka. the base images repository
  - 2025.3     : 567 GB
  - Pre-2024.3 : 158 GB
  - Pre-2024.1 : 171 GB
  - 2023.3     : 153 GB
  - 2023.1     : 148 GB
  - 2023-02-15 : 132 GB
  - 2022.3     : 157 GB
  - 2022.1     : 164 GB
  - 2021.3     : 120 GB
  - 2021.1     :  84 GB
  - Early 2020 : 110 GB
  - 2015       :  50 GB
-->

공식 칼리 리눅스 미러가 되기 위해서는 웹으로 접근 가능한 서버(HTTP 필수, 가능하면 HTTPS도 지원)가 필요하고, **충분한 디스크 공간, 좋은 대역폭, rsync, 그리고 SSH 접속이 활성화**되어 있어야 해요. 서버는 **반드시 고정 IP 주소**를 가지고 있어야 해요. 2025년 9월 기준으로 메인 패키지 저장소는 약 650GB이고 이미지 저장소는 약 140GB인데, 이 수치는 변동될 수 있고 시간이 지남에 따라 천천히 증가할 거예요. 그래서 서버는 최소 1TB의 저장 공간을 확보해야 해요.

미러 사이트는 HTTP와 RSYNC를 통해 파일을 제공해야 하니 이 서비스들이 활성화되어 있어야 해요. HTTPS는 선택 사항이에요. HTTP는 HTTPS로 리다이렉트되지 않아야 해요. FTP 액세스는 선택 사항이에요.

**"푸시 미러링"에 관한 참고 사항** - 칼리 리눅스 미러링 인프라는 미러가 새로 고침되어야 할 때 미러에 알리기 위해 SSH 기반 트리거를 사용해요. 이는 현재 하루에 4번 이루어져요.

### 미러용 사용자 계정 만들기

미러 전용 계정이 아직 없다면, 계정을 만들어 보세요(여기서는 `archvsync`라고 부를게요):

```console
$ sudo adduser --disabled-password --shell /bin/bash archvsync
Adding user 'archvsync' ...
[...]
Is the information correct? [Y/n]
```

### 미러용 디렉토리 만들기

미러를 담을 디렉토리를 만들고 방금 만든 전용 사용자로 소유자를 변경해 주세요:

```console
$ sudo mkdir -p /srv/mirrors/kali{,-images}
$ sudo chown archvsync:archvsync /srv/mirrors/kali{,-images}
```

### rsync 설정하기

다음으로, rsync 데몬을 설정해서(필요하면 활성화) 해당 디렉토리를 내보내도록 해요:

```console
$ sudo sed -i -e "s/RSYNC_ENABLE=false/RSYNC_ENABLE=true/" /etc/default/rsync
$ sudo vim /etc/rsyncd.conf
$ cat /etc/rsyncd.conf
uid = nobody
gid = nogroup
max connections = 25
socket options = SO_KEEPALIVE

[kali]
path = /srv/mirrors/kali
comment = The Kali Archive
read only = true

[kali-images]
path = /srv/mirrors/kali-images
comment = The Kali ISO images
read only = true
$ sudo service rsync start
Starting rsync daemon: rsync.
```

### 미러 설정하기

웹 서버 및 FTP 서버 구성은 이 문서의 범위를 벗어나요. 이상적으로는 미러를 `http://yourmirror.net/kali`와 `http://yourmirror.net/kali-images`에서 제공해야 해요(FTP 프로토콜을 지원하는 경우에도 동일하게 설정).

이제 재밌는 부분이에요: SSH 트리거와 실제 미러링을 처리할 전용 사용자를 설정할 거예요. 먼저 [ftpsync.tar.gz](https://archive.kali.org/ftpsync.tar.gz)를 사용자 계정에 풀어야 해요:

```console
$ sudo su - archvsync
$ wget https://archive.kali.org/ftpsync.tar.gz
$ tar zxf ftpsync.tar.gz
```

이제 설정 파일을 만들어야 해요. 템플릿에서 시작해서 최소한 `MIRRORNAME`, `TO`, `RSYNC_PATH`, 및 `RSYNC_HOST` 매개변수를 수정해 주세요:

```console
$ whoami
archvsync
$ cp etc/ftpsync-kali.conf.sample etc/ftpsync-kali.conf
$ vim etc/ftpsync-kali.conf
$ grep -E '^[^#]' etc/ftpsync-kali.conf
MIRRORNAME=`hostname -f`
TO="/srv/mirrors/kali/"
RSYNC_PATH="kali"
RSYNC_HOST="archive.kali.org"
```

### SSH 키 설정하기

마지막 단계는 archive.kali.org가 미러를 트리거할 수 있도록 `.ssh/authorized_keys` 파일을 설정하는 거예요:

```console
$ whoami
archvsync
$ mkdir -p ~/.ssh/
$ chmod 0700 ~/.ssh/
$ wget -O - -q https://archive.kali.org/pushmirror.pub >> ~/.ssh/authorized_keys
$ chmod 0600 ~/.ssh/authorized_keys
```

ftpsync.tar.gz를 홈 디렉토리에 풀지 않았다면, `.ssh/authorized_keys`에 하드코딩된 `~/bin/ftpsync` 경로를 적절히 수정해야 해요.

## 공개 미러 만들기 - 연락하기

이제 [devel@kali.org](mailto:devel@kali.org)로 미러에 관한 모든 정보(SSH 푸시 액세스를 위한 사용자/포트, 공개 호스트 이름 등 포함)를 이메일로 보내야 해요. 그러면 메인 미러 목록에 추가되고 archive.kali.org에서 rsync 액세스를 열어줄 거예요. 문제가 생기거나 미러 설정을 변경/조정해야 할 경우 연락할 담당자를 명확히 알려주세요.

### 초기 동기화

archive.kali.org에서 첫 번째 푸시를 기다리는 대신, 국내 미러를 사용하여 초기 동기화를 수행할 수 있어요. 예를 들어, `mirror.jeonnam.school`을 사용할 수 있어요. 이 미러는 한국에 위치하고 있으며, 빠른 속도를 제공해요. 다음 명령어를 사용하여 초기 동기화를 수행할 수 있어요:

```console
$ whoami
archvsync
$ rsync -qaH mirror.jeonnam.school::kali /srv/mirrors/kali/ &
$ rsync -qaH mirror.jeonnam.school::kali-images /srv/mirrors/kali-images/ &
```

### 방화벽 규칙

네트워크 트래픽을 제한하는 경우, 다음 서비스에 대한 접근이 허용되었는지 확인해 주세요:
- HTTP (80,443/TCP) - 172.104.27.124 및 54.39.128.230 (이 아이피는 미러 추적에 사용됩니다)
- SSH (22/TCP) - <archive.kali.org> (aka `148.113.211.220` and `2607:5300:214:dc00::`)
- RSYNC (873/TCP) - <archive.kali.org> (aka `148.113.211.220` and `2607:5300:214:dc00::`)
- RSYNC (873/TCP) - <http.kali.org> (aka `54.39.128.230` and `2607:5300:203:3fe6::`)

### ISO 이미지 수동 미러링을 위한 cron 설정

ISO 이미지 저장소는 푸시 미러링을 사용하지 않아서 일일 rsync 실행을 예약해야 해요. 바로 사용할 수 있는 `bin/mirror-kali-images` 스크립트가 제공되니, 이걸 전용 사용자의 crontab에 추가해 보세요. `etc/mirror-kali-images.conf`만 설정하면 돼요:

```console
$ whoami
archvsync
$ cp etc/mirror-kali-images.conf.sample etc/mirror-kali-images.conf
$ vim etc/mirror-kali-images.conf
$ grep -E '^[^#]' etc/mirror-kali-images.conf
TO="/srv/mirrors/kali-images/"
$ crontab -e
$ crontab -l
# m h dom mon dow command
39 3 * * * ~/bin/mirror-kali-images
```

중요한 건 archive.kali.org에 너무 많은 미러가 동시에 몰리지 않게 조정해주세요. 예를 들어, 매일 오전 3시 39분에 실행되도록 설정했어요. 

ROKFOSS 프로젝트에서는 칼리 이미지처럼 변동이 자주 있지 않은 저장소의 경우 국내 미러를 통해서 업데이트 하는 걸 권장해요. 이는 원본 서버에 대한 부담을 줄여줄 수 있고 국내 미러를 통해서 동기화하므로 속도도 빠를 거예요! 우리는 그중에서 mirror.jeonnam.school를 추천해요.

## 사설 칼리 리눅스 미러 설정 방법

사설 미러를 설정하고 싶다면, 다음과 같은 차이점을 제외하고 공개 미러와 동일한 도구를 사용할 수 있어요:

- 패키지 저장소에 SSH 푸시 미러링을 사용할 수 없어요. 대신 미러 소유 사용자(위 설명에서는 `archvsync`)의 crontab에 `~/bin/ftpsync sync:archive:kali`를 추가해야 해요.
- kali.org가 아닌 미러를 소스 미러로 사용해야 해요. 국내에서는 `mirror.keiminem.com`을 추천해요.

## 문제 해결

### ftpsync 수동 실행

테스트를 위해 ftpsync를 수동으로 실행해볼 수 있어요. 다음과 같이 해주세요:

```
$ whoami
archvsync
$ ~/bin/ftpsync sync:archive:kali
```

### 오래된 `.~tmp~` 디렉토리

증상: 미러 동기화가 매번 일관되게 실패하고, 오류 로그에 다음과 같은 줄이 표시돼요:

```
rsync: connection unexpectedly closed (5757 bytes received so far) [generator]
rsync error: error in rsync protocol data stream (code 12) at io.c(232) [generator=3.2.7]
```

가능한 원인 및 해결 방법 (Stefan Nikolov 덕분에):

> rsync(`--delay-updates`)에 의해 남겨진 오래된 `.~tmp~` 디렉토리가 있었어요. [...] 우리의 해결 방법은 `.~tmp~` 디렉토리를 수동으로 삭제하고 다시 동기화하는 거예요.
