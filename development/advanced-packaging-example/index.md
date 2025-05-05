---
title: 고급 패키징 단계별 예제 — FinalRecon & Python‑icmplib
description:
icon:
weight: 13
author: ["gamb1t", "g0tmi1k",]
번역: ["xenix4845"]
---

_이 가이드는 작성한 시점을 기준으로 정확한 정보를 담고 있어요. 여러 외부 리소스를 참조하기 때문에 시간이 지나면서 소프트웨어 업데이트로 일부 내용이 달라질 수 있어요._

**[FinalRecon](https://github.com/thewhiteh4t/FinalRecon)**은 여러 파이썬 의존성을 가진 **Python 3** 애플리케이션이에요. 이 글을 작성하는 시점에서 의존성 중 하나인 **[python3-icmplib](https://github.com/ValentinBELYN/icmplib)**는 칼리 리눅스 저장소에 없네요. 이 가이드에서는 의존성 체인을 따라가는 방법과 패키지가 포함될 수 있도록 필요한 모든 것을 수정하는 방법을 배워볼 거예요. 또한 패치, 헬퍼 스크립트, 패키지를 위한 [런타임 테스트](/docs/development/contributing-runtime-tests/)도 만들어 볼 거예요.

우리는 이미 [패키징 환경 설정에 관한 문서](/docs/development/setting-up-packaging-system/)와 이전 패키징 가이드 [#1 (Instaloader)](/docs/development/intro-to-packaging-example/)와 [#2 (Photon)](/docs/development/intermediate-packaging-example/)를 따랐다고 가정하고 진행할게요. 해당 문서들에서 내용을 상세히 설명하고 있으니 참고하세요.

## FinalRecon 코드 개요

먼저 [FinalRecon의 소스 코드](https://github.com/thewhiteh4t/FinalRecon)를 살펴보고 어떤 정보를 얻을 수 있는지 확인해 볼까요? 이를 통해 다음과 같은 사항을 확인할 수 있어요:

- [태그된 릴리스가 없음](https://github.com/thewhiteh4t/FinalRecon/releases)
- [MIT 라이센스 파일](https://github.com/thewhiteh4t/FinalRecon/blob/0d41eb61a023c1ae467ede8653d37cc847695f01/LICENSE)
- `setup.py` 파일이 없음 ([setuptools](https://packaging.python.org/tutorials/packaging-projects/)에서 사용됨)
- `requirements.txt` [파일](https://github.com/thewhiteh4t/FinalRecon/blob/0d41eb61a023c1ae467ede8653d37cc847695f01/requirements.txt)이 있음 ([pip](https://pip.pypa.io/en/stable/user_guide/#requirements-files)에서 사용됨)
- 도구 및 사용 가이드에 대한 다양한 설명
- 추가 조사가 필요할 경우를 위한 다양한 외부 링크

### 태그된 릴리스 없음

FinalRecon은 [태그된 릴리스가 없어서](https://github.com/thewhiteh4t/FinalRecon/releases) 우리가 직접 업스트림(원본) 타르(tar) 파일을 생성해야 해요. 어떤 브랜치가 있는지 살펴보면 단 하나만 있네요(안정적/프로덕션 브랜치도 없고, 베타/배포/스테이징 브랜치도 없어요). 따라서 작성자가 태그된 릴리스를 만들 때까지 메인 브랜치의 최신 커밋을 사용할게요. _이슈를 열거나 작성자에게 이메일을 보내 이런 행동에 대한 응답을 얻을 수도 있어요._

데비안 패키징을 할 때는 "태그된" 릴리스가 선호돼요. 일반 사용자들은 보통 "안정적"이고 완전히 테스트된 것을 원하거든요. 또한 배포판이 패키지를 업데이트할 시기를 알기도 쉬워요: 업스트림이 새 버전을 릴리스할 때까지 기다리면 되는데, 이는 코드가 사용할 준비가 되었다는 명확한 신호니까요. 따라서 가능하다면 최신 Git 커밋보다는 태그된 릴리스를 패키징하는 것이 좋아요.

### 라이선스

이 패키지는 **[GitHub](https://github.com/thewhiteh4t/FinalRecon)**에서 **[MIT](https://opensource.org/licenses/MIT)** 라이선스를 사용한다고 확인됐어요. [라이선스 파일](https://github.com/thewhiteh4t/FinalRecon/blob/0d41eb61a023c1ae467ede8653d37cc847695f01/LICENSE)을 살펴보면 복사할 내용이 많지 않아서 그대로 복사하면 돼요. 아쉽게도 관리자는 찾았지만 연락처 정보는 아직 찾지 못했네요. 계속해서 연락처 정보를 찾아야 할 거예요.

### 의존성

`requirements.txt` [파일](https://github.com/thewhiteh4t/FinalRecon/blob/0d41eb61a023c1ae467ede8653d37cc847695f01/requirements.txt)이 있어요(파이썬의 [pip](https://pip.pypa.io/en/stable/user_guide/#requirements-files)에서 이 도구를 실행하는 데 필요한 **파이썬 의존성**을 설치하는 데 사용됨). 어떤 것들이 필요한지 확인해봐야 할 거예요.

### 설명

다시 한번 [FinalRecon의 GitHub](https://github.com/thewhiteh4t/FinalRecon)에서 설명을 가져올게요.

**짧은 설명**으로는 [README](https://github.com/thewhiteh4t/FinalRecon/blob/0d41eb61a023c1ae467ede8653d37cc847695f01/README.md)의 첫 줄을 수정해서 사용할 거예요: "웹 정찰을 위한 빠르고 간단한 파이썬 스크립트"

**긴 설명**도 [README](https://github.com/thewhiteh4t/FinalRecon/blob/0d41eb61a023c1ae467ede8653d37cc847695f01/README.md)의 첫 줄을 바탕으로 수정해서 사용하지만, 이번에는 좀 더 확장해볼게요: "모듈식 구조를 따르고 다양한 영역에 관한 자세한 정보를 제공하는 웹 정찰용 빠르고 간단한 파이썬 스크립트"

### 관리자

GitHub 전체를 살펴봐도 이메일 주소를 찾을 수 없어요. `git log`에서 관련 이메일 주소를 볼 수 있지만, "thewhiteh4t"에 대한 여러 이메일(최소 3개)이 있어서 확실하지 않네요. 대신 더 깊이 조사해봤어요. `README.md`에 YouTube 데모 비디오 링크가 있다는 것을 발견했고, YouTube 채널의 [소개 페이지](https://www.youtube.com/c/thewhiteh4t/about)에서 비즈니스 문의용 **이메일 주소**를 볼 수 있었어요(git log의 어떤 이메일과도 일치하지 않음). 이 이메일이 연락처 정보로 사용하기 좋은 선택이 될 것 같아요.
그렇지만, 연락처 정보가 없어도 패키징을 계속할 수 있으니 필수적인 부분은 아니에요.

## 환경 설정하기

우리는 이미 [패키징 환경 설정에 관한 문서](/docs/development/setting-up-packaging-system/)를 따랐다고 가정할게요.

이제 이 패키지를 위한 디렉터리를 설정해 볼까요?

```console
kali@kali:~$ mkdir -p ~/kali/packages/finalrecon/ ~/kali/upstream/
kali@kali:~$
```

## Git 스냅샷 다운로드하기

업스트림 소스 코드의 아카이브를 다운로드할 거예요. 업스트림에서 아직 릴리스 태그를 만들지 않았기 때문에 메인 브랜치의 최신 Git 커밋을 패키징할 거예요. 이 작업을 수행하는 여러 방법이 있지만, 이 예제에서는 `uscan`을 사용할게요. `uscan`은 Git 저장소를 다운로드하여 `.tar.gz` 아카이브로 압축하고 의미 있는(어느 정도 표준화된) 버전 문자열을 생성할 수 있어요.

이 마지막 부분이 중요해요. 데비안 패키지에는 반드시 버전이 있어야 하지만, Git 커밋 자체에는 버전이 없거든요. 따라서 Git 커밋과 버전을 연결해야 하는데, 이를 잘못하는 **여러 방법이 있어요**. 그래서 직접 패키지 버전을 결정하기보다는 도구(`uscan`)가 이를 처리하도록 할게요.

uscan을 사용하려면 `watch` 파일이 필요해요. 이 파일은 일반적으로 패키징 파일의 일부이며, `debian/watch`에 위치해요.

먼저 작업 디렉터리로 이동한 다음 `debian` 디렉터리를 생성해 볼까요?

```console
kali@kali:~$ cd ~/kali/packages/finalrecon/
kali@kali:~/kali/packages/finalrecon$
kali@kali:~/kali/packages/finalrecon$ mkdir debian
kali@kali:~/kali/packages/finalrecon$
```

이제 `watch` 파일을 만들어 볼게요. watch 파일의 목적은 온라인에서 최신 업스트림 릴리스를 찾기 위한 지침을 제공하는 거예요. 하지만 이 경우에는 업스트림에서 아직 태그된 릴리스를 제공하지 않았기 때문에, 메인 브랜치의 최신 Git 커밋을 추적하도록 watch 파일을 구성할게요:

```console
kali@kali:~/kali/packages/finalrecon$ vim debian/watch
kali@kali:~/kali/packages/finalrecon$
kali@kali:~/kali/packages/finalrecon$ cat debian/watch
version=4
opts="mode=git, pgpmode=none" \
  https://github.com/thewhiteh4t/FinalRecon HEAD
kali@kali:~/kali/packages/finalrecon$
```

이제 uscan을 실행해서 upstream의 최신 Git 커밋을 내려받아 tarball로 묶을 준비가 끝났어요:

```console
kali@kali:~/kali/packages/finalrecon$ uscan --destdir ~/kali/upstream/ --force-download \
    --package finalrecon --upstream-version 0~0 --watchfile debian/watch
uscan: Newest version of finalrecon on remote site is 0.0~git20201107.0d41eb6, local version is 0~0
uscan:  => Newer package available from:
        => https://github.com/thewhiteh4t/FinalRecon HEAD
uscan warn: Missing debian/source/format, switch compression to gzip
Successfully repacked ~/kali/upstream/finalrecon-0.0~git20201107.0d41eb6.tar.xz as ~/kali/upstream/finalrecon_0.0~git20201107.0d41eb6.orig.tar.gz.
```

이 명령어는 왜 이렇게 길까? 지금은 **거의 빈 디렉터리**에서 uscan을 돌리고 있어서, 옵션으로 해야 할 일을 하나하나 알려줘야 해요. 핵심은 다음 다섯 가지예요.

* `--watchfile` ― uscan이 참고할 **watch 파일**을 지정해요.
* `--package` ― **패키지 이름**을 알려줘요.
* `--upstream-version` ― “현재 업스트림 버전”을 뜻해요. 보통 uscan은 *온라인 최신 버전*과 *현재 버전*을 비교해 더 새롭다면 내려받지만, 지금은 새 패키지를 만드는 중이라 ‘현재 버전’이 없죠. 그래서 가장 낮은 값인 \*\*`0~0`\*\*을 넣어 “온라인에 있는 건 전부 최신”이라고 인식하게 만듭니다.
* `--destdir` ― 내려받은 파일을 저장할 **경로**예요.
* `--force-download` ― uscan의 기본 판단을 무시하고 **무조건 최신 업스트림**을 받아요.

옵션을 다 준 뒤, `~/kali/upstream` 폴더를 보면 이렇게 파일이 생겨 있어요:

```console
kali@kali:~/kali/packages/finalrecon$ ls ~/kali/upstream
finalrecon_0.0~git20201107.0d41eb6.orig.tar.gz  finalrecon-0.0~git20201107.0d41eb6.tar.xz
```

`uscan`이 Git에서 가져온 소스를 `.tar.xz` 파일로 압축했는데, 어떤 이유로 인해 (위의 `uscan warn:` 경고 참조) `.tar.gz`로 다시 압축했어요. 사실 압축 형식은 크게 중요하지 않아서 `.gz`와 `.xz` 둘 다 사용할 수 있어요. 중요한 점은 파일 이름이 `.orig.tar.*`로 끝나는 것을 사용해야 한다는 거예요. 그래서 우리는 `.tar.gz` 파일을 사용할 거예요.

`uscan`이 만들어낸 버전 문자열은 조금 이상해 보이는데, 바로 `0.0~git20201107.0d41eb6`예요. 왜 이런 형태일까요?

* `0.0~`는 버전 문자열의 가장 낮은 시작점이에요. 이렇게 시작하면 나중에 업스트림이 "태그된 릴리스"를 하게 되면 어떤 버전을 선택하더라도 현재 버전보다는 더 높은 버전이 되기 때문에 관리가 쉬워져요.
* `git`은 정보를 제공하기 위한 부분으로, 업스트림에서 사용하는 버전 관리 시스템(VCS)을 의미해요. 다른 예로는 `svn`이나 `bzr` 같은 것이 있어요.
* `20201107`은 업스트림 커밋 날짜(ISO-8601 형식인 YYYYMMDD)를 나타내요. 버전 문자열에 날짜가 들어가는 이유는 새로운 Git 스냅샷을 가져올 때 항상 날짜가 최신이 되도록 해서 패키지 관리자가 올바르게 버전을 정렬할 수 있게 하기 위해서예요.
* `0d41eb6`는 Git 커밋 해시예요. 이것은 명확하게 어떤 업스트림 코드가 패키지에 포함되었는지 알려주는 역할을 해요. 이게 없다면 개발자가 정확히 어떤 커밋을 패키징했는지 알기 어려워질 수 있어요. 날짜만 의존할 경우, 같은 날짜에 여러 개의 커밋이 있으면 정확히 어떤 커밋인지 불분명해질 수 있어요. 또 날짜가 UTC 기준이라 로컬 시간과 혼동될 수 있으므로, Git 커밋을 명시하는 것은 개발자에게 매우 유용해요.

이 많은 정보를 통해 패키지 관리 과정에 대해 잘 이해하셨기를 바라요. 이제 다음 단계로 넘어가서 계속 진행해볼게요.

## 패키지 소스 코드 생성

이제 새로운 **빈 Git 저장소**를 만들 거예요:

```console
kali@kali:~/kali/packages/finalrecon$ git init
Initialized empty Git repository in /home/kali/kali/packages/finalrecon/.git/
kali@kali:~/kali/packages/finalrecon$
```

그리고 방금 다운로드한 `.tar.gz` 파일을 빈 Git 저장소로 **가져오기(import)** 하면 돼요. 중간에 나오는 질문에는 기본값을 선택하거나, 명령어에 `--no-interactive` 옵션을 사용하면 돼요:

```console
kali@kali:~/kali/packages/finalrecon$ gbp import-orig ~/kali/upstream/finalrecon_0.0~git20201107.0d41eb6.orig.tar.gz
What will be the source package name? [finalrecon] 
What is the upstream version? [0.0~git20201107.0d41eb6] 
gbp:info: Importing '../upstream/finalrecon_0.0~git20201107.0d41eb6.orig.tar.gz' to branch 'upstream'...
gbp:info: Source package is finalrecon
gbp:info: Upstream version is 0.0~git20201107.0d41eb6
gbp:info: Successfully imported version 0.0~git20201107.0d41eb6 of /home/kali/kali/upstream/finalrecon_0.0~git20201107.0d41eb6.orig.tar.gz
kali@kali:~/kali/packages/finalrecon$
```

**기본 브랜치를** `master`에서 `kali/master`로 **변경한 다음**, 이전 브랜치를 **삭제하는 것**을 기억하세요.  
_변경 내용을 눈으로 확인하기 위해 간단히 `git branch -v`를 실행해요:_

```console
kali@kali:~/kali/packages/finalrecon$ git checkout -b kali/master
Switched to a new branch 'kali/master'
kali@kali:~/kali/packages/finalrecon$
kali@kali:~/kali/packages/finalrecon$ git branch -D master
Deleted branch master (was 95b196b).
kali@kali:~/kali/packages/finalrecon$
kali@kali:~/kali/packages/finalrecon$ git branch -v
* kali/master  bd003d7 New upstream version 0.0~git20201107.0d41eb6
  pristine-tar 2413cfe pristine-tar data for finalrecon_0.0~git20201107.0d41eb6.orig.tar.gz
  upstream     bd003d7 New upstream version 0.0~git20201107.0d41eb6
kali@kali:~/kali/packages/finalrecon$
```

이제 관련 파일들로 **`debian/` 폴더를 채울 수 있어요**.  
업스트림 `.tar.gz` 파일을 수동으로 지정할 거예요 _(파일이 `../`가 아닌 `~/kali/upstream/`에 있기 때문이에요)_.  패키지 이름도 이전과 동일한 명명 규칙(`패키지이름_버전`)을 사용할 거예요.

이미 `debian/` 디렉터리가 있으므로 `--addmissing` 옵션을 사용해야 해요  
(위에서 watch 파일만을 위해 생성한 것이에요).

이후에는 자동으로 생성되는 예제 파일들을 제거할 거예요  
(사용되지 않으므로).

```console
kali@kali:~/kali/packages/finalrecon$ dh_make --file ~/kali/upstream/finalrecon_0.0~git20201107.0d41eb6.orig.tar.gz -p finalrecon_0.0~git20201107.0d41eb6 --addmissing --single -y
Maintainer Name     : Joseph O'Gorman
Email-Address       : gamb1t@kali.org
Date                : Fri, 22 Apr 2022 11:33:33 +0000
Package Name        : finalrecon
Version             : 0.0~git20201107.0d41eb6
License             : blank
Package Type        : single
Currently there is not top level Makefile. This may require additional tuning
File watch.ex exists, skipping
Done. Please edit the files in the debian/ subdirectory now.

kali@kali:~/kali/packages/finalrecon$
kali@kali:~/kali/packages/finalrecon$ rm -f debian/*.docs debian/README* debian/*.ex debian/*.EX
kali@kali:~/kali/packages/finalrecon$
kali@kali:~/kali/packages/finalrecon$ git status
On branch kali/master
Untracked files:
  (use "git add <file>..." to include in what will be committed)
	debian/

nothing added to commit but untracked files present (use "git add" to track)
kali@kali:~/kali/packages/finalrecon$
kali@kali:~/kali/packages/finalrecon$ ls debian/
changelog  control  copyright  rules  source  watch
kali@kali:~/kali/packages/finalrecon$
```

이 시점에서 기본 패키징 파일이 준비되었으니, 본격적인 작업을 시작하기 전에 커밋하는 것이 좋을 것 같아요:

```console
kali@kali:~/kali/packages/finalrecon$ git add debian/
kali@kali:~/kali/packages/finalrecon$
kali@kali:~/kali/packages/finalrecon$ git commit -m "Initial packaging files"
[kali/master 52042da] Initial packaging files
 6 files changed, 93 insertions(+)
 create mode 100644 debian/changelog
 create mode 100644 debian/control
 create mode 100644 debian/copyright
 create mode 100755 debian/rules
 create mode 100644 debian/source/format
 create mode 100644 debian/watch
kali@kali:~/kali/packages/finalrecon$
```

- - -

이제 `debian/` 폴더 안의 파일들을 **정확한 정보**로 채우기 위해 편집을 시작할 거예요. 앞서 [FinalRecon GitHub](https://github.com/thewhiteh4t/FinalRecon)에서 찾은 내용을 참고해서 아래 네 가지 정보를 확실히 확인해 주세요:

* **의존성(Dependencies)**
* **설명(Description)**
* **라이선스(License)**
* **유지보수자(Maintainers)**

---

## FinalRecon 파이썬 의존성 확인하기

이 도구가 제대로 동작하려면 `requirements.txt` 파일(파이썬 [pip](https://pip.pypa.io/en/stable/user_guide/#requirements-files)에서 **필요한 라이브러리**를 설치할 때 사용하는 파일)이 무엇을 요구하는지 살펴봐야 해요.

FinalRecon은 다양한 \*\*파이썬 라이브러리(의존성)\*\*을 쓰고, 별도의 시스템 명령은 호출하지 않아요.

파이썬 생태계에는 **pip**(파이썬 패키지 관리자)가 있어서, 필요한 라이브러리를 내려받고 설치해 주죠. 하지만 우리는 이제 **데비안 패키지 관리 체계**로 빌드할 예정이므로, pip용 라이브러리를 \*\*데비안 형식(.deb)\*\*으로 포팅해야 해요. 그렇게 해야 운영체제 차원에서 파일을 추적·업그레이드·제거할 수 있습니다.

먼저 기본값(표준 저장소)에 없는 라이브러리가 무엇인지 확인해 볼게요.


```console
kali@kali:~/kali/packages/finalrecon$ cat requirements.txt
requests
ipwhois
bs4
lxml
dnslib
aiohttp
aiodns
psycopg2
tldextract
icmplib
kali@kali:~/kali/packages/finalrecon$
```

- - -

그런 다음 `requirements.txt`에 있는 각 의존성을 `apt-cache`에서 검색해 칼리 리눅스 저장소에 모두 있는지 확인해 볼 거예요:

```console
kali@kali:~/kali/packages/finalrecon$ sudo apt update
kali@kali:~/kali/packages/finalrecon$
kali@kali:~/kali/packages/finalrecon$ apt-cache search ipwhois | grep -i python3
python3-ipwhois - Retrieve and parse whois data for IP addresses (Python 3)
kali@kali:~/kali/packages/finalrecon$
```

- - -

우리는 위 과정을 `requirements.txt`의 모든 항목에 대해 반복하면서 수동으로 검색할 수도 있고, 간단한 루프를 만들어 자동화할 수도 있어요.

이 과정에서 **엔트리가 없는 의존성 하나(`icmplib`)**를 발견하게 될 거예요:

```console
kali@kali:~/kali/packages/finalrecon$ cat requirements.txt | while read x; do
  apt-cache search $x | grep -i "python3-$x -" \
    || echo --MISSING $x--;
done
python3-requests - elegant and simple HTTP library for Python3, built for human beings
python3-ipwhois - Retrieve and parse whois data for IP addresses (Python 3)
python3-bs4 - error-tolerant HTML parser for Python 3
python3-lxml - pythonic binding for the libxml2 and libxslt libraries
python3-dnslib - Module to encode/decode DNS wire-format packets (Python 3)
python3-aiohttp - http client/server for asyncio
python3-aiodns - Asynchronous DNS resolver library for Python 3
python3-psycopg2 - Python 3 module for PostgreSQL
python3-tldextract - Python library for separating TLDs
--MISSING icmplib--
kali@kali:~/kali/packages/finalrecon$
```

- - -

`icmplib`에 대해 **검색 범위를 넓혀**볼 수 있어요(이전에는 grep으로 출력을 제한했기 때문에):

```console
kali@kali:~/kali/packages/finalrecon$ apt-cache search icmplib | grep -i python3
kali@kali:~/kali/packages/finalrecon$
kali@kali:~/kali/packages/finalrecon$ apt-cache search icmplib
kali@kali:~/kali/packages/finalrecon$
```

- - -

안타깝게도 칼리 리눅스에는 현재 이 의존성(Python의 icmplib)이 저장소에 없는 것 같아요. 이는 FinalRecon을 완전히 패키징할 수 있도록 icmplib도 패키징하기 위해 **패키징 프로세스를 확장**해야 한다는 의미예요.

먼저 **[pypi.org](https://pypi.org/) 저장소**에서 `icmplib`를 찾아볼게요. [PyPI의 icmplib](https://pypi.org/project/icmplib/)와 함께 [GitHub 페이지](https://github.com/ValentinBELYN/icmplib) 링크를 쉽게 찾을 수 있어요. FinalRecon과 마찬가지로 icmplib의 GitHub 페이지를 살펴보면 **icmplib에는 추가 의존성이 필요하지 않을 것**이라는 것을 알 수 있어요(`requirements.txt` 파일이 없고 `setup.py`에는 [`install_requires`](https://packaging.python.org/discussions/install-requires-vs-requirements/)용으로 나열된 것이 없음). 따라서 비교적 간단한 파이썬 패키지가 될 거예요.

이제 두 가지 선택지가 있어요:

- icmplib로 넘어가기 전에 FinalRecon 패키징을 계속해요. `icmplib` 작업이 끝날 때까지 완전히 작동하는 패키지를 만들 수 없다는 점을 기억해야 해요.
- FinalRecon 패키징을 일시 중단하고 icmplib에 초점을 맞춰요. 지금까지 수행한 작업과 수집한 정보에 대해 자세한 메모를 작성해야 해요.

우리는 전자 옵션을 선택하여 **가능한 한 FinalRecon 작업을 계속**할게요.

## FinalRecon 패키지 소스 코드 편집하기

이제 `debian/` 폴더의 파일을 편집할 수 있어요.

### 변경로그

이제 이전 가이드([#1 (Instaloader)](/docs/development/intro-to-packaging-example/) & #2 (Photon))에서처럼 **버전**, **배포판**, **설명**에 대한 표준 변경을 수행해요. 결과 파일은 다음과 유사해야 해요:

```console
kali@kali:~/kali/packages/finalrecon$ vim debian/changelog
kali@kali:~/kali/packages/finalrecon$
kali@kali:~/kali/packages/finalrecon$ cat debian/changelog
finalrecon (0.0~git20201107.0d41eb6-0kali1) kali-dev; urgency=medium

  * Initial release

 -- Joseph O'Gorman <gamb1t@kali.org>  Fri, 22 Apr 2022 11:33:33 +0700
kali@kali:~/kali/packages/finalrecon$
```

{{% notice info %}}
`vim` 대신 `dch -r` 또는 `gbp dch`를 사용할 수도 있어요.

이전에 사용한 날짜와 동일하게 버전을 업데이트해야 해요.
{{% /notice %}}

### 컨트롤

GitHub과 소스 코드에서 이미 수집한 정보를 바탕으로, 이전 패키징 가이드([#1 (Instaloader)](/docs/development/intro-to-packaging-example/)와 [#2 (Photon)](/docs/development/intermediate-packaging-example/))와 유사하게 작업할 거예요. 이제 무엇을 변경해야 하는지 잘 이해하고 있을 거예요.

컴파일할 코드가 없으므로 `Architecture: all`로 설정할 수 있어요. 이는 대부분의 파이썬 스크립트에 해당하는데, 파이썬 "확장"을 제공하지 않기 때문이에요. 그렇지 않으면 컴파일된 `.so` 파일(예: [psycopg2](https://pkg.kali.org/pkg/psycopg2))을 생성해요.

패키지를 빌드하기 위한 **파이썬 의존성**과 도구 실행을 위한 의존성(pip의 값)을 포함해야 해요.

한 가지 주목할 점은 `python3-icmplib`예요. **이 패키지는 아직 존재하지 않아요**. 곧 만들 것이므로 나중에 추가하지 않아도 되도록 지금 추가할게요. 이는 **`icmplib` 작업이 끝날 때까지 패키지를 빌드할 수 없다는 것**을 의미해요:

```console
kali@kali:~/kali/packages/finalrecon$ vim debian/control
kali@kali:~/kali/packages/finalrecon$
kali@kali:~/kali/packages/finalrecon$ cat debian/control
Source: finalrecon
Section: misc
Priority: optional
Maintainer: Kali Developers <devel@kali.org>
Uploaders: Joseph O'Gorman <gamb1t@kali.org>
Build-Depends: debhelper-compat (= 12),
               dh-python,
               python3-aiodns,
               python3-aiohttp,
               python3-all,
               python3-bs4,
               python3-dnslib,
               python3-icmplib,
               python3-ipwhois,
               python3-lxml,
               python3-psycopg2,
               python3-requests,
               python3-tldextract,
Standards-Version: 4.5.0
Homepage: https://github.com/thewhiteh4t/FinalRecon
Vcs-Browser: https://gitlab.com/kalilinux/packages/finalrecon
Vcs-Git: https://gitlab.com/kalilinux/packages/finalrecon

Package: finalrecon
Architecture: all
Depends: ${misc:Depends},
         ${python3:Depends},
         python3-aiodns,
         python3-aiohttp,
         python3-bs4,
         python3-dnslib,
         python3-icmplib,
         python3-ipwhois,
         python3-lxml,
         python3-psycopg2,
         python3-requests,
         python3-tldextract,
Description: A fast and simple python script for web reconnaissance
 A fast and simple python script for web reconnaissance that follows
 a modular structure and provides detailed information on various areas.
kali@kali:~/kali/packages/finalrecon$
```

### 저작권

이미 저작권 정보(라이선스, 이름, 연락처, 연도 및 출처)를 얻었으므로 이제 그냥 추가하면 돼요:

```console
kali@kali:~/kali/packages/finalrecon$ vim debian/copyright
kali@kali:~/kali/packages/finalrecon$
kali@kali:~/kali/packages/finalrecon$ cat debian/copyright
Format: https://www.debian.org/doc/packaging-manuals/copyright-format/1.0/
Upstream-Name: finalrecon
Upstream-Contact: thewhiteh4t <thewhiteh4t@protonmail.com>
Source: https://github.com/thewhiteh4t/FinalRecon

Files: *
Copyright: 2020 thewhiteh4t <thewhiteh4t@protonmail.com>
License: MIT

Files: debian/*
Copyright: 2020 Joseph O'Gorman <gamb1t@kali.org>
License: MIT

License: MIT
 Copyright (c) 2020 thewhiteh4t
 .
 Permission is hereby granted, free of charge, to any person obtaining a copy
 of this software and associated documentation files (the "Software"), to deal
 in the Software without restriction, including without limitation the rights
 to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
 copies of the Software, and to permit persons to whom the Software is
 furnished to do so, subject to the following conditions:
 .
 The above copyright notice and this permission notice shall be included in all
 copies or substantial portions of the Software.
 .
 THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
 IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
 FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
 AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
 LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
 OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
 SOFTWARE.
kali@kali:~/kali/packages/finalrecon$
```

### Rules

rules 파일의 시작 부분은 #2 (Photon)와 매우 유사하지만, 새로운 하단 섹션이 있어요.
이 부분은 `finalrecon.py`의 권한을 설정하여 symlink(by `debian/links`)를 사용하여 호출할 때 실행 가능하도록 해요:

```console
kali@kali:~/kali/packages/finalrecon$ vim debian/rules
kali@kali:~/kali/packages/finalrecon$
kali@kali:~/kali/packages/finalrecon$ cat debian/rules
#!/usr/bin/make -f
#export DH_VERBOSE = 1
export PYBUILD_NAME=finalrecon

%:
    dh $@ --with python3

override_dh_install:
    dh_install
    chmod 0755 debian/finalrecon/usr/share/finalrecon/finalrecon.py
kali@kali:~/kali/packages/finalrecon$
```

{{% notice info %}}
Beware that the "dh" line needs to be indented by a single tabulation character, rather than spaces.
{{% /notice %}}

### Watch

watch 파일은 이 예제의 시작 부분에서 이미 다루었으며, 메인 브랜치의 최신 Git 커밋을 추적하도록 구성되어 있어요.

**[GitHub용 공통 구성](https://wiki.debian.org/debian/watch)**도 추가할 수 있지만, 주석 처리해 두세요. 그러면 업스트림이 릴리스를 발표할 때 watch 파일에 모든 것이 준비되어 있고 주석 처리만 해제하면 돼요:

```console
kali@kali:~/kali/packages/finalrecon$ vim debian/watch
kali@kali:~/kali/packages/finalrecon$
kali@kali:~/kali/packages/finalrecon$ cat debian/watch
version=4                                       
opts=mode=git,pgpmode=none \
  https://github.com/thewhiteh4t/FinalRecon HEAD

# Use the following when upstream starts to tag releases:
#opts=filenamemangle=s/.+\/v?(\d\S+)\.tar\.gz/finalrecon-$1\.tar\.gz/ \
#  https://github.com/thewhiteh4t/FinalRecon/tags .*/v?(\d\S+)\.tar\.gz
kali@kali:~/kali/packages/finalrecon$
```

### Links

이전([#1 (Instaloader)](/docs/development/intro-to-packaging-example/) & [#2 (Photon)](/docs/development/intermediate-packaging-example/))과는 달리, 이번에는 "헬퍼 스크립트"를 사용하지 않고 여전히 `$PATH`에 있는 주 파이썬 파일을 가리키는 심볼릭 링크를 생성해요:

```console
kali@kali:~/kali/packages/finalrecon$ vim debian/links
kali@kali:~/kali/packages/finalrecon$
kali@kali:~/kali/packages/finalrecon$ cat debian/links
usr/share/finalrecon/finalrecon.py usr/bin/finalrecon
kali@kali:~/kali/packages/finalrecon$
```

{{% notice info %}}
스크립트 테스트 과정 중에 파이썬 스크립트를 호출하기 전에 디렉터리로 `cd`해야 한다는 것을 알게 되었어요. 이는 도구가 파일을 `dumps` 폴더에 저장하려고 하기 때문이며, FinalRecon이 경로에 없으면 실패해요.

이는 소스 코드를 검사하거나 패키지가 빌드된 후 테스트할 때 시행착오를 통해 발견할 수 있어요.
{{% /notice %}}

### .Install

이제 설치 파일을 만들 수 있어요. 이 파일은 **패키지 압축을 풀 때 어떤 파일이 시스템의 어디로 가는지** 지정하는 데 필요해요. 패키지 디렉터리의 루트에서 모든 것을 포함해야 해요:

```console
kali@kali:~/kali/packages/finalrecon$ vim debian/finalrecon.install
kali@kali:~/kali/packages/finalrecon$
kali@kali:~/kali/packages/finalrecon$ cat debian/finalrecon.install
conf usr/share/finalrecon/
finalrecon.py usr/share/finalrecon/
modules usr/share/finalrecon/
wordlists usr/share/finalrecon/
kali@kali:~/kali/packages/finalrecon$
```

{{% notice info %}}
대상 디렉터리 앞에 슬래시가 없어요
{{% /notice %}}

### Patches

이 도구에서는 **업데이트 및 의존성 검사기를 비활성화하기 위한 패치를 구현**해야 해요. 프로그램이 자체 업데이트를 하면 시스템이 패키지 외부의 추가 파일을 인식하지 못하므로 상황이 복잡해져요. 의존성도 이제 패키지가 처리해요. 이 작업이 필요하다는 것을 알기 위해서는 도구에 대한 지식이나 소스 코드 검토가 필요해요.

패치 프로세스는 다음과 같아요(자세한 내용은 [이전 가이드 #2 (Photon)](/docs/development/intermediate-packaging-example/) 참조):

```console
kali@kali:~/kali/packages/finalrecon$ gbp pq import
gbp:info: Trying to apply patches at 'f1c4c9f8d25224186749ce69a9f403f207feda03'
gbp:info: 0 patches listed in 'debian/patches/series' imported on 'patch-queue/kali/master'
kali@kali:~/kali/packages/finalrecon$
kali@kali:~/kali/packages/finalrecon$ vim finalrecon.py
kali@kali:~/kali/packages/finalrecon$
kali@kali:~/kali/packages/finalrecon$ git add finalrecon.py
kali@kali:~/kali/packages/finalrecon$
kali@kali:~/kali/packages/finalrecon$ git commit -m "disable requirements check"
[...]
kali@kali:~/kali/packages/finalrecon$
kali@kali:~/kali/packages/finalrecon$ vim finalrecon.py
kali@kali:~/kali/packages/finalrecon$
kali@kali:~/kali/packages/finalrecon$ git add finalrecon.py
kali@kali:~/kali/packages/finalrecon$
kali@kali:~/kali/packages/finalrecon$ git commit -m "disable ver_check"
[...]
kali@kali:~/kali/packages/finalrecon$
kali@kali:~/kali/packages/finalrecon$ gbp pq export
gbp:info: On 'patch-queue/kali/master', switching to 'kali/master'
gbp:info: Generating patches from git (kali/master..patch-queue/kali/master)
kali@kali:~/kali/packages/finalrecon$
kali@kali:~/kali/packages/finalrecon$ git branch -v
* kali/master             bd003d7 New upstream version 0.0~git20201107.0d41eb6
  patch-queue/kali/master 2935f22 disable ver_check
  pristine-tar            2413cfe pristine-tar data for finalrecon_0.0~git20201107.0d41eb6.orig.tar.gz
  upstream                bd003d7 New upstream version 0.0~git20201107.0d41eb6
kali@kali:~/kali/packages/finalrecon$
kali@kali:~/kali/packages/finalrecon$ ls debian/patches/
disable-requirements-check.patch  disable-ver_check.patch  series
kali@kali:~/kali/packages/finalrecon$
kali@kali:~/kali/packages/finalrecon$ cat debian/patches/disable-requirements-check.patch
From: Joseph O'Gorman <gamb1t@kali.org>
Date: Sun, 8 Nov 2020 07:26:07 +0000
Subject: disable requirements check

---
 finalrecon.py | 32 ++++++++++++++++----------------
 1 file changed, 16 insertions(+), 16 deletions(-)

diff --git a/finalrecon.py b/finalrecon.py
index 735f40b..95e99f1 100644
--- a/finalrecon.py
+++ b/finalrecon.py
@@ -26,22 +26,22 @@ else:

 path_to_script = os.path.dirname(os.path.realpath(__file__))

-with open(path_to_script + '/requirements.txt', 'r') as rqr:
-       pkg_list = rqr.read().strip().split('\n')
-
-print('\n' + G + '[+]' + C + ' Checking Dependencies...' + W + '\n')
-
-for pkg in pkg_list:
-       spec = importlib.util.find_spec(pkg)
-       if spec is None:
-               print(R + '[-]' + W + ' {}'.format(pkg) + C + ' is not Installed!' + W)
-               fail = True
-       else:
-               pass
-if fail == True:
-       print('\n' + R + '[-]' + C + ' Please Execute ' + W + 'pip3 install -r requirements.txt' + C + ' to Install Missing Packages' + W + '\n')
-       os.remove(pid_path)
-       sys.exit()
+#with open(path_to_script + '/requirements.txt', 'r') as rqr:
+#      pkg_list = rqr.read().strip().split('\n')
+
+#print('\n' + G + '[+]' + C + ' Checking Dependencies...' + W + '\n')
+
+#for pkg in pkg_list:
+#      spec = importlib.util.find_spec(pkg)
+#      if spec is None:
+#              print(R + '[-]' + W + ' {}'.format(pkg) + C + ' is not Installed!' + W)
+#              fail = True
+#      else:
+#              pass
+#if fail == True:
+#      print('\n' + R + '[-]' + C + ' Please Execute ' + W + 'pip3 install -r requirements.txt' + C + ' to Install Missing Packages' + W + '\n')
+#      os.remove(pid_path)
+#      sys.exit()

 import argparse

kali@kali:~/kali/packages/finalrecon$
kali@kali:~/kali/packages/finalrecon$ cat debian/patches/disable-requirements-check.patch
From: Joseph O'Gorman <gamb1t@kali.org>
Date: Sun, 8 Nov 2020 07:26:53 +0000
Subject: disable ver_check

---
 finalrecon.py | 2 +-
 1 file changed, 1 insertion(+), 1 deletion(-)

diff --git a/finalrecon.py b/finalrecon.py
index 95e99f1..d21877c 100644
--- a/finalrecon.py
+++ b/finalrecon.py
@@ -207,7 +207,7 @@ def full_recon():
 try:
        fetch_meta()
        banner()
-       ver_check()
+       #ver_check()

        if target.startswith(('http', 'https')) == False:
                print(R + '[-]' + C + ' Protocol Missing, Include ' + W + 'http://' + C + ' or ' + W + 'https://' + '\n')
kali@kali:~/kali/packages/finalrecon$
```

## 런타임 테스트

[런타임 테스트](/docs/development/contributing-runtime-tests/)는 다음과 같이 진행돼요(자세한 내용은 [이전 가이드 #2 (Photon)](/docs/development/intermediate-packaging-example/) 참조). 이전과 마찬가지로 **도움말 화면을 찾는 최소한의 테스트**를 만들 거예요:

```console
kali@kali:~/kali/packages/finalrecon$ mkdir -p debian/tests/
kali@kali:~/kali/packages/finalrecon$
kali@kali:~/kali/packages/finalrecon$ vim debian/tests/control
kali@kali:~/kali/packages/finalrecon$
kali@kali:~/kali/packages/finalrecon$ cat debian/tests/control
Test-Command: finalrecon -h
Restrictions: superficial
kali@kali:~/kali/packages/finalrecon$
```

## 의존성 완성하기

이론적으로, 누락된 `icmplib` 의존성을 제외하고는 이제 완전히 작동하는 패키지가 있어야 해요. 따라서 FinalRecon을 최종적으로 빌드하기 전에 `icmplib`를 패키징해야 해요.

## icmplib

### 패키지 이름 지정하기

이전 가이드([#1 (Instaloader)](/docs/development/intro-to-packaging-example/) & #2 (Photon))와 달리 **소스 패키지**와 **바이너리 패키지** 모두에 같은 이름을 사용했지만, 이번에는 다르게 할게요.

**바이너리 패키지의 명명 규칙은 `python3-<package>`**이며, 이는 기술적 수준에서 영향을 미치므로 따르는 것이 중요해요. 그러나 **소스 패키지는 `python-<package>`**(또는 그냥 `<package>`)일 수 있어요. 이를 따르지 않아도 문제는 없어요. 그러나 칼리 팀의 관점에서는 `python-<package>`를 선호하고 사용할 거예요. 자세한 내용은 [데비안 리소스](https://www.debian.org/doc/packaging-manuals/python-policy/ch-module_packages.html)를 참조하세요.

### 치트 시트 패키징

이 패키지는 `python3-setuptools`를 사용하는 간단한 패키지예요([첫 번째 가이드(Instaloader)](/docs/development/intro-to-packaging-example/)와 같이). 이 가이드가 너무 길어지지 않도록 `icmplib`에 대해 단계별로 설명하지는 않을게요.

파이썬 라이브러리 빌드에 대한 자세한 내용은 [데비안 리소스](https://wiki.debian.org/Python/LibraryStyleGuide)를 참조하세요.

다음은 **패키지 빌드에 필요한 명령어** 개요예요:

```plaintext
mkdir -p ~/kali/upstream/ ~/kali/build-area/ ~/kali/packages/python-icmplib/ 
wget https://github.com/ValentinBELYN/icmplib/archive/v1.2.2.tar.gz -O ~/kali/upstream/python-icmplib_1.2.2.orig.tar.gz
cd ~/kali/packages/python-icmplib/
git init
gbp import-orig ~/kali/upstream/python-icmplib_1.2.2.orig.tar.gz --no-interactive --debian-branch=kali/master
dh_make --file ~/kali/upstream/python-icmplib_1.2.2.orig.tar.gz -p python-icmplib_1.2.2 --python -y
rm -f debian/{*.docs,README*,*.ex,*.EX}
vim debian/changelog
vim debian/control
vim debian/copyright
vim debian/rules
vim debian/watch
gbp buildpackage --git-builder=sbuild --git-export=WC
sudo dpkg -i ~/kali/build-area/python3-icmplib_1.2.2-0kali1_all.deb
pip search icmplib
git add debian/
git commit -m "Initial release"
```

- - -

`debian/` 폴더의 주요 파일 내용 미리보기:

#### Changelog

간단하게, 다른 가이드들과 마찬가지로 #1 (Instaloader) & #2 (Photon), **버전**, **배포판**, **설명**을 편집해요.

참고로, `python-icmplib`는 `debian/control`의 소스 이름과 일치해야 해요:

```console
kali@kali:~/kali/packages/python-icmplib$ cat debian/changelog
python-icmplib (1.2.2-0kali1) kali-dev; urgency=medium

  * Initial release

 -- Joseph O'Gorman <gamb1t@kali.org>  Mon, 12 Oct 2020 18:10:27 -0400
kali@kali:~/kali/packages/python-icmplib$
```

#### Control

이것은 `Section: python`으로 이전에 본 것과 약간 달라요. 이는 이것이 **파이썬 라이브러리**이기 때문이에요. 자세한 내용은 [데비안 작성 가이드](https://www.debian.org/doc/debian-policy/ch-archive.html#s-subsections)와 [다양한 옵션](https://packages.debian.org/testing/)을 참조하세요.

또한 패키지 이름을 다르게 지정해야 해요. `debian/control`의 소스 패키지 부분은 상단 부분이며, `Source:` 필드로 이름이 지정돼요. 반면에 바이너리 부분은 하단 절반이고 `Package:`를 사용하여 이름을 지정해요. _가능한 경우 Kali Linux는 항상 소스 패키지와 바이너리 패키지를 모두 만들려고 해요(자세한 내용은 [데비안 리소스](https://wiki.debian.org/Packaging/SourcePackage) 참조)._

참고로, 소스 이름 `python-icmplib`는 `debian/changelog`와 일치해야 해요:

```console
kali@kali:~/kali/packages/python-icmplib$ cat debian/control
Source: python-icmplib
Section: python
Priority: optional
Maintainer: Kali Developers <devel@kali.org>
Uploaders: Joseph O'Gorman <gamb1t@kali.org>
Build-Depends: debhelper-compat (= 12),
                                dh-python,
                                python3-all,
                                python3-setuptools
Standards-Version: 4.5.0
Homepage: https://github.com/ValentinBELYN/icmplib
Vcs-Browser: https://gitlab.com/kalilinux/packages/python-icmplib
Vcs-Git: https://gitlab.com/kalilinux/packages/python-icmplib.git

Package: python3-icmplib
Architecture: all
Depends: ${python3:Depends}, ${misc:Depends}
Description: Python tool to forge ICMP packages
 icmplib is a brand new and modern implementation of the ICMP protocol in Python
 Able to forge ICMP packages to make your own ping, multiping, traceroute etc
kali@kali:~/kali/packages/python-icmplib$
```

#### 저작권

우리가 `orig.tar.gz`의 이름을 바꿨기 때문에, 업스트림 이름이 잘못되었어요. 일반적으로 앞에 `python3-`가 붙지 않을 거예요. 이 정보는 **소스 URL**에서 얻을 수 있어요:

```console
kali@kali:~/kali/packages/python-icmplib$ cat debian/copyright
Format: https://www.debian.org/doc/packaging-manuals/copyright-format/1.0/
Upstream-Name: icmplib
Upstream-Contact: Valentin BELYN <valentin-hello@gmx.com>
Source: https://github.com/ValentinBELYN/icmplib

Files: *
Copyright: 2020 Valentin BELYN <valentin-hello@gmx.com>
License: LGPL-3+

Files: debian/*
Copyright: 2020 Joseph O'Gorman <gamb1t@kali.org>
License: LGPL-3+

License: LGPL-3+
 This program is free software; you can redistribute it and/or modify it
 under the terms of the GNU Lesser General Public License as
 published by the Free Software Foundation; either version 3 of
 the License, or (at your option) any later version.
 .
 This program is distributed in the hope that it will be useful, but
 WITHOUT ANY WARRANTY; without even the implied warranty of
 MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the GNU
 Lesser General Public License for more details.
 .
 You should have received a copy of the GNU Lesser General Public
 License along with this program; if not, see <https://www.gnu.org/licenses/>.
 .
 On Debian systems, the full text of the GNU Lesser General Public
 License version 3 can be found in the file
 `/usr/share/common-licenses/LGPL-3'.
kali@kali:~/kali/packages/python-icmplib$
```

#### Rules

`PYBUILD_NAME`에서는 앞에 붙는 `python-` 부분을 빼야 해요. 바이너리 패키지(debian/control에 정의된)는 `python3-icmplib`로 생성되지만, [PyBuild](https://wiki.debian.org/Python/Pybuild)는 **순수한 Python 모듈 이름만 원하기 때문**이에요:

```console
kali@kali:~/kali/packages/python-icmplib$ cat debian/rules
#!/usr/bin/make -f
#export DH_VERBOSE = 1
export PYBUILD_NAME=icmplib

%:
	dh $@ --with python3 --buildsystem=pybuild
kali@kali:~/kali/packages/python-icmplib$
```

#### Watch

간단하게, 다른 모든 가이드([#1 (Instaloader)](/docs/development/intro-to-packaging-example/) & [#2 (Photon)](/docs/development/intermediate-packaging-example/))와 마찬가지로, **[GitHub용 표준 데비안 watch 파일](https://wiki.debian.org/debian/watch)**을 사용해요:

```console
kali@kali:~/kali/packages/python-icmplib$ cat debian/watch
version=4
opts=filenamemangle=s/.+\/v?(\d\S+)\.tar\.gz/icmplib-$1\.tar\.gz/ \
  https://github.com/ValentinBELYN/icmplib/tags .*/v?(\d\S+)\.tar\.gz
kali@kali:~/kali/packages/python-icmplib$
```

- - -
Python 3 라이브러리 파일인 icmplib 구축에 성공했어요!

#### 최종 FinalRecon 빌드

현재 `python3-icmplib`가 Kali Linux에 아직 수락되지 않았거나, 두 패키지를 동시에 제출하고 싶을 수 있어요. 이런 경우 **최근 생성된 패키지를 chroot에 포함**시켜서 `sbuild`가 사용할 수 있게 할 수 있어요. 이는 FinalRecon의 필수 요구 사항이에요.

또한 패키지의 상태가 확실하지 않기 때문에, 최신 편집 내용을 Git에 커밋하지 않기로 결정할 수도 있어요. 따라서 **패키지를 빌드할 때** `--git-export=WC`를 추가할 거예요:

```console
kali@kali:~/kali/packages/python-icmplib$ cd ~/kali/packages/finalrecon/
kali@kali:~/kali/packages/finalrecon$
kali@kali:~/kali/packages/finalrecon$ gbp buildpackage \
  --git-builder=sbuild --git-export=WC \
  --extra-package=$HOME/kali/build-area/python3-icmplib_1.2.2-0kali1_all.deb
[...]
kali@kali:~/kali/packages/finalrecon$
kali@kali:~/kali/packages/finalrecon$ ls -lah ~/kali/build-area/finalrecon_*.deb
-rw-rw-r-- 1 kali kali 83K Nov  8 07:44 /home/kali/kali/build-area/finalrecon_0.0~git20201107.0d41eb6-0kali1_all.deb
kali@kali:~/kali/packages/finalrecon$
```

- - -

새로 생성한 패키지를 테스트하기 전에, `debian/control`에 몇 가지 의존성을 나열했다는 것을 기억해야 해요(패키지를 빌드하는 데 필요한 것뿐만 아니라 실행하는 데 필요한 것도). `dpkg`를 사용하면 이러한 요구 사항이 자동으로 충족되지 않으므로 먼저 수동으로 설치해야 해요. **운영 체제에서 누락된 것**을 다음과 같이 확인할 수 있어요:

```console
kali@kali:~/kali/packages/finalrecon$ dpkg-checkbuilddeps
dpkg-checkbuilddeps: error: Unmet build dependencies: python3-ipwhois python3-dnslib python3-aiohttp python3-aiodns python3-psycopg2 python3-tldextract
kali@kali:~/kali/packages/finalrecon$
kali@kali:~/kali/packages/finalrecon$ sudo apt -y build-dep .
[...]
kali@kali:~/kali/packages/finalrecon$
```

{{% notice info %}}
패키지 설치에 실패하면 다음과 같은 문제가 발생할 수 있어요:

```console
kali@kali:~/kali/packages/finalrecon$ sudo dpkg -i ~/kali/build-area/finalrecon_*.deb
(Reading database ... 167589 files and directories currently installed.)
Preparing to unpack .../finalrecon_0.0~git20201107.0d41eb6-0kali1_all.deb ...
Unpacking finalrecon (0.0~git20201107.0d41eb6-0kali1) over (0.0~git20201107.0d41eb6-0kali1) ...
dpkg: dependency problems prevent configuration of finalrecon:
 finalrecon depends on python3-ipwhois; however:
  Package python3-ipwhois is not installed.
 finalrecon depends on python3-dnslib; however:
  Package python3-dnslib is not installed.
 finalrecon depends on python3-aiohttp; however:
  Package python3-aiohttp is not installed.
 finalrecon depends on python3-aiodns; however:
  Package python3-aiodns is not installed.
 finalrecon depends on python3-psycopg2; however:
  Package python3-psycopg2 is not installed.
 finalrecon depends on python3-tldextract; however:
  Package python3-tldextract is not installed.

dpkg: error processing package finalrecon (--install):
 dependency problems - leaving unconfigured
Processing triggers for kali-menu (2020.3.2) ...
Errors were encountered while processing:
 finalrecon
kali@kali:~/kali/packages/finalrecon$
```

- - -

그런 다음 다음에 패키지를 설치하거나 업데이트하려고 할 때 실패하는 문제가 발생해요:

```console
kali@kali:~/kali/packages/finalrecon$ sudo apt upgrade
Reading package lists... Done
Building dependency tree
Reading state information... Done
You might want to run 'apt --fix-broken install' to correct these.
The following packages have unmet dependencies:
 finalrecon : Depends: python3-ipwhois but it is not installed
              Depends: python3-dnslib but it is not installed
              Depends: python3-aiohttp but it is not installed
              Depends: python3-aiodns but it is not installed
              Depends: python3-psycopg2 but it is not installed
              Depends: python3-tldextract but it is not installed
E: Unmet dependencies. Try 'apt --fix-broken install' with no packages (or specify a solution).
kali@kali:~/kali/packages/finalrecon$
```

- - -

제안하는 대로 `sudo apt --fix-broken install`을 실행하면 보통 문제가 해결돼요.
{{% /notice %}}

- - -

패키지가 빌드되고 의존성이 설치되었어요. 이제 **마침내 FinalRecon**을 설치할 차례예요:

```console
kali@kali:~/kali/packages/finalrecon$ sudo dpkg -i ~/kali/build-area/finalrecon_*.deb
[...]
kali@kali:~/kali/packages/finalrecon$
kali@kali:~/kali/packages/finalrecon$ dpkg -l | grep final
ii  finalrecon                           0.0~git20201107.0d41eb6-0kali1               all          A fast and simple python script for web reconnaissance
kali@kali:~/kali/packages/finalrecon$
```

{{% notice info %}}
다음과 같이 설치할 수도 있어요:

```console
kali@kali:~/kali/packages/finalrecon$ sudo debi --debs-dir ~/kali/build-area/
```

{{% /notice %}}

- - -

**FinalRecon을 패키지로 성공적으로** 빌드했어요!

이제 **정상 작동**하는지 테스트해볼까요:

```console
kali@kali:~/kali/packages/finalrecon$ finalrecon
usage: finalrecon [-h] [--headers] [--sslinfo] [--whois] [--crawl] [--dns] [--sub] [--trace] [--dir] [--ps] [--full] [-t T] [-T T] [-w W] [-r] [-s]
                  [-sp SP] [-d D] [-e E] [-m M] [-p P] [-tt TT] [-o O]
                  url
finalrecon: error: the following arguments are required: url
kali@kali:~/kali/packages/finalrecon$
```

## 작업 저장하기

이제 지금까지 진행한 **작업을 저장**할 수 있어요:

```console
kali@kali:~/kali/packages/finalrecon$ git add debian/
kali@kali:~/kali/packages/finalrecon$
kali@kali:~/kali/packages/finalrecon$ git commit -m "Initial release"
[kali/master d1c9f75] Initial release
 12 files changed, 169 insertions(+)
 create mode 100644 debian/changelog
 create mode 100644 debian/control
 create mode 100644 debian/copyright
 create mode 100644 debian/finalrecon.install
 create mode 100644 debian/links
 create mode 100644 debian/patches/disable-requirements-check.patch
 create mode 100644 debian/patches/disable-ver_check.patch
 create mode 100644 debian/patches/series
 create mode 100755 debian/rules
 create mode 100644 debian/source/format
 create mode 100644 debian/tests/control
 create mode 100644 debian/watch
kali@kali:~/kali/packages/finalrecon$
kali@kali:~/kali/packages/finalrecon$ git status
On branch kali/master
nothing to commit, working tree clean
kali@kali:~/kali/packages/finalrecon$
```

- - -

이제 패키징을 완료할 수 있으며, 이러한 패키지를 추가하기 위해 **[Kali Linux 버그 트래커](https://bugs.kali.org/)에 요청을 제출**할 수 있어요!

## Kali 팀의 메시지

패키징 과정에서 도구 작성자(업스트림)와 협력하여 프로그램이 [FHS에 더 잘 맞도록](https://tldp.org/LDP/Linux-Filesystem-Hierarchy/html/) 만들었어요. 예를 들어, `/usr/share/finalrecon/dumps`를 사용하지 않는데, 이는 `/usr/share`에 쓰기 위해서는 루트 권한이 필요하고 여기에 사용자 파일이 저장되는 것을 원치 않기 때문이에요.