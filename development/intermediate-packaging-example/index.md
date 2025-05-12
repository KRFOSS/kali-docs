---
title: 중급 패키징 단계별 예제
description:
icon:
weight: 12
author: ["gamb1t",]
번역: ["xenix4845"]
---

# Photon

[Photon](https://github.com/s0md3v/Photon)은 여러 의존성을 가진 **Python3** 애플리케이션이에요. 이는 [Instaloader](/docs/development/intro-to-packaging-example/)보다 더 흥미로운 패키지로, 잠재적으로 더 많은 작업이 필요해요.

## Photon 코드 개요

[Instaloader](/docs/development/intro-to-packaging-example/)처럼, 우선 [GitHub 페이지](https://github.com/s0md3v/photon)를 살펴보고 어떤 정보를 얻을 수 있는지 확인해보세요. 이 경우 다음을 확인할 수 있어요:

- `setup.py`는 없지만, `requirements.txt`([파일](https://github.com/s0md3v/Photon/blob/master/requirements.txt))가 있어요
- [라이선스는 GPL-3](https://github.com/s0md3v/Photon/blob/master/LICENSE.md)이에요
- [릴리스](https://github.com/s0md3v/photon/releases)가 있어요

`setup.py` 파일이 없기 때문에 패키징 과정에서 더 많은 작업이 필요하며, [Instaloader](/docs/development/intro-to-packaging-example/)와는 다르게 몇 가지를 처리해야 해요.

## 환경 설정하기

우리는 이미 [패키징 환경 설정에 관한 문서](/docs/development/setting-up-packaging-system/)를 따랐다고 가정해요.

이제 이 패키지를 위한 디렉토리를 설정해보세요:

```console
kali@kali:~$ mkdir -p ~/kali/packages/photon/ ~/kali/upstream
kali@kali:~$
```

## 태그 릴리스 다운로드하기

이 패키지는 [Instaloader](/docs/development/intro-to-packaging-example/)처럼 태그 릴리스가 있으므로, 동일한 과정을 따르세요. Photon의 [GitHub 릴리스 페이지](https://github.com/s0md3v/photon/releases)로 가서 최신 버전이 **1.3.0**임을 확인해요.
그런 다음 `[이름]_[버전].orig.tar.gz` 형식으로 **다운로드**하세요:

```console
kali@kali:~$ wget https://github.com/s0md3v/photon/archive/v1.3.0.tar.gz -O ~/kali/upstream/photon_1.3.0.orig.tar.gz
kali@kali:~$
```

## 패키지 소스 코드 생성하기

필수 조건이 완료되었으므로 이제 작업 디렉토리로 이동할 수 있어요:

```console
kali@kali:~$ cd ~/kali/packages/photon/
kali@kali:~/kali/packages/photon$
```

그런 다음 **빈 Git 저장소**로 만들 수 있어요:

```console
kali@kali:~/kali/packages/photon$ git init
Initialized empty Git repository in /home/kali/packages/photon/.git/
kali@kali:~/kali/packages/photon$
```

이제 이전에 다운로드한 `.tar.gz`를 방금 생성한 빈 Git 저장소로 **가져올** 수 있어요. 메시지가 표시되면 기본값을 수락하세요:

```console
kali@kali:~/kali/packages/photon$ gbp import-orig ~/kali/upstream/photon_1.3.0.orig.tar.gz
What will be the source package name? [photon]
What is the upstream version? [1.3.0]
gbp:info: Importing '/home/kali/kali/upstream/photon_1.3.0.orig.tar.gz' to branch 'upstream'...
gbp:info: Source package is photon
gbp:info: Upstream version is 1.3.0
gbp:info: Successfully imported version 1.3.0 of /home/kali/kali/upstream/photon_1.3.0.orig.tar.gz
kali@kali:~/kali/packages/photon$
```

**기본 브랜치**를 `master`에서 `kali/master`로 변경하고(upstream 개발용으로는 `master`를 사용하므로), 이전 브랜치를 **삭제**하세요.
_또한 `git branch -v`를 실행하여 변경 사항을 시각적으로 확인해요:_

```console
kali@kali:~/kali/packages/photon$ git checkout -b kali/master
Switched to a new branch 'kali/master'
kali@kali:~/kali/packages/photon$
kali@kali:~/kali/packages/photon$ git branch -D master
Deleted branch master (was 95b196b).
kali@kali:~/kali/packages/photon$
kali@kali:~/kali/packages/photon$ git branch -v
* kali/master  95b196b New upstream version 4.4.4
  pristine-tar 7e90962 pristine-tar data for photon_1.3.0.orig.tar.gz
  upstream     95b196b New upstream version 4.4.4
kali@kali:~/kali/packages/photon$
```

이제 `debian/` 폴더와 관련 파일을 **생성**할 수 있어요. 이 패키지도 **S**ingle로 설정할 거예요.
다운로드한 파일을 수동으로 지정하고(위치가 `../`가 아니므로), 템플릿에서 사용할 패키지 이름도 지정하세요:

```console
kali@kali:~/kali/packages/photon$ dh_make --file ~/kali/upstream/photon_1.3.0.orig.tar.gz -p photon_1.3.0
Type of package: (single, indep, library, python)
[s/i/l/p]?
Maintainer Name     : Joseph O'Gorman
Email-Address       : gamb1t@kali.org
Date                : Mon, 13 Jul 2020 17:28:51 -0400
Package Name        : photon
Version             : 1.3.0
License             : blank
Package Type        : single
Are the details correct? [Y/n/q]
Skipping creating ../photon_1.3.0.orig.tar.gz because it already exists
Currently there is not top level Makefile. This may require additional tuning
Done. Please edit the files in the debian/ subdirectory now.
kali@kali:~/kali/packages/photon$
kali@kali:~/kali/packages/photon$ rm debian/*.docs debian/README* debian/*.ex debian/*.EX
kali@kali:~/kali/packages/photon$
```

이 파일들이 각각 어떤 역할을 하는지 간단히 복습하면:

- `debian/changelog` - 패키지가 **업데이트될 때 추적**해요(이유와 누가 했는지 포함). 이것은 **패키지 버전**을 담당해요
- `debian/control` - **패키지의 메타데이터**에요(`apt`에서 자주 볼 수 있음)
- `debian/copyright` - **어떤 라이선스**인지를 나타내요. 패키지가 우리가 패키지를 만드는 데 들인 작업과 다른 것으로 될 수 있어요
- `debian/rules` - 소프트웨어를 **빌드하는 방법**과 패키지로 만드는 방법이에요
- `debian/source/format` - **소스 패키지** 형식이에요

이제 기본 패키징 파일이 준비되었으니, 실제 작업을 시작하기 전에 커밋하는 것이 좋겠어요:

```console
kali@kali:~/kali/packages/photon$ git add debian/
kali@kali:~/kali/packages/photon$
kali@kali:~/kali/packages/photon$ git commit -m "Initial packaging files"
[kali/master 52042da] Initial packaging files
 5 files changed, 93 insertions(+)
 create mode 100644 debian/changelog
 create mode 100644 debian/control
 create mode 100644 debian/copyright
 create mode 100755 debian/rules
 create mode 100644 debian/source/format
kali@kali:~/kali/packages/photon$
```

이제 각 파일을 편집하여 정보가 정확한지 확인해야 해요. [GitHub](https://github.com/s0md3v/photon)에서 찾은 내용을 활용하여 `debian/` 파일에 올바른 정보를 제공할 수 있어요:

- 라이선스
- 의존성
- 관리자
- 설명

## 정보 수집하기

### 라이선스/관리자

이 패키지는 [Instaloader](/docs/development/intro-to-packaging-example/)처럼 매우 간단하고, **[GitHub](https://github.com/s0md3v/photon)에서 이미 [라이선스](https://github.com/s0md3v/photon/blob/master/LICENSE.md)를 감지**했어요. 라이선스는 **[GPL-3](https://www.gnu.org/licenses/gpl-3.0.html)**이에요.

GPL-3 라이선스의 경우 업스트림 라이선스 파일 그대로 전체를 복사할 필요는 없어요. `/usr/share/common-licenses/`를 보면 이미 여러 라이선스가 전체 형태로 로컬에서 사용 가능해요. [Debian의 GPL-3 페이지](https://ftp-master.debian.org/licenses/good/gpl3/)를 보면 허용되는 축약된 버전의 라이선스를 사용할 수 있어요.

불행히도 업스트림의 라이선스 파일을 보면 연락처 정보나 이름을 볼 수 없어요. 다른 곳에서 찾아봐야 해요.

**[README.md](https://github.com/s0md3v/Photon/blob/master/README.md)**와 [Photon의 GitHub 페이지](https://github.com/s0md3v/Photon/blob/master/README.md)를 살펴보면, [X(구 트위터)](https://twitter.com/s0md3v)가 링크되어 있고 다른 관리자는 보이지 않기 때문에 `s0md3v`가 유일한 관리자로 보여요. [s0md3v의 GitHub 프로필 페이지](https://github.com/s0md3v)를 보면 이메일이 표시되어 있어요_(로그인한 경우)_! 이를 통해 계속 진행하는 데 필요한 **관리자 이름**과 **이메일 주소**를 알 수 있어요.

### 의존성/관리자

이제 이 도구가 작동하는 데 필요한 **의존성**이 무엇인지 알아내야 해요. 다행히도 앞서 발견한 `requirements.txt` 파일이 있어요. 이를 보면 **네 가지** 의존성이 필요한 것을 확인할 수 있어요:

```console
kali@kali:~/kali/packages/photon$ cat requirements.txt
requests
requests[socks]
urllib3
tld
kali@kali:~/kali/packages/photon$
```

이제 `apt`에서 올바른 이름을 찾아 나중에 `debian/control` 파일을 편집할 때 모든 것이 준비되었는지 확인해야 해요.

[Instaloader](/docs/development/intro-to-packaging-example/)에서 이미 **requests**는 `python3-requests`로 사용할 수 있다는 것을 알고 있어요. 하지만 **requests[socks]**는 그렇지 않으므로 socks를 설치하는 방법을 찾아야 해요. `python3-requests`를 검색하면 socks에 대한 언급이 포함된 결과가 없어요:

```console
kali@kali:~/kali/packages/photon$ apt-cache search python3-requests | grep -i socks
kali@kali:~/kali/packages/photon$
kali@kali:~/kali/packages/photon$ apt-cache search python3-requests
beancount - Double-entry accounting from text files
python-requests-toolbelt-doc - Utility belt for python3-requests (documentation)
python3-awsauth - AWS authentication for Amazon S3 for the python3-requests module
python3-beancount - Double-entry accounting from text files - Python module
python3-httmock - Mocking library for python3-requests
python3-proxmoxer - Python Wrapper for the Proxmox 2.x API (HTTP and SSH) (Python 3)
python3-requests - elegant and simple HTTP library for Python3, built for human beings
python3-requests-cache - persistent cache for requests library (Python 3)
python3-requests-file - File transport adapter for Requests - Python 3.X
python3-requests-futures - library for asynchronous HTTP requests (Python 3)
python3-requests-kerberos - Kerberos/GSSAPI authentication handler for python-requests - Python 3.x
python3-requests-mock - mock out responses from the requests package - Python 3.x
python3-requests-ntlm - Adds support for NTLM authentication to the requests library
python3-requests-oauthlib - module providing OAuthlib auth support for requests (Python 3)
python3-requests-toolbelt - Utility belt for advanced users of python3-requests
python3-requests-unixsocket - Use requests to talk HTTP via a UNIX domain socket - Python 3.x
python3-requestsexceptions - import exceptions from bundled packages in requests. - Python 3.x
units - converts between different systems of units
kali@kali:~/kali/packages/photon$
```

검색 범위를 넓히고 `grep`을 활용하면 필요한 것을 찾을 수 있어요!

```console
kali@kali:~/kali/packages/photon$ apt-cache search python3 | grep -i socks
python3-aiohttp-socks - SOCKS proxy connector for aiohttp (Python 3)
python3-asysocks - Socks5 / Socks4 client and server library (Python 3)
python3-socks - Python 3 SOCKS client module
python3-socksipychain - Python SOCKS/HTTP/SSL chaining proxy module
kali@kali:~/kali/packages/photon$
```

`python3-socks`가 우리의 **두 번째 의존성**인 것 같아요. 하지만 이것이 정말 올바른 패키지인지 확인해보세요:

- 먼저 [requests의 PyPI 페이지](https://pypi.org/project/requests/)에서 올바른 업스트림 소스를 찾아요.
- 다음으로 [setup.py 파일](https://github.com/psf/requests/blob/master/setup.py)을 찾고 [socks가 있는 줄](https://github.com/psf/requests/blob/master/setup.py#L106)을 살펴봐요. 여기서 PySocks가 PyPI 모듈 이름임을 알 수 있어요.
- 다시 [PySocks의 PyPI 페이지](https://pypi.org/project/PySocks/)에서 업스트림 소스를 찾아요.
- 마지막으로 PySocks의 업스트림 소스를 python3-socks와 함께 제공된 홈페이지와 비교하여 일치하는지 확인해요:

```console
kali@kali:~/kali/packages/photon$ apt-cache show python3-socks | grep Homepage
Homepage: https://github.com/Anorov/PySocks
kali@kali:~/kali/packages/photon$
```

이것을 염두에 두고, **urllib3**와 **tld**를 찾아보세요:

```console
kali@kali:~/kali/packages/photon$ apt-cache search python3 | grep urllib3
python3-urllib3 - HTTP library with thread-safe connection pooling for Python3
kali@kali:~/kali/packages/photon$
kali@kali:~/kali/packages/photon$ apt-cache search python3 | grep tld
python3-tld - Extract the top level domain (TLD) from a given URL (Python 3)
python3-tldextract - Python library for separating TLDs
kali@kali:~/kali/packages/photon$
```

다른 두 개도 찾았어요!
이제 네 가지 의존성(`python3-requests`,`python3-socks`,`python3-urllib3` & `python3-tld`)을 모두 알아냈으니 계속 진행할 수 있어요.

### 관리자

[Instaloader](/docs/development/intro-to-packaging-example/)와 마찬가지로, 다른 부분을 진행하면서 소프트웨어의 저자와 관리자를 발견했으니 이 부분에 대해서는 따로 할 일이 없어요.

### 설명

[Instaloader](/docs/development/intro-to-packaging-example/)와 유사하게 [GitHub 페이지](https://github.com/s0md3v/photon)에서 설명을 가져올 거예요. 짧은 설명과 긴 설명, 두 가지 설명 값이 필요하다는 것을 기억하세요.

첫 번째 설명은 **짧은 설명**이에요. 이것을 위해 GitHub의 소개 섹션에서 요약을 가져와서 "OSINT"를 "Open Source INTelligence"로 확장하여 "OSINT"가 무엇인지 모르는 사람들을 위해 설명해요.

**긴 설명**을 위해서는 소개 설명을 반복하고, README의 **데이터 추출** 부분 아래의 설명을 활용할 거예요. 이렇게 하는 이유는 긴 설명이 패키지 이름으로 시작할 수 없기 때문에, 짧은 설명을 반복하고 추가 컨텍스트를 제공하도록 수정할 거예요. 하지만 지금은 `debian/control` 파일을 편집할 때까지 기억해두세요.

## 패키지 소스 코드 편집하기

이제 정보를 복사했으니, `dh_make`로 만든 `debian/` 폴더의 파일들을 채우기 시작할 수 있어요.

### Changelog

여기서 다음을 변경해야 해요:

- **배포판**을 `unstable`에서 `kali-dev`로
    - unstable은 Debian의 개발 배포판이고, kali-dev는 Kali에서 사용하는 것이에요.
- **버전**(from `1.3.0-1`에서 `1.3.0-0kali1`로)
    - 형식은 `[소프트웨어버전]-0kali[릴리스]`예요. 이 패키지가 Debian에 들어갈 경우 버전 충돌을 방지하고 Debian 버전으로 적절히 업그레이드되도록 `0kali`를 사용해요.
- **로그 항목** - `Initial release`로 간단하게 유지해요

```console
kali@kali:~/kali/packages/photon$ vim debian/changelog
kali@kali:~/kali/packages/photon$
kali@kali:~/kali/packages/photon$ cat debian/changelog
photon (1.3.0-0kali1) kali-dev; urgency=medium

  * Initial release

 -- Joseph O'Gorman <gamb1t@kali.org>  Mon, 13 Jul 2020 17:28:51 -0400
 kali@kali:~/kali/packages/photon$
```

### Control

대부분은 [Instaloader](/docs/development/intro-to-packaging-example/)에서 이미 알아냈으니, 더 쉽게 채울 수 있어요. 정보 수집 과정에서 찾은 값을 제공하면 다음과 같이 됩니다:

```console
kali@kali:~/kali/packages/photon$ vim debian/control
kali@kali:~/kali/packages/photon$
kali@kali:~/kali/packages/photon$ cat debian/control
Source: photon
Section: net
Priority: optional
Maintainer: Kali Developers <devel@kali.org>
Uploaders: Joseph O'Gorman <gamb1t@kali.org>
Build-Depends: debhelper-compat (= 12),
               dh-python,
               python3-all,
               python3-requests,
               python3-socks,
               python3-tld,
               python3-urllib3,
Standards-Version: 4.5.0
Homepage: https://github.com/s0md3v/Photon
Vcs-Browser: https://gitlab.com/kalilinux/packages/photon
Vcs-Git: https://gitlab.com/kalilinux/packages/photon.git

Package: photon
Architecture: all
Depends: ${python3:Depends},
         ${misc:Depends},
         python3-requests,
         python3-socks,
         python3-tld,
         python3-urllib3,
Description: Incredibly fast crawler designed for open source intelligence
 This package includes a fast and flexible crawler designed for open source
 intelligence (OSINT).
 .
 Photon can extract the following data while crawling:
  - URLs (in-scope & out-of-scope)
  - URLs with parameters (example.com/gallery.php?id=2)
  - Intel (emails, social media accounts, amazon buckets etc.)
  - Files (pdf, png, xml etc.)
  - Secret keys (auth/API keys & hashes)
  - JavaScript files & Endpoints present in them
  - Strings matching custom regex pattern
  - Subdomains & DNS related data
 .
 The extracted information is saved in an organized manner or can be exported
 as json.
kali@kali:~/kali/packages/photon$
```

이제 `Build-Depends`에 Python3 패키지이므로 다음을 추가해야 해요:
- **dh-python**
- **python3-all**
- **python3-requests**
- **python3-socks**
- **python3-tld**
- **python3-urllib3**

그러나 이 패키지는 **setup.py** 파일이 없으므로 [Instaloader](/docs/development/intro-to-packaging-example/)와 비교하여 약간의 변경이 필요해요. 변경 사항은 `python3-setuptools` 빌드 의존성을 포함하지 않는다는 것이에요. `python3-all`은 여전히 포함시킬 것이며, 이는 다른 이유 때문이에요. 또한 패키지의 모든 의존성을 포함할 것이에요. 이렇게 하면 테스트 스위트가 있는 경우 실행할 수 있어요.

앞서 언급했듯이, `python3-all`은 여전히 포함되어 있어요. 이는 컴파일된 바이너리 확장이 필요한 Python 모듈에 의존하기 때문이에요. 다음과 같이 찾을 수 있어요:

```console
kali@kali:~/kali/packages/photon$ find /usr/lib/python3.8/ -name '*.so'
/usr/lib/python3.8/lib-dynload/_codecs_hk.cpython-38-x86_64-linux-gnu.so
[...]
/usr/lib/python3.8/dist-packages/cryptography/hazmat/bindings/_openssl.abi3.so
/usr/lib/python3.8/config-3.8-x86_64-linux-gnu/libpython3.8.so
kali@kali:~/kali/packages/photon$
```

이것은 조금 복잡해 보일 수 있고, 처음에는 그다지 도움이 되지 않아요. 우리가 이전 파일 중 하나를 결국 호출하는 의존성이 있는지 확인하기 위해 다음을 수행해요:

```console
kali@kali:~/kali/packages/photon$ apt depends python3-urllib3
python3-urllib3
  Depends: <python3:any>
    python3
  Depends: python3-six
  Recommends: ca-certificates
  Suggests: python3-cryptography
  Suggests: python3-idna
  Suggests: python3-openssl
  Suggests: python3-socks
kali@kali:~/kali/packages/photon$
```

보다시피, `python3-urllib3`는 python3 모듈 **cryptography**를 제안하는데, 이전 목록에서 `python3-all`이 필요하다는 것을 알 수 있어요. 이것으로 `python3-all`을 포함하는 것이 충분해요.

`Depends`에서는 이전과 같이 **${shlibs:Depends}**를 **${python3:Depends}**로 변경하고, 이전에 찾은 `apt` 이름(`python3-requests`, `python3-socks`, `python3-tld` & `python3-urllib3`)을 추가해요.

### Copyright

이미 사용할 **저작권 라이선스** 설명과 `Upstream-Contact` 정보를 알고 있으니, 이 파일을 쉽게 채울 수 있어요:

```console
kali@kali:~/kali/packages/photon$ vim debian/copyright
kali@kali:~/kali/packages/photon$
kali@kali:~/kali/packages/photon$ cat debian/copyright
Format: https://www.debian.org/doc/packaging-manuals/copyright-format/1.0/
Upstream-Name: photon
Upstream-Contact: s0md3v <s0md3v@gmail.com>
Source: https://gihub.com/s0md3v/Photon

Files: *
Copyright: 2020 s0md3v <s0md3v@gmail.com>
License: GPL-3+

Files: debian/*
Copyright: 2020 Joseph O'Gorman <gamb1t@kali.org>
License: GPL-3+

License: GPL-3+
 This package is free software; you can redistribute it and/or modify
 it under the terms of the GNU General Public License as published by
 the Free Software Foundation; either version 3 of the License, or
 (at your option) any later version.
 .
 This package is distributed in the hope that it will be useful,
 but WITHOUT ANY WARRANTY; without even the implied warranty of
 MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
 GNU General Public License for more details.
 .
 You should have received a copy of the GNU General Public License
 along with this program. If not, see <https://www.gnu.org/licenses/>
 .
 On Debian systems, the complete text of the GNU General
 Public License version 3 can be found in "/usr/share/common-licenses/GPL-3".
kali@kali:~/kali/packages/photon$
```

### Rules

이 rules 파일은 [Instaloader](/docs/development/intro-to-packaging-example/)의 것과 **설치 방법**에 있어서 유사하지만, 패키지 빌드 방식을 크게 변경하는 한 가지 주요 차이점이 있어요. `setup.py`가 없으므로 `pybuild` 빌드 시스템을 **설정할 필요가 없어요**:

```console
kali@kali:~/kali/packages/photon$ vim debian/rules
kali@kali:~/kali/packages/photon$
kali@kali:~/kali/packages/photon$ cat debian/rules
#!/usr/bin/make -f
#export DH_VERBOSE = 1
export PYBUILD_NAME=photon

%:
	dh $@ --with python3
kali@kali:~/kali/packages/photon$
```

"dh" 줄은 단일 탭 문자로 들여쓰기 되어야 한다는 점에 주의하세요.

### Watch

이 watch 파일은 [Instaloader](/docs/development/intro-to-packaging-example/)의 것보다 버전 변환에 신경 쓸 필요가 없어 더 쉬워요.
다음과 같이 간단한 watch 파일을 작성할 수 있어요:

```console
kali@kali:~/kali/packages/photon$ vim debian/watch
kali@kali:~/kali/packages/photon$
kali@kali:~/kali/packages/photon$ cat debian/watch
version=4
opts=filenamemangle=s/.+\/v?(\d\S+)\.tar\.gz/photon-$1\.tar\.gz/ \
  https://github.com/s0md3v/photon/tags .*/v?(\d\S+)\.tar\.gz
kali@kali:~/kali/packages/photon$
```

## .Install 및 Helper-Scripts

이 패키지의 `.install` 및 헬퍼 스크립트 파일은 매우 간단해요.

먼저 헬퍼 스크립트를 만들어요:

```console
kali@kali:~/kali/packages/photon$ mkdir -p debian/helper-script/
kali@kali:~/kali/packages/photon$
kali@kali:~/kali/packages/photon$ vim debian/helper-script/photon
kali@kali:~/kali/packages/photon$
kali@kali:~/kali/packages/photon$ cat debian/helper-script/photon
#!/bin/sh
exec python3 /usr/share/photon/photon.py "$@"
kali@kali:~/kali/packages/photon$
```

이제 install 파일을 만들 수 있어요. 이 install 파일에서는 `photon.py`가 사용할 파일들을 복사하도록 해요:

```console
kali@kali:~/kali/packages/photon$ vim debian/photon.install
kali@kali:~/kali/packages/photon$
kali@kali:~/kali/packages/photon$ cat debian/photon.install
core usr/share/photon/
plugins usr/share/photon/
photon.py usr/share/photon/
debian/helper-script/photon usr/bin/
kali@kali:~/kali/packages/photon$
```

이제 이 패키지를 여기서 마무리할 수 있어요. 이것은 **작동하는 패키지**이고, 빌드할 때 모든 것이 올바른 것으로 보여요: `gbp buildpackage --git-builder=sbuild --git-export=WC`

하지만 이 패키지를 최대한 좋게 만들고 싶어요!
`photon.py` 파일을 보면 업데이트 기능이 내장되어 있어요. 이것은 [gbp pq](https://manpages.debian.org/unstable/git-buildpackage/gbp-pq.1.en.html)를 사용하여 **패치**해야 하는 부분이에요. 또한 [autopkgtests](/docs/development/contributing-runtime-tests/)의 유용성 때문에 **최소한의 테스트**를 제공해야 해요.

### 패치

패치는 때로 업스트림 도구의 문제를 수정하거나 표준을 준수하기 위해 사용돼요. 이 도구에서는 `photon.py`에서 업데이트 기능을 제거하는 패치를 만들 거예요. 그래서 사용자가 **apt**에서 도구를 업데이트하도록 할 거예요.

이 패키지에 대한 패치를 만들기 위해 [gbp pq](https://manpages.debian.org/unstable/git-buildpackage/gbp-pq.1.en.html)를 사용할 거예요:

```console
kali@kali:~/kali/packages/photon$ gbp pq import
gbp:info: Trying to apply patches at 'ac95fad43f0418dd05510a9647b9f8e08c24ce12'
gbp:info: 0 patches listed in 'debian/patches/series' imported on 'patch-queue/kali/master'
kali@kali:~/kali/packages/photon$
```

먼저 자유롭게 작업할 수 있는 별도의 브랜치를 만들어요. 이 명령어를 사용하면 이미 생성된 패치가 자동으로 가져와지고 적용돼요:

```console
kali@kali:~/kali/packages/photon$ vim photon.py
kali@kali:~/kali/packages/photon$
kali@kali:~/kali/packages/photon$ git commit -m "disable update option"
[patch-queue/kali/master 799ec4a] disable update option
 1 file changed, 8 deletions(-)
kali@kali:~/kali/packages/photon$
```

변경한 후 커밋해요. 커밋 메시지는 패치의 제목이 될 거예요:

```console
kali@kali:~/kali/packages/photon$ gbp pq export
gbp:info: On 'patch-queue/kali/master', switching to 'kali/master'
gbp:info: Generating patches from git (kali/master..patch-queue/kali/master)
kali@kali:~/kali/packages/photon$
```

우리가 만든 커밋의 제목으로 커밋을 패치로 내보내요. 새로운 파일이 만들어진 것을 볼 수 있어요:

```console
kali@kali:~/kali/packages/photon$ ls debian/patches/
disable-update-option.patch  series
kali@kali:~/kali/packages/photon$
```

### Autopkgtest

[Autopkgtest](https://autopkgtest.kali.org/)는 **패키지 업데이트 시 문제를 감지**하는 데 큰 도움을 줘요. 이 패키지에서는 더 복잡한 오류를 잡지 못할 수 있지만 단순한 테스트라도 없는 것보다 나아요.
먼저 테스트를 만들 디렉토리를 생성해요:

```console
kali@kali:~/kali/packages/photon$ mkdir -p debian/tests
```

이제 control 파일을 만들어야 해요. 여러 명령어/테스트가 있는 경우 이 파일에 다른 정보를 넣겠지만, 실행할 명령어/테스트가 하나뿐이므로 이 파일에서 직접 작업할 수 있어요.

이 테스트에서는 photon에 `--help`를 제공하고 제대로 작동하는지 확인할 거예요. photon만 사용하므로 도구가 의존하는 항목만 필요해요. 이를 위해 **@**를 사용할 거예요. 마지막으로, 많은 것을 감지하지 못하는 단순한 테스트이므로 이것이 **표면적인** 테스트라는 것을 명시해요:

```console
kali@kali:~/kali/packages/photon$ vim debian/tests/control
kali@kali:~/kali/packages/photon$
kali@kali:~/kali/packages/photon$ cat debian/tests/control
Test-Command: photon --help
Depends: @
Restrictions: superficial
kali@kali:~/kali/packages/photon$
```

런타임 테스트에 대한 더 자세한 정보는 [우리 문서](/docs/development/contributing-runtime-tests/)에서 찾을 수 있으며, 런타임 테스트에 대해 자세히 설명하는 다른 유용한 리소스도 있어요.

## 마무리하기

이제 변경 사항을 커밋하고 모든 것이 작동하는지 확인할 수 있어요:

```console
kali@kali:~/kali/packages/photon$ git add debian/
kali@kali:~/kali/packages/photon$
kali@kali:~/kali/packages/photon$ git commit -m "Initial release"
[kali/master 9d93880] Initial release
 12 files changed, 139 insertions(+), 6 deletions(-)
 create mode 100644 debian/changelog
 create mode 100644 debian/control
 create mode 100644 debian/copyright
 create mode 100644 debian/helper-script/photon
 create mode 100644 debian/patches/disable-update-option.patch
 create mode 100644 debian/patches/series
 create mode 100644 debian/photon.install
 create mode 100755 debian/rules
 create mode 100644 debian/source/format
 create mode 100644 debian/tests/control
 create mode 100644 debian/watch
kali@kali:~/kali/packages/photon$
```

패키지를 빌드해보세요!

```console
kali@kali:~/kali/packages/photon$ gbp buildpackage --git-builder=sbuild --git-export=WC
gbp:info: Creating /home/kali/kali/build-area/photon_1.3.0.orig.tar.gz
gbp:info: Exporting 'WC' to '/home/kali/kali/build-area/photon-tmp'
gbp:info: Moving '/home/kali/kali/build-area/photon-tmp' to '/home/kali/kali/build-area/photon-1.3.0'
gbp:info: Performing the build
dh clean --with python3
   dh_clean
[...]
Processing triggers for libc-bin (2.30-8) ...
W: photon: binary-without-manpage usr/bin/photon

I: Lintian run was successful.

+------------------------------------------------------------------------------+
| Post Build                                                                   |
+------------------------------------------------------------------------------+


+------------------------------------------------------------------------------+
| Cleanup                                                                      |
+------------------------------------------------------------------------------+

Purging /<<BUILDDIR>>
Not cleaning session: cloned chroot in use

+------------------------------------------------------------------------------+
| Summary                                                                      |
+------------------------------------------------------------------------------+

Build Architecture: amd64
Build Type: full
Build-Space: 524
Build-Time: 2
Distribution: kali-dev
Host Architecture: amd64
Install-Time: 126
Job: /home/kali/kali/build-area/photon_1.3.0-0kali1.dsc
Lintian: warn
Machine Architecture: amd64
Package: photon
Package-Time: 752
Source-Version: 1.3.0-0kali1
Space: 524
Status: successful
Version: 1.3.0-0kali1
--------------------------------------------------------------------------------
Finished at 2020-07-13T22:11:01Z
Build needed 00:12:32, 524k disk space
kali@kali:~/kali/packages/photon$
```

성공적으로 완료되었어요! 이제 패키지를 테스트하고, 변경 사항을 커밋하고 푸시한 다음, 추가 요청을 보낼 수 있어요!
