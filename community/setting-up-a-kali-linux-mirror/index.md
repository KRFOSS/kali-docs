---
title: Kali Linux 미러 구축하기
description:
icon:
weight:
author: ["g0tmi1k",]
---

## 공개 Kali Linux 미러 설정 방법

아래의 설명은 공개적으로 접근 가능한 미러를 제공하고 이를 미러 리다이렉터(<https://http.kali.org> 및 <https://cdimage.kali.org>)에 통합하고자 하는 경우에 유용합니다.
[ROKFOSS 프로젝트](https://krfoss.org/doc/setup-mirror/#kali-mirror)에서는 다른 방법으로도 미러를 구축하는 방법을 안내하고 있습니다. 필요하신 경우 읽어보십시오.

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

공식 Kali Linux 미러가 되기 위해서는 웹으로 접근 가능한 서버(HTTP 필수, 가능하면 HTTPS도 지원)가 필요하며, **충분한 디스크 공간, 좋은 대역폭, rsync, 그리고 SSH 접속이 활성화**되어 있어야 합니다. 서버는 **반드시 고정 IP 주소**를 가지고 있어야 합니다. 2024년 3월 기준으로 메인 패키지 저장소는 약 500GB이고 이미지 저장소는 약 175GB이지만, 이 수치는 변동될 수 있으며 시간이 지남에 따라 천천히 증가할 것입니다. 따라서 서버는 최소 1TB의 저장 공간을 확보해야 합니다.

미러 사이트는 HTTP와 RSYNC를 통해 파일을 제공해야 하므로 이 서비스들이 활성화되어 있어야 합니다. HTTPS는 선택 사항입니다. HTTP는 HTTPS로 리다이렉트되지 않아야 합니다. FTP 액세스는 선택 사항입니다.

**"푸시 미러링"에 관한 참고 사항** - Kali Linux 미러링 인프라는 미러가 새로 고침되어야 할 때 미러에 알리기 위해 SSH 기반 트리거를 사용합니다. 이는 현재 하루에 4번 이루어집니다.

### 미러용 사용자 계정 생성하기

미러 전용 계정이 아직 없다면, 계정을 생성하세요(여기서는 `archvsync`라고 부릅니다):

```console
$ sudo adduser --disabled-password archvsync
Adding user 'archvsync' ...
[...]
Is the information correct? [Y/n]
```

### 미러용 디렉토리 생성하기

미러를 포함할 디렉토리를 만들고 방금 생성한 전용 사용자로 소유자를 변경하세요:

```console
$ sudo mkdir -p /srv/mirrors/kali{,-images}
$ sudo chown archvsync:archvsync /srv/mirrors/kali{,-images}
```

### rsync 설정하기

다음으로, rsync 데몬을 설정하여(필요한 경우 활성화) 해당 디렉토리를 내보내도록 합니다:

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

웹 서버 및 FTP 서버 구성은 이 문서의 범위를 벗어납니다. 이상적으로는 미러를 `http://yourmirror.net/kali`와 `http://yourmirror.net/kali-images`에서 제공해야 합니다(FTP 프로토콜을 지원하는 경우 동일하게 설정).

이제 흥미로운 부분입니다: SSH 트리거와 실제 미러링을 처리할 전용 사용자의 설정입니다. 먼저 [ftpsync.tar.gz](https://archive.kali.org/ftpsync.tar.gz)을 사용자 계정에 풀어야 합니다:

```console
$ sudo su - archvsync
$ wget https://archive.kali.org/ftpsync.tar.gz
$ tar zxf ftpsync.tar.gz
```

이제 설정 파일을 만들어야 합니다. 템플릿에서 시작하여 최소한 `MIRRORNAME`, `TO`, `RSYNC_PATH`, 및 `RSYNC_HOST` 매개변수를 편집합니다:

```console
$ whoami
archvsync
$ cp etc/ftpsync.conf.sample etc/ftpsync-kali.conf
$ vim etc/ftpsync-kali.conf
$ grep -E '^[^#]' etc/ftpsync-kali.conf
MIRRORNAME=`hostname -f`
TO="/srv/mirrors/kali/"
RSYNC_PATH="kali"
RSYNC_HOST="archive.kali.org"
```

### SSH 키 설정하기

마지막 단계는 archive.kali.org가 미러를 트리거할 수 있도록 `.ssh/authorized_keys` 파일을 설정하는 것입니다:

```console
$ whoami
archvsync
$ mkdir -p ~/.ssh/
$ chmod 0700 ~/.ssh/
$ wget -O - -q https://archive.kali.org/pushmirror.pub >> ~/.ssh/authorized_keys
$ chmod 0600 ~/.ssh/authorized_keys
```

ftpsync.tar.gz를 홈 디렉토리에 풀지 않은 경우, `.ssh/authorized_keys`에 하드코딩된 `~/bin/ftpsync` 경로를 적절히 조정해야 합니다.

## 공개 미러 만들기 - 연락하기

이제 [devel@kali.org](mailto:devel@kali.org)로 미러에 관한 모든 세부 정보(SSH 푸시 액세스를 위한 사용자/포트, 공개 호스트 이름 등 포함)를 이메일로 보내야 합니다. 그러면 메인 미러 목록에 추가되고 archive.kali.org에서 rsync 액세스를 열어줄 것입니다. 문제가 발생하거나 미러 설정을 변경/조정해야 하는 경우 연락할 담당자를 명확히 표시해 주세요.

### 초기 동기화

archive.kali.org에서 첫 번째 푸시를 기다리는 대신, 가까운 미러와 초기 rsync를 실행하는 것이 좋습니다. 위에서 링크된 미러 목록을 사용하여 하나를 선택하세요. ftp.halifax.rwth-aachen.de를 선택했다고 가정하면, 전용 미러 사용자로 다음을 실행할 수 있습니다:

```console
$ whoami
archvsync
$ rsync -qaH ftp.halifax.rwth-aachen.de::kali /srv/mirrors/kali/ &
$ rsync -qaH ftp.halifax.rwth-aachen.de::kali-images /srv/mirrors/kali-images/ &
```

### 방화벽 규칙

네트워크 트래픽을 제한하는 경우, 다음 서비스에 대한 접근이 허용되었는지 확인하세요:

- SSH (22/TCP) - <archive.kali.org> (또는 `192.99.45.140` 및 `2607:5300:60:508c::`)
- RSYNC (873/TCP) - <archive.kali.org> (또는 `192.99.45.140` 및 `2607:5300:60:508c::`)
- RSYNC (873/TCP) - <http.kali.org> (또는 `54.39.128.230` 및 `2607:5300:203:3fe6::`)

### ISO 이미지 수동 미러링을 위한 cron 설정

ISO 이미지 저장소는 푸시 미러링을 사용하지 않으므로 일일 rsync 실행을 예약해야 합니다. 바로 사용할 수 있는 `bin/mirror-kali-images` 스크립트가 제공되며, 이를 전용 사용자의 crontab에 추가할 수 있습니다. `etc/mirror-kali-images.conf`를 구성하기만 하면 됩니다:

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

archive.kali.org가 동시에 너무 많은 미러로 인해 과부하되지 않도록 _정확한 시간을 조정하세요_.

## 사설 Kali Linux 미러 설정 방법

사설 미러를 설정하고 싶다면, 다음과 같은 차이점을 제외하고 공개 미러와 동일한 도구를 사용할 수 있습니다:

- 패키지 저장소를 위한 SSH 푸시 미러링을 사용할 수 없으므로, 미러 소유 사용자(위 설명에서는 `archvsync`)의 crontab에 `~/bin/ftpsync sync:archive:kali`를 추가해야 합니다.
- 소스 미러로 kali.org가 아닌 미러를 사용해야 합니다. 대부분은 공개 rsync 액세스를 제공합니다(kali.org 서버는 제한됨)
