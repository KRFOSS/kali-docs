---
title: 패키징 입문 단계별 예제
description:
icon:
weight: 11
author: ["gamb1t",]
번역: ["xenix4845"]
---

# Instaloader

[Instaloader](https://github.com/instaloader/instaloader/)는 하나의 의존성(파이썬의 `requests`)만 가진 **파이썬 3** 애플리케이션이에요. 이것은 비교적 간단한 패키지지만, 단순히 쉘 스크립트만 패키징하는 것보다는 복잡해요. 학습 기회와 단순함 때문에 이는 좋은 입문용 패키지가 돼요.

## Instaloader 코드 개요

우선 애플리케이션의 [깃허브 페이지](https://github.com/instaloader/instaloader/)를 살펴봐요. 몇 가지 눈에 띄는 사항들이 있어요:

![](instaloader-00.png)

여기서 나중에 유용할 정보들이 보여요:

- 이 도구는 `setup.py` 스크립트를 포함하고 있어요
- 릴리스가 있어요
- 라이선스는 MIT 기반이에요

나중에 각각에 대해 더 자세히 살펴볼 거예요. 지금은 알아두면 좋을 정보일 뿐이에요.

## 환경 설정하기

우리는 이미 [패키징 환경 설정에 관한 문서](/docs/development/setting-up-packaging-system/)를 따랐다고 가정할게요.

이제 이 패키지를 위한 디렉토리를 설정해 볼게요:

```console
kali@kali:~$ mkdir -p ~/kali/packages/instaloader/ ~/kali/upstream/
kali@kali:~$
```

패키지 빌드와 관련된 모든 것은 `~/kali/`를 사용할 거예요. 그 안에는 두 개의 하위 폴더가 있을 거예요:

- `packages/`는 우리가 만들 패키지의 소스 코드가 될 거예요
- `upstream/`은 애플리케이션 소스 코드의 압축 파일이 될 거예요 _(가능하면 이전에 보았던 태그 버전 릴리스를 사용하는 게 좋아요)_

## 태그 릴리스 다운로드하기

처음부터 새 패키지를 만들고 있기 때문에 패키징하려는 도구의 버전을 수동으로 다운로드할 거예요. 만약 패키지를 업데이트하는 중이라면(그리고 올바르게 패키징되었다면), 이 과정을 더 빠르게 해주는 방법이 있어요. 하지만 이 내용은 [다른 가이드](/docs/development/advanced-packaging-example/)에서 다룰 거예요.

[깃허브 릴리스 페이지](https://github.com/instaloader/instaloader/releases)로 가면 최신 버전(이 글을 쓰는 시점에서는 `4.4.4`)을 볼 수 있어요. 여기에는 `instaloader-v4.4.4-windows-standalone.zip`, `Source Code (zip)`, `Source Code (tar.gz)` 다운로드 옵션이 있어요. 우리는 `tar.gz` 옵션에 관심이 있어요.

`wget`을 사용해서 Debian 표준에 따라 소스 패키지의 이름을 적절하게 지정할 거예요(`.orig.tar.gz`에 주목하세요):

```console
kali@kali:~$ wget https://github.com/instaloader/instaloader/archive/refs/tags/v4.4.4.tar.gz  -O ~/kali/upstream/instaloader_4.4.4.orig.tar.gz
kali@kali:~$
```

소프트웨어에 태그 릴리스가 없거나 _(또는 한동안 릴리스가 없었다면)_, 최신 git 커밋을 사용할 수 있어요. 이 내용은 다른 가이드에서 다루고 있어요. 하지만 가능한 경우 태그 릴리스를 사용하는 것이 좋아요.

## 패키지 소스 코드 생성하기

패키지의 작업 위치로 경로를 변경해야 해요:

```console
kali@kali:~$ cd ~/kali/packages/instaloader/
kali@kali:~/kali/packages/instaloader$
```

이제 새로운 빈 git 저장소를 만들 거예요:

```console
kali@kali:~/kali/packages/instaloader$ git init
Initialized empty Git repository in /home/kali/kali/packages/instaloader/.git/
kali@kali:~/kali/packages/instaloader$
```

원한다면 "status"와 "log"를 보고 확인할 수 있어요:

```console
kali@kali:~/kali/packages/instaloader$ git status
On branch master

No commits yet

nothing to commit (create/copy files and use "git add" to track)
kali@kali:~/kali/packages/instaloader$
kali@kali:~/kali/packages/instaloader$ git log
fatal: your current branch 'master' does not have any commits yet
kali@kali:~/kali/packages/instaloader$
```

좋아요. 모든 것이 비어 있어요. 깨끗한 작업 영역이 준비되었어요.

이제 이전에 `wget`으로 다운로드한 파일을 사용하여 upstream 버전을 패키징 소스 코드로 가져올 수 있어요. 파일 이름 형식 덕분에 `gbp`는 패키지 이름으로 `instaloader`, 버전으로 `4.4.4`를 감지할 수 있어요. 기본값을 그대로 받아들이기 위해 Enter를 누르기만 하면 돼요:

```console
kali@kali:~/kali/packages/instaloader$ gbp import-orig ~/kali/upstream/instaloader_4.4.4.orig.tar.gz
What will be the source package name? [instaloader]
What is the upstream version? [4.4.4]
gbp:info: Importing '/home/kali/kali/upstream/instaloader_4.4.4.orig.tar.gz' to branch 'upstream'...
gbp:info: Source package is instaloader
gbp:info: Upstream version is 4.4.4
gbp:info: Successfully imported version 4.4.4 of /home/kali/kali/upstream/instaloader_4.4.4.orig.tar.gz
kali@kali:~/kali/packages/instaloader$
```

모든 것이 괜찮은지 확인하고 싶다면, 다시 한번 git을 사용할 수 있어요:

```console
kali@kali:~/kali/packages/instaloader$ git status
On branch master
nothing to commit, working tree clean
kali@kali:~/kali/packages/instaloader$
kali@kali:~/kali/packages/instaloader$ git log
commit 494f71875f10f8d1da69a8edf2fc75300e4485b9 (HEAD -> master, tag: upstream/4.4.4, upstream)
Author: Joseph O'Gorman <gamb1t@kali.org>
Date:   Thu Jul 2 18:44:38 2020 -0400

    New upstream version 4.4.4
kali@kali:~/kali/packages/instaloader$
kali@kali:~/kali/packages/instaloader$ git branch -v
* master       494f718 New upstream version 4.4.4
  pristine-tar 439fe30 pristine-tar data for instaloader_4.4.4.orig.tar.gz
  upstream     494f718 New upstream version 4.4.4
kali@kali:~/kali/packages/instaloader$
```

이제 master 브랜치에 자동 커밋이 생성되었어요 _(`*`로 표시된 현재 활성 브랜치)_, 그리고 다른 두 개의 브랜치도 함께 있어요:

- `pristine-tar`는 가져오기에서 생성된 메타데이터예요
- `upstream`은 우리의 패키지 수정 없이 애플리케이션의 소스 코드를 포함하고 있어요

우리는 칼리 패키지를 만들고 있는데, `master` 브랜치가 아닌 `kali/master` 브랜치를 사용해요. 그러니 브랜치를 바꾸죠:

```console
kali@kali:~/kali/packages/instaloader$ git checkout -b kali/master
Switched to a new branch 'kali/master'
kali@kali:~/kali/packages/instaloader$
kali@kali:~/kali/packages/instaloader$ git branch -D master
Deleted branch master (was 494f718).
kali@kali:~/kali/packages/instaloader$
kali@kali:~/kali/packages/instaloader$ git branch -v
* kali/master  494f718 New upstream version 4.4.4
  pristine-tar 439fe30 pristine-tar data for instaloader_4.4.4.orig.tar.gz
  upstream     494f718 New upstream version 4.4.4
kali@kali:~/kali/packages/instaloader$
```

이제 데비안 기반 패키지를 빌드하는 데 필요한 파일을 생성하고, 생성된 예제 파일들을 제거할 수 있어요. 이 과정에서 다음과 같은 옵션 중에서 선택하라는 메시지가 표시돼요:

- `Single binary` (단일 바이너리)
- `Arch-Independent` (아키텍처 독립적)
- `Library` (라이브러리)
- `Python` (파이썬)

간단하게 하기 위해 "**S**ingle"을 선택할 거예요. 그런 다음 화면에 보이는 내용을 `Y`로 수락하세요. 어떤 옵션을 언제 사용해야 하는지에 대한 자세한 정보가 필요하면 [dh_make의 매뉴얼 페이지](https://manpages.debian.org/jessie/dh-make/dh_make.8.en.html)를 참조하세요:

```console
kali@kali:~/kali/packages/instaloader$ dh_make --file ~/kali/upstream/instaloader_4.4.4.orig.tar.gz -p instaloader_4.4.4
Type of package: (single, indep, library, python)
[s/i/l/p]?
Maintainer Name     : Joseph O'Gorman
Email-Address       : gamb1t@kali.org
Date                : Thu, 02 Jul 2020 17:59:47 -0400
Package Name        : instaloader
Version             : 4.4.4
License             : blank
Package Type        : single
Are the details correct? [Y/n/q]
Currently there is not top level Makefile. This may require additional tuning
Done. Please edit the files in the debian/ subdirectory now.
kali@kali:~/kali/packages/instaloader$
kali@kali:~/kali/packages/instaloader$ rm debian/*.docs debian/README* debian/*.ex debian/*.EX
kali@kali:~/kali/packages/instaloader$
kali@kali:~/kali/packages/instaloader$ rm -r debian/upstream
kali@kali:~/kali/packages/instaloader$
```

`--file`을 사용해서 `orig.tar.gz` 파일의 위치를 지정해요. 파일이 한 디렉토리 뒤에 있었다면 (`../`), 이것이 필요하지 않았겠지만, 우리는 파일을 위한 별도의 위치를 만들었기 때문에 필요해요.

`dh_make`를 사용했을 때 어떤 파일이 생성되었는지 보고 싶다면, `git`을 사용할 수 있어요:

```console
kali@kali:~/kali/packages/instaloader$ git status
On branch kali/master
Untracked files:
  (use "git add <file>..." to include in what will be committed)
        debian/

nothing added to commit but untracked files present (use "git add" to track)
kali@kali:~/kali/packages/instaloader$
kali@kali:~/kali/packages/instaloader$ ls -R debian/
debian/:
changelog  control  copyright  rules  source

debian/source:
format
kali@kali:~/kali/packages/instaloader$
```

각 파일에 대한 간단한 개요는 다음과 같아요:

- `changelog` - 패키지가 업데이트될 때 추적해요(이유와 누가 했는지 포함). 이것이 패키지 버전을 담당해요
- `control` - 패키지의 메타데이터예요(`apt`와 함께 자주 볼 수 있어요)
- `copyright` - 어떤 것이 어떤 라이선스 하에 있는지 나타내요. 패키지는 우리가 패키지를 만드는 데 들인 작업과 다른 라이선스 하에 있을 수 있어요
- `rules` - 패키지를 설치하는 방법이에요
- `source/format` - 소스 패키지 형식이에요
- `control` - 패키지의 메타데이터예요(`apt`와 함께 자주 볼 수 있어요)
- `copyright` - 어떤 것이 어떤 라이선스 하에 있는지 나타내요. 패키지는 우리가 패키지를 만드는 데 들인 작업과 다른 라이선스 하에 있을 수 있어요
- `rules` - 패키지를 설치하는 방법이에요
- `source/format` - 소스 패키지 형식이에요

이 시점에서 기본 패키징 파일이 준비되었고, 실제 작업을 시작하기 전에 커밋하는 것이 좋을 것 같아요:

```console
kali@kali:~/kali/packages/instaloader$ git add debian/
kali@kali:~/kali/packages/instaloader$
kali@kali:~/kali/packages/instaloader$ git commit -m "Initial packaging files"
[kali/master 52042da] Initial packaging files
 5 files changed, 93 insertions(+)
 create mode 100644 debian/changelog
 create mode 100644 debian/control
 create mode 100644 debian/copyright
 create mode 100755 debian/rules
 create mode 100644 debian/source/format
kali@kali:~/kali/packages/instaloader$
```

이제 정보가 정확한지 확인하기 위해 이들 대부분을 편집해야 해요. GitHub에서 찾은 내용을 활용하여 `debian/` 파일에 올바른 정보를 제공할 수 있어요:

- 라이선스
- 의존성
- 관리자
- 설명

## 정보 수집하기

### 라이선스/관리자

이 패키지의 경우 매우 간단해요. 깃허브가 도움을 주었고, [라이선스](https://github.com/instaloader/instaloader/blob/master/LICENSE)를 MIT로 감지했어요. 또한 라이선스 파일도 있다는 것을 확인할 수 있어요:

```console
kali@kali:~/kali/packages/instaloader$ cat LICENSE
The MIT License (MIT)

Copyright (c) 2016-2019 Alexander Graf and André Koch-Kramer.
[...]
kali@kali:~/kali/packages/instaloader$
```

라이선스를 읽어보면 `Alexander Graf`와 `André Koch-Kramer` 두 명의 저자가 있다는 것을 알 수 있어요. 하지만 그들에게 연락할 방법은 없어요. 저자들에게 크레딧을 줄 수 있도록 더 많은 저자를 알려줄 수 있는 무언가를 찾기 위해 나머지 git 저장소를 계속 탐색해요. 정해진 구조는 없지만, 확인하고 주의해야 할 몇 가지 사항이 있어요:

- `README*` - 저자들이 연락처 정보를 여기에 넣을 수 있어요
    - 몇 가지 예: `README`, `README.txt`, `README.MD` `README.MKDOCS`, 또는 `Readme.txt`
- `AUTHOR*` - 저자 정보를 위한 전용 파일이 있을 수 있어요
- `CREDIT*` - 누구에게 크레딧을 주는지에 대한 전용 파일이 있을 수 있어요
- `LICENSE*` - 위에서 언급한 것처럼, 라이선스 파일에 저자 정보가 있을 수 있어요
- `docs/` - 모든 문서를 별도의 폴더에 배치할 수 있어요
- 애플리케이션의 "주요" 시작점에는 파일 맨 위에 주석이 있을 수 있어요 - 이 경우 `instaloader.py`
- Git 커밋 - `git --no-pager log -s --format="%ae" | sort -u`

우리 패키지의 경우, 다음과 같은 것들을 볼 수 있어요:

```console
kali@kali:~/kali/packages/instaloader$ ls
AUTHORS.md  debian  deploy  docs  instaloader  instaloader.py  LICENSE  Pipfile  Pipfile.lock  README.rst  setup.py  test
kali@kali:~/kali/packages/instaloader$
kali@kali:~/kali/packages/instaloader$ ls docs/
as-module.rst    codesnippets      contributing.rst  installation.rst  logo.svg   requirements.txt             _templates
basic-usage.rst  codesnippets.rst  favicon.ico       logo_heading.png  Makefile   sphinx_autodoc_typehints.py  troubleshooting.rst
cli-options.rst  conf.py           index.rst         logo.png          README.md  _static
kali@kali:~/kali/packages/instaloader$
```

보시다시피, `AUTHORS.md`, `docs/`, `instaloader.py`, 그리고 `README.rst`가 있어서 살펴볼 곳이 몇 군데 있어요. `AUTHORS.md`부터 시작하면, 저자의 이름과 연락 방법을 볼 수 있어요:

```console
kali@kali:~/kali/packages/instaloader$ cat AUTHORS.md
Authors
=======

Instaloader is written by

- Alexander Graf (@aandergr)
- André Koch-Kramer (@Thammus)
- Lars Lindqvist (@e5150)
kali@kali:~/kali/packages/instaloader$
```

이메일 주소가 아니라 사용자 이름인 것 같아요(깃허브용일 수도 있고, 일반적인 인터넷 핸들일 수도 있어요). 이것만으로도 계속 진행할 수 있어요(이상적이진 않지만).

시도해볼 수 있는 또 다른 방법은 그들이 git에서 "정당한" 이메일 주소를 사용했는지 확인하는 거예요:

```console
kali@kali:~/kali/packages/instaloader$ git clone https://github.com/instaloader/instaloader/ /tmp/instaloader
kali@kali:~/kali/packages/instaloader$
kali@kali:~/kali/packages/instaloader$ cd /tmp/instaloader/
kali@kali:/tmp/instaloader$ git --no-pager log -s --format="%ae" | sort -u | grep -v '@users.noreply.github.com'
[...]
kali@kali:/tmp/instaloader$
kali@kali:/tmp/instaloader$ cd ~/kali/packages/instaloader/
kali@kali:~/kali/packages/instaloader$
```

그렇지 않은 것 같아요. 시도해볼 가치는 있었어요!

### 의존성/관리자

애플리케이션이 작동하기 위해 어떤 것이 머신에 설치되어야 하는지 확인해야 해요. 미리 설치되어 있거나 애플리케이션을 사용하여 설치될 수 있어요.

이 정보를 찾아볼 수 있는 몇 가지 시작점:

- `README*`
- `SETUP*`
- `INSTALL*`
- `docs/`

이 애플리케이션에는 README가 있지만, 소스에서 빌드/컴파일하는 방법이 아니라 애플리케이션을 설치하는 방법만 알려주고 있어요:

```console
kali@kali:~/kali/packages/instaloader$ grep -C 3 -i install README.rst

::

    $ pip3 install instaloader

    $ instaloader profile [profile ...]

kali@kali:~/kali/packages/instaloader$
```

[pip 옵션](https://pypi.org/project/instaloader/)을 살펴볼 수도 있지만, 이 가이드의 범위를 벗어나요.

다음으로 많은 유용한 정보가 포함된 `setup.py`를 발견했어요:

```console
kali@kali:~/kali/packages/instaloader$ cat setup.py
#!/usr/bin/env python3
[...]
if sys.version_info < (3, 5):
    sys.exit('Instaloader requires Python >= 3.5.')

requirements = ['requests>=2.4']

if platform.system() == 'Windows' and sys.version_info < (3, 6):
    requirements.append('win_unicode_console')
[...]
    url='https://instaloader.github.io/',
    license='MIT',
    author='Alexander Graf, André Koch-Kramer',
    author_email='mail@agraf.me, koch-kramer@web.de',
    description='Download pictures (or videos) along with their captions and other metadata '
                'from Instagram.',
    long_description=open(os.path.join(SRC, 'README.rst')).read(),
    install_requires=requirements,
    python_requires='>=3.5',
[...]
kali@kali:~/kali/packages/instaloader$
```

이 파일에서 다음과 같은 정보를 얻을 수 있었어요:

- 셔뱅(shebang)을 통해 이것이 파이썬 3임을 알 수 있어요 (`#!/usr/bin/env python3`).
- 파이썬 3.5 이상이 필요하다는 것을 알 수 있어요
- `requests`가 필요하고 `v2.4` 이상이어야 한다는 것을 알 수 있어요
- 윈도우에서는 다른 의존성이 필요하지만, 우리는 리눅스이므로 해당 사항이 없어요
- 프로그램의 홈 URL을 볼 수 있어요
- 라이선스(MIT)를 볼 수 있어요
- 저자와 그들의 이메일 주소를 볼 수 있어요
- 프로그램에 대한 설명을 얻을 수 있어요

유용하네요!

패키징을 할 때, 오프라인에서도 설치할 수 있는 독립형 패키지를 만들고 있어요. 또한 파이썬의 pip나 Ruby의 gems와 같은 다른 시스템 패키지 관리 시스템도 염두에 두어야 해요. 이러한 모든 의존성도 주 OS 패키지 관리에 있어야 해요. 우리의 경우 파이썬의 `requests`가 필요해요.

이것을 검색하는 두 가지 방법이 있어요:

- [pkg.kali.org](https://pkg.kali.org/)
- `apt-cache`

하지만 우리가 무엇을 검색하고 있는지 알아야 해요. 명명 규칙이 있지만, 확실하지 않다면 여러 검색을 수행하는 것이 도움이 될 수 있어요:

- `requests`
- `python-requests`
- `python3-requests`

일단 지금은 명령줄 옵션을 고수할게요.

그냥 `requests`만 검색하면 너무 많은 결과가 나와요:

```console
kali@kali:~/kali/packages/instaloader$ apt-cache search requests | wc -l
561
kali@kali:~/kali/packages/instaloader$
```

그래서 설명의 **짧은 버전**만 검색하여 목록을 줄여야 해요(나중에 더 자세히 다루겠지만, 이는 출력의 보이는 부분이에요):

```console
kali@kali:~/kali/packages/instaloader$ apt-cache search --names-only requests | wc -l
19
kali@kali:~/kali/packages/instaloader$
kali@kali:~/kali/packages/instaloader$ apt-cache search --names-only python-requests
python-requests-cache-doc - persistent cache for requests library (doc)
python-requests-doc - elegant and simple HTTP library for Python (Documentation)
python-requests-mock-doc - mock out responses from the requests package - doc
python-requests-oauthlib-doc - module providing OAuthlib auth support for requests (Common Documentation)
python-requests-toolbelt-doc - Utility belt for python3-requests (documentation)
kali@kali:~/kali/packages/instaloader$
kali@kali:~/kali/packages/instaloader$ apt-cache search --names-only python-requests | grep -vi 'doc'
kali@kali:~/kali/packages/instaloader$
```

결과에서 문서를 제거한 후에는 결과를 얻지 못했어요. 그래서 다음 검색을 진행해 볼게요:

```console
kali@kali:~/kali/packages/instaloader$ apt-cache search --names-only python3-requests
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
kali@kali:~/kali/packages/instaloader$
```

첫 번째 결과인 `python3-requests`가 정확히 맞는 것 같아요! 더 자세히 살펴볼게요:

```console
kali@kali:~/kali/packages/instaloader$ apt-cache show python3-requests
Package: python3-requests
Source: requests
Version: 2.23.0+dfsg-2
[...]
kali@kali:~/kali/packages/instaloader$
```

그리고 버전이 `2.23.0`인 것을 볼 수 있는데, 이는 `2.4`보다 높으므로 패키지를 업데이트할 필요가 없어요. 필요할 때 다른 가이드에서 다룰 거예요.

### 관리자

다른 부분을 진행하면서 이미 소프트웨어의 저자와 관리자를 발견했기 때문에 이에 대해 추가 작업을 할 필요가 없어요.

### 설명

우리는 두 가지 설명을 제공해야 해요: 긴 설명과 짧은 설명. 깃허브 페이지를 보면 짧은 설명에 사용할 수 있는 정보 섹션이 있어요. 긴 설명에는 README에 있는 설명을 사용할 수 있어요.

![](instaloader-01.png)

또한 `setup.py`에서도 값을 얻었어요.

## 패키지 소스 코드 편집하기

이제 이 정보를 복사했으니 `dh_make`로 생성한 `debian/` 폴더의 파일을 채우기 시작할 수 있어요.

이 주제에 대한 자세한 정보는 [Debian 문서](https://www.debian.org/doc/manuals/maint-guide/dreq.en.html)에서 찾을 수 있어요.

### Changelog

[패키징 환경 설정](/docs/development/setting-up-packaging-system/) 문서를 따랐다면, 변경해야 할 값은 **배포판**(distribution, `UNRELEASED`에서 `kali-dev`로), **버전**(`4.4.4-1`에서 `4.4.4-0kali1`로), 그리고 **로그 항목**뿐이에요:

```console
kali@kali:~/kali/packages/instaloader$ cat debian/changelog
instaloader (4.4.4-1) UNRELEASED; urgency=medium

  * Initial release (Closes: #nnnn)  <nnnn is the bug number of your ITP>

 -- Joseph O'Gorman <gamb1t@kali.org>  Thu, 02 Jul 2020 17:59:47 -0400
kali@kali:~/kali/packages/instaloader$
kali@kali:~/kali/packages/instaloader$ vim debian/changelog
kali@kali:~/kali/packages/instaloader$
kali@kali:~/kali/packages/instaloader$ cat debian/changelog
instaloader (4.4.4-0kali1) kali-dev; urgency=medium

  * Initial release

 -- Joseph O'Gorman <gamb1t@kali.org>  Thu, 02 Jul 2020 17:59:47 -0400
kali@kali:~/kali/packages/instaloader$
```

### Control

이 파일은 패키지의 메타데이터이며, 많은 정보를 포함하고 있어요.

이 주제에 대한 자세한 정보는 [데비안 문서](https://www.debian.org/doc/manuals/maint-guide/dreq.en.html)에서 찾을 수 있어요.

기본적으로, 이 파일은 다음과 같이 생겼어요:

```console
kali@kali:~/kali/packages/instaloader$ cat debian/control
Source: instaloader
Section: unknown
Priority: optional
Maintainer: Joseph O'Gorman <gamb1t@kali.org>
Rules-Requires-Root: no
Build-Depends:
 debhelper-compat (= 13)
Standards-Version: 4.6.1
Homepage: <insert the upstream URL, if relevant>
#Vcs-Browser: https://salsa.debian.org/debian/instaloader
#Vcs-Git: https://salsa.debian.org/debian/instaloader.git

Package: instaloader
Architecture: any
Depends:
 ${misc:Depends},
 ${shlibs:Depends},
Description: <insert up to 60 chars description>
 <Insert long description, indented with spaces.>
kali@kali:~/kali/packages/instaloader$
```

여기서 업데이트가 필요한 몇 가지 사항을 확인할 수 있어요:

- `Section` - misc로 설정하거나, [데비안 테스팅](https://packages.debian.org/testing/)의 섹션을 기반으로 다른 섹션이라고 확실히 알고 있다면 해당 섹션으로 설정할 수 있어요
- `Maintainer` - 개인이 아닌 칼리 팀으로 변경해요
- `Uploaders` - 애플리케이션 패키징을 담당하는 개인(들)이에요
- `Build-Depends` - 패키지를 빌드하는 데 필요한 패키지들이에요
- `Homepage` - 인터넷에서 도구가 위치한 곳이에요
- `Vcs-Browser` - 온라인에서 볼 수 있는 패키지 소스 코드예요
- `Vcs-Git` - 패키지 소스 코드 위치예요
- `Architecture` - 어떤 기계에서 작동할 수 있는지에 대한 정보예요
- `Depends` - 이 패키지가 작동하는 데 필요한 다른 패키지들이에요
- `Description` - 짧은 설명과 긴 설명이에요

이전에 이미 대부분을 파악했으므로 이제 쉽게 채워넣을 수 있어요. 깃랩 계정에 빈 원격 git 저장소를 만들었어요. 우리 예제에서는 결과가 다음과 같아요:

```console
kali@kali:~/kali/packages/instaloader$ vim debian/control
kali@kali:~/kali/packages/instaloader$
kali@kali:~/kali/packages/instaloader$ cat debian/control
Source: instaloader
Section: misc
Priority: optional
Maintainer: Kali Developers <devel@kali.org>
Uploaders: Joseph O'Gorman <gamb1t@kali.org>
Rules-Requires-Root: no
Build-Depends:
 debhelper-compat (= 13),
 dh-python,
 python3-all,
 python3-requests,
 python3-setuptools,
Standards-Version: 4.6.1
Homepage: https://instaloader.github.io/
Vcs-Browser: https://gitlab.com/kalilinux/packages/instaloader
Vcs-Git: https://gitlab.com/kalilinux/packages/instaloader.git

Package: instaloader
Architecture: all
Depends:
 python3-requests,
 ${misc:Depends},
 ${python3:Depends},
Description: Download media along with their metadata from Instagram
 Downloads public and private profiles, hashtags, user stories, feeds
   and saved media
 Downloads comments, geotags and captions of each post.
 Automatically detects profile name changes and renames the target
   directory accordingly
 Allows fine-grained customization of filters and where to store
   downloaded media
kali@kali:~/kali/packages/instaloader$
```
참고: `Build-Depends`와 `Depends`는 한 칸 들여쓰기(그리고 쉼표로 끝남)되어 있어요. `Description`도 한 칸 들여쓰기되어 있어요.

여기에는 많은 내용이 있으니, 몇 가지 사항을 짚어볼게요.

**긴 설명**의 서식을 지정할 때 명심해야 할 점은, 약 70자마다(가장 가까운 단어 기준으로) 새 줄을 삽입해야 한다는 거예요. 이렇게 하면 서식을 깔끔하게 유지할 수 있어요.

이제 의존성으로 넘어가볼게요. 여기에는 **빌드**와 **패키지** 의존성이 있어요. 파이썬 3의 빌드 의존성은 네 가지가 필요해요:

- `debhelper-compat`
- `dh-python`
- `python3-all`
- `python3-setuptools`

별도의 가이드에서 이들이 포함되는 이유에 대한 설명이 있을 거예요. 하지만 앞의 두 가지만 파이썬 3 패키징의 기본 요소이고, 나머지 두 가지는 더 특정한 경우에 사용돼요.

우리 애플리케이션에는 `setup.py`에서 가져온 또 다른 의존성인 `python3-requests`가 있어요. 이는 애플리케이션에서 요구하는 사항이에요. 일반적으로 `setup.py` 파일이 없다면 "Build-Depends"에 `python3-requests`를 포함할 필요가 없어요. 그러나 `setup.py` 파일이 있기 때문에, "Build-Depends"와 패키지 "Depends" 모두에 `python3-requests`를 포함해야 해요. 이렇게 하면 패키지를 설치할 때 이러한 패키지들이 항상 시스템에 존재하게 돼요(특히 "sbuild"를 사용할 때 편리해요).

`debhelper-compat` 수준은 패키지가 빌드되는 방식을 결정해요. 호환성 수준이 높을수록 더 새로운 버전이에요. 새 버전은 일부 단순 작업을 자동으로 처리하므로, 이 값을 낮추지 않는 것이 좋아요.

패키지 의존성은 비교적 간단해요. Python 도구를 패키징하고 있으므로 `${shlibs:Depends}`를 제거하고, 대신 파이썬3 의존성 버전인 `${python3:Depends}`로 대체해요. 또한 도구에 필요하므로 `python3-requests`도 포함시켜요. 이 도구에는 다른 의존성이 필요하지 않으므로 이것으로 충분해요.

마지막으로 아키텍처를 **any**에서 **all**로 변경해야 해요. 이 도구는 모든 아키텍처에 설치될 수 있기 때문이에요.

### 저작권

모든 생성물에는 원저작자가 있어요. 그들은 작품에 대한 제어권을 가지고 있으며, 이를 존중해야 해요. 저작권 파일에서 이를 명시할 수 있어요.

이 주제에 대한 자세한 정보는 [데비안 문서](https://www.debian.org/doc/manuals/maint-guide/dreq.en.html)와 [여기](https://www.debian.org/doc/packaging-manuals/copyright-format/1.0/)에서 찾을 수 있어요.

아래는 스켈레톤 템플릿 출력(주석 제거됨)이에요:

```console
kali@kali:~/kali/packages/instaloader$ grep -v '#' debian/copyright
Format: https://www.debian.org/doc/packaging-manuals/copyright-format/1.0/
Source: <url://example.com>
Upstream-Name: instaloader
Upstream-Contact: <preferred name and address to reach the upstream project>

Files:
 *
Copyright:
 <years> <put author's name and email here>
 <years> <likewise for another author>
License: <special license>
 <Put the license of the package here indented by 1 space>
 <This follows the format of Description: lines in control file>
 .
 <Including paragraphs>

Files:
 debian/*
Copyright:
 2020 Joseph O'Gorman <gamb1t@kali.org>
License: GPL-2+
 This package is free software; you can redistribute it and/or modify
 it under the terms of the GNU General Public License as published by
 the Free Software Foundation; either version 2 of the License, or
 (at your option) any later version.
 .
 This package is distributed in the hope that it will be useful,
 but WITHOUT ANY WARRANTY; without even the implied warranty of
 MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
 GNU General Public License for more details.
 .
 You should have received a copy of the GNU General Public License
 along with this program. If not, see <https://www.gnu.org/licenses/>
Comment:
 On Debian systems, the complete text of the GNU General
 Public License version 2 can be found in "/usr/share/common-licenses/GPL-2".

kali@kali:~/kali/packages/instaloader$
```

원래 도구의 저자는 자신의 작업에 대한 소유권을 가지고 있고, 우리가 패키지를 만드는 데 들인 작업은 우리에게 속해요.
업데이트 후에는 다음과 같이 보여요:

```console
kali@kali:~/kali/packages/instaloader$ vim debian/copyright
kali@kali:~/kali/packages/instaloader$
kali@kali:~/kali/packages/instaloader$ cat debian/copyright
Format: https://www.debian.org/doc/packaging-manuals/copyright-format/1.0/
Source: https://github.com/instaloader/instaloader
Upstream-Name: instaloader

Files: *
Copyright:
 2016-2020 Alexander Graf <mail@agraf.me>
 2016-2020 André Koch-Kramer <koch-kramer@web.de>
License: MIT

Files: debian/*
Copyright: 2020 Joseph O'Gorman <gamb1t@kali.org>
License: MIT

License: MIT
 The MIT License
 Permission is hereby granted, free of charge, to any person obtaining a copy
 of this software and associated documentation files (the "Software"), to deal
 in the Software without restriction, including without limitation the rights to
 use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies
 of the Software, and to permit persons to whom the Software is furnished to do
 so, subject to the following conditions:
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

kali@kali:~/kali/packages/instaloader$
```

다음과 같은 사항들을 변경했어요:

- 선택적 매개변수(`Upstream-Contact`)를 제거했어요. 이는 저작권 파일에서 이미 다뤄졌기 때문이에요.
- 애플리케이션의 홈페이지를 `Source`에 넣었어요
- `setup.py`에서 두 저자의 이름과 주소를 가져왔어요. 날짜는 `LICENSE` 파일에서 가져왔어요
- MIT 라이선스 텍스트 전체를 바로 뒤에 두는 대신, 파일 끝 부분에 배치하고 제목을 붙였어요.
- 패키징 섹션의 기본값으로 사용된 `GPL-2+`를 애플리케이션에서 사용하는 것과 **동일한** `MIT` 라이선스로 대체했어요. 이것이 데비안 패키지의 표준이에요(패키징 작업은 애플리케이션의 라이선스와 일치해야 함).

### Rules

이 파일은 데비안 패키지를 빌드하기 위한 Makefile이에요.

이 주제에 대한 더 자세한 정보는 [데비안 문서](https://www.debian.org/doc/manuals/maint-guide/dreq.en.html)에서 찾을 수 있어요.

템플릿의 출력은 다음과 같이 생겼어요:

```console
kali@kali:~/kali/packages/instaloader$ cat debian/rules
#!/usr/bin/make -f

# See debhelper(7) (uncomment to enable).
# Output every command that modifies files on the build system.
#export DH_VERBOSE = 1


# See FEATURE AREAS in dpkg-buildflags(1).
#export DEB_BUILD_MAINT_OPTIONS = hardening=+all

# See ENVIRONMENT in dpkg-buildflags(1).
# Package maintainers to append CFLAGS.
#export DEB_CFLAGS_MAINT_APPEND  = -Wall -pedantic
# Package maintainers to append LDFLAGS.
#export DEB_LDFLAGS_MAINT_APPEND = -Wl,--as-needed


%:
	dh $@


# dh_make generated override targets.
# This is an example for Cmake (see <https://bugs.debian.org/641051>).
#override_dh_auto_configure:
#	dh_auto_configure -- \
#	-DCMAKE_LIBRARY_PATH=$(DEB_HOST_MULTIARCH)

kali@kali:~/kali/packages/instaloader$
```

댓글과 주석이 미리 주석 처리되어 있는 항목들이 많은데, 이것들은 디버깅과 문제 해결에 유용할 수 있어요.
(shebang)셔뱅(`#!/usr/bin/make -f`) 외에도 현재 사용 중인 두 줄이 더 있어요:

```plaintext
%:
	dh $@
```

이것은 와일드카드(`%`)이고, 모든 인수를 `dh`에 전달해요.

이제 여기에 들어갈 내용은 프로그램과 그 복잡성에 따라 달라져요. 우리 프로그램은 파이썬 애플리케이션이므로 `python3`로 빌드하도록 지시해야 해요. 또한 애플리케이션 소스에 `setup.py` 파일이 포함되어 있기 때문에 `pybuild`를 사용하여 빌드하도록 지시해야 해요. `setup.py` 파일이 없었다면 이 플래그를 추가할 필요가 없었을 거예요. 또한 PyBuild에 애플리케이션 이름을 알려줘야 해요. 다음과 같이 작성할 수 있어요:

```console
kali@kali:~/kali/packages/instaloader$ vim debian/rules
kali@kali:~/kali/packages/instaloader$
kali@kali:~/kali/packages/instaloader$ cat debian/rules
#!/usr/bin/make -f

#export DH_VERBOSE = 1
export PYBUILD_NAME = instaloader

%:
	dh $@ --with python3 --buildsystem=pybuild
kali@kali:~/kali/packages/instaloader$
```

NOTE: It uses TAB for indentation, as its a Makefile.

### Watch

추가로 포함하는 것을 강력히 권장하는 파일은 `watch` 파일이에요. 이 파일은 업스트림을 가리키며, 패키징된 것보다 더 최신 버전의 애플리케이션이 있는지 감지하는 데 사용돼요. 이는 패키지 업데이트 시에 유용해요.

자세한 정보와 예제 형식은 [데비안 위키](https://wiki.debian.org/debian/watch)를 참조하세요.
이 위키를 통해 깃허브에 대한 예제를 볼 수 있는데, 우리 프로젝트가 저장된 곳이 바로 [github.com/instaloader/instaloader/](https://github.com/instaloader/instaloader/)예요:

```plaintext
version=4
opts=filenamemangle=s/.+\/v?(\d\S+)\.tar\.gz/<project>-$1\.tar\.gz/ \
  https://github.com/<user>/<project>/tags .*/v?(\d\S+)\.tar\.gz
```

이제 우리의 필요에 맞게 수정해볼게요:

```console
kali@kali:~/kali/packages/instaloader$ vim debian/watch
kali@kali:~/kali/packages/instaloader$
kali@kali:~/kali/packages/instaloader$ cat debian/watch
version=4
opts=filenamemangle=s/.+\/v?(\d\S+)\.tar\.gz/instaloader-$1\.tar\.gz/ \
  https://github.com/instaloader/instaloader/tags .*/v?(\d\S+)\.tar\.gz
kali@kali:~/kali/packages/instaloader$
```

참고: 들여쓰기는 두 칸 띄어쓰기를 사용해요

그럼 제대로 작동하는지 빠르게 확인해볼게요:

```console
kali@kali:~/kali/packages/instaloader$ uscan -vv --no-download
[...]
uscan info: Found the following matching hrefs on the web page (newest first):
   /instaloader/instaloader/archive/v4.4.4rc3.tar.gz (4.4.4rc3) index=4.4.4rc3-1
   /instaloader/instaloader/archive/v4.4.4rc2.tar.gz (4.4.4rc2) index=4.4.4rc2-1
   /instaloader/instaloader/archive/v4.4.4rc1.tar.gz (4.4.4rc1) index=4.4.4rc1-1
   /instaloader/instaloader/archive/v4.4.4.tar.gz (4.4.4) index=4.4.4-1
   /instaloader/instaloader/archive/v4.4.3.tar.gz (4.4.3) index=4.4.3-1
[...]
    $newversion  = 4.4.4rc3
    $lastversion = 4.4.4
[...]
uscan: Newest version of instaloader on remote site is 4.4.4rc3, local version is 4.4.4
uscan:    => Newer package available from
      https://github.com/instaloader/instaloader/archive/v4.4.4rc3.tar.gz
uscan info: Scan finished
kali@kali:~/kali/packages/instaloader$
```

제대로 작동하지 않네요! 모든 버전은 올바르게 감지했지만, 릴리스 후보(RC) 때문에 순서가 제대로 정렬되지 않았어요. 이는 [릴리스 페이지](https://github.com/instaloader/instaloader/tags)를 보면 알 수 있어요:

![](instaloader-02.png)

[데비안 위키](https://wiki.debian.org/debian/watch)를 다시 살펴보면 [일반적인 실수](https://wiki.debian.org/debian/watch#Common_mistakes)라는 섹션이 있어요:

> 알파, 베타 또는 릴리스 후보 버전을 최종 릴리스보다 먼저 정렬되도록 수정하지 않는 문제. 해결책은 다음과 같이 "uversionmangle"을 사용하는 것입니다:

```plaintext
opts=uversionmangle=s/(\d)[_\.\-\+]?((RC|rc|pre|dev|beta|alpha)\d*)$/$1~$2/
```

하지만 Instaloader에 맞게 약간 수정해야 해요. 위 uversionmangle을 기반으로 시행착오를 통해 알아낼 수 있어요.

작동 방식을 확인해 볼게요:

```console
kali@kali:~/kali/packages/instaloader$ vim debian/watch
kali@kali:~/kali/packages/instaloader$
kali@kali:~/kali/packages/instaloader$ cat debian/watch
version=4
opts=uversionmangle=s/(\d)[_\.\-\+]?((RC|rc|pre|dev|beta|alpha|a)\d*)$// \
  https://github.com/instaloader/instaloader/tags .*/v?(\d\S+)\.tar\.gz
kali@kali:~/kali/packages/instaloader$
kali@kali:~/kali/packages/instaloader$ uscan -vv --no-download
[...]
uscan info: Newest version of instaloader on remote site is 4.4.4, local version is 4.4.4
uscan info:    => Package is up to date for from
      https://github.com/instaloader/instaloader/archive/v4.4.4.tar.gz
uscan info: Scan finished
kali@kali:~/kali/packages/instaloader$
```

성공이에요!

## .Install & Helper-Scripts

지금까지 우리가 한 모든 작업은 패키지를 빌드하기 위한 것이었지만, 애플리케이션을 어떻게 설치해야 하는지는 아직 말하지 않았어요:

```console
kali@kali:~/kali/packages/instaloader$ vim debian/instaloader.install
kali@kali:~/kali/packages/instaloader$
kali@kali:~/kali/packages/instaloader$ cat debian/instaloader.install
instaloader.py usr/share/instaloader/
instaloader usr/share/instaloader/
kali@kali:~/kali/packages/instaloader$
```

참고: 대상 디렉토리에 앞에 슬래시(/)가 없어요

이대로 진행할 수 있지만, 예상대로 작동하지 않을 수 있어요. 이는 `$PATH`에 아무것도 없기 때문에, 명령줄에서 `instaloader.py`를 입력해도 작동하지 않을 거예요(또한 파일 확장자 `.py`가 붙어 있어요). 해결책은 `$PATH`에 위치하는 **헬퍼 스크립트**를 만드는 것이에요(그리고 이를 `.install` 파일에 포함시켜요):

```console
kali@kali:~/kali/packages/instaloader$ mkdir -p debian/helper-script/
kali@kali:~/kali/packages/instaloader$
kali@kali:~/kali/packages/instaloader$ vim debian/helper-script/instaloader
kali@kali:~/kali/packages/instaloader$
kali@kali:~/kali/packages/instaloader$ cat debian/helper-script/instaloader
#!/bin/sh
exec python3 /usr/share/instaloader/instaloader.py "$@"
kali@kali:~/kali/packages/instaloader$
kali@kali:~/kali/packages/instaloader$ vim debian/instaloader.install
kali@kali:~/kali/packages/instaloader$
kali@kali:~/kali/packages/instaloader$ cat debian/instaloader.install
instaloader.py usr/share/instaloader/
instaloader usr/share/instaloader/
debian/helper-script/instaloader usr/bin/
kali@kali:~/kali/packages/instaloader$
```

이제 모든 필요한 `debian/` 파일들이 추가되었어요. 이제 빌드할 시간이에요!

## 패키징하기

모든 것을 하나의 파일로 묶을 시간이에요. 패키지를 만들기 위해 **sbuild**를 사용할 거예요. 이것에는 장단점이 있어요. 장점 중 하나는 여기서 빌드되면 빌드 데몬을 위해 설계되었기 때문에 다른 곳에서도 빌드된다는 거예요. 단점은 chroot에서 누락된 종속성을 감지하고 설치하려고 시도하기 때문에 네트워크 저장소에 접근해야 하며, 이로 인해 빌드 속도가 느려질 수 있어요.

**sbuild**를 사용하고 싶지 않다면, 인수에서 이를 제외하면 돼요(예: `gbp buildpackage`). 하지만, 그럴 경우 `debian/control`의 `Build-Depends` 섹션에 있는 것을 직접 설치해야 해요(예: `sudo apt install -y dh-python python3-all python3-setuptools python3-requests`).

그럼 sbuild를 한번 시도해 볼게요:

```console
kali@kali:~/kali/packages/instaloader$ gbp buildpackage --git-builder=sbuild
gbp:error: Can't determine package type: Failed to read changelog: can't get HEAD:debian/changelog: fatal: path 'debian/changelog' exists on disk, but not in 'HEAD'
kali@kali:~/kali/packages/instaloader$
```

앗! git에 변경 사항을 커밋하지 않았네요. 부끄럽네요.

만약 원한다면 `gbp buildpackage --git-builder=sbuild --git-export=WC`를 사용해서 이 과정을 우회할 수 있어요. 이렇게 하면 git 이력에 여러 디버깅/문제 해결 커밋으로 지저분해지는 것을 방지하면서 `debian/` 값들을 테스트해볼 수 있어요. 그런 다음 패키지가 제대로 작동하는 상태가 되면 git에 커밋하고 다시 시도할 수 있어요:

```console
kali@kali:~/kali/packages/instaloader$ git status
On branch kali/master
Untracked files:
  (use "git add <file>..." to include in what will be committed)
        debian/

nothing added to commit but untracked files present (use "git add" to track)
kali@kali:~/kali/packages/instaloader$
kali@kali:~/kali/packages/instaloader$ git add debian/
kali@kali:~/kali/packages/instaloader$
kali@kali:~/kali/packages/instaloader$ git commit -m "Initial release"
[kali/master 10a9e96] Add debian/ files
 8 files changed, 94 insertions(+)
 create mode 100644 debian/changelog
 create mode 100644 debian/control
 create mode 100644 debian/copyright
 create mode 100755 debian/helper-script/instaloader
 create mode 100644 debian/instaloader.install
 create mode 100755 debian/rules
 create mode 100644 debian/source/format
 create mode 100644 debian/watch
kali@kali:~/kali/packages/instaloader$
```

다시 빌드해봐요!:

참고: 다음 오류가 발생하지 않을 수도 있어요(운영체제의 "깨끗함" 정도에 따라 다름):

```console
kali@kali:~/kali/packages/instaloader$ gbp buildpackage --git-builder=sbuild
gbp:info: Exporting 'HEAD' to '/home/kali/kali/build-area/instaloader-tmp'
gbp:info: Moving '/home/kali/kali/build-area/instaloader-tmp' to '/home/kali/kali/build-area/instaloader-4.4.4'
gbp:info: Performing the build
dh clean --with python3 --buildsystem=pybuild
dh: error: unable to load addon python3: Can't locate Debian/Debhelper/Sequence/python3.pm in @INC (you may need to install the Debian::Debhelper::Sequence::python3 module) (@INC contains: /etc/perl /usr/local/lib/x86_64-linux-gnu/perl/5.30.3 /usr/local/share/perl/5.30.3 /usr/lib/x86_64-linux-gnu/perl5/5.30 /usr/share/perl5 /usr/lib/x86_64-linux-gnu/perl-base /usr/lib/x86_64-linux-gnu/perl/5.30 /usr/share/perl/5.30 /usr/local/lib/site_perl) at (eval 25) line 1.
BEGIN failed--compilation aborted at (eval 25) line 1.
make: *** [debian/rules:7: clean] Error 255
E: Failed to clean source directory /home/kali/kali/build-area/instaloader-4.4.4 (/home/kali/kali/build-area/instaloader_4.4.4-0kali1.dsc)
gbp:error: 'sbuild' failed: it exited with 1
kali@kali:~/kali/packages/instaloader$
```

위 오류가 발생한다면, 이는 운영체제에 `dh-python`이 없기 때문이에요. 다음 명령어로 빠르게 해결할 수 있어요:

```console
kali@kali:~/kali/packages/instaloader$ sudo apt install -y dh-python
kali@kali:~/kali/packages/instaloader$
```

한 번 더 빌드를 시도해 볼게요:

```console
kali@kali:~/kali/packages/instaloader$ gbp buildpackage --git-builder=sbuild
gbp:info: Exporting 'HEAD' to '/home/kali/kali/build-area/instaloader-tmp'
gbp:info: Moving '/home/kali/kali/build-area/instaloader-tmp' to '/home/kali/kali/build-area/instaloader-4.4.4'
gbp:info: Performing the build
dh clean --with python3 --buildsystem=pybuild
[...]

+------------------------------------------------------------------------------+
| Package contents                                                             |
+------------------------------------------------------------------------------+

[...]

Install lintian build dependencies (apt-based resolver)
-------------------------------------------------------

[...]

E: instaloader source: source-is-missing [docs/_static/bootstrap-4.1.3.bundle.min.js]
W: instaloader: no-manual-page [usr/bin/instaloader]

E: Lintian run failed (runtime error)

[...]

+------------------------------------------------------------------------------+
| Summary                                                                      |
+------------------------------------------------------------------------------+

Build Architecture: amd64
Build Type: full
Build-Space: 2460
Build-Time: 5
Distribution: kali-dev
Host Architecture: amd64
Install-Time: 37
Job: /home/kali/kali/build-area/instaloader_4.4.4-0kali1.dsc
Lintian: error
Machine Architecture: amd64
Package: instaloader
Package-Time: 45
Source-Version: 4.4.4-0kali1
Space: 2460
Status: successful
Version: 4.4.4-0kali1
--------------------------------------------------------------------------------
Finished at 2020-07-03T15:51:09Z
Build needed 00:00:45, 2460k disk space

kali@kali:~/kali/packages/instaloader$
```

여기 출력이 매우 길어서 내용을 줄였지만, 성공적으로 빌드된 것을 볼 수 있어요. `lintian`에서 나온 오류, 경고, 정보가 있더라도 말이죠!

생성된 파일을 확인해 볼게요:

```console
kali@kali:~/kali/packages/instaloader$ ls ~/kali/build-area/instaloader*
/home/kali/kali/build-area/instaloader_4.4.4-0kali1_all.deb          /home/kali/kali/build-area/instaloader_4.4.4-0kali1.debian.tar.xz
/home/kali/kali/build-area/instaloader_4.4.4-0kali1_amd64.build      /home/kali/kali/build-area/instaloader_4.4.4-0kali1.dsc
/home/kali/kali/build-area/instaloader_4.4.4-0kali1_amd64.buildinfo  /home/kali/kali/build-area/instaloader_4.4.4.orig.tar.gz
/home/kali/kali/build-area/instaloader_4.4.4-0kali1_amd64.changes
kali@kali:~/kali/packages/instaloader$
```

출력물이 생겼어요!

## Lintian 만족시키기

더 자세한 정보는 [데비안 문서](https://www.debian.org/doc/manuals/maint-guide/checkit.en.html)를 참조하세요.

오류 `E: instaloader source: source-is-missing [docs/_static/bootstrap-4.1.3.bundle.min.js]`를 이해해 보겠습니다:

```console
kali@kali:~/kali/packages/instaloader$ lintian-explain-tags source-is-missing
N:
E: source-is-missing
N: 
N:   다음 파일의 소스가 누락되었습니다. Lintian은 소스를 찾기 위해 몇 가지 가능한 경로를 확인했지만 찾지 못했습니다.
N:   
N:   패키지를 다시 패키징하여 소스를 포함하거나 "debian/missing-sources" 디렉토리에 추가하세요.
N:   
N:   참고로 very-long-line-length-in-source-file로 태그된 파일은 source-is-missing으로 태그될 가능성이 큽니다. 이는 버그가 아니라 기능입니다.
N: 
N:   Visibility: error
N:   Show-Always: no
N:   Check: files/source-missing
N: 
kali@kali:~/kali/packages/instaloader$
```

`docs/_static/bootstrap-4.1.3.bundle.min.js` 파일의 문제는 이것이 축소된 자바스크립트라는 거예요. 사람이 읽을 수 있는 형태가 아니고, 수정할 수 없기 때문에 소스 파일로 간주되지 않아요. Lintian이 제안하듯이 `debian/missing-sources`에 이 파일의 소스를 제공할 수 있지만, 소스가 바로 준비되어 있지 않기 때문에 다른 옵션을 탐색해야 해요. 이 가이드에서는 Lintian 오버라이드에 초점을 맞출 거예요. 문제가 되는 파일이 `docs` 디렉토리에 있고, 포함된 파일을 조사해보면 이것은 Instaloader가 [문서 사이트](https://instaloader.github.io/) 페이지를 호스팅하는 데 사용하는 것이므로, 이 파일을 무시하고 Lintian에게도 무시하도록 지시할 수 있어요.

이를 위해 `debian` 디렉토리에 위치한 `instaloader.lintian-overrides` 파일을 생성할 거예요. 여기에서 오류 메시지에서 `:`부터 복사하여 붙여넣을 수 있어요. 이 기회에 `no-manual-page`에 대한 경고도 무시하도록 설정해 볼게요. 다음은 결과 파일입니다:

```console
kali@kali:~/kali/packages/instaloader$ vim debian/instaloader.lintian-overrides

kali@kali:~/kali/packages/instaloader$

kali@kali:~/kali/packages/instaloader$ cat debian/instaloader.lintian-overrides
source-is-missing [docs/_static/bootstrap-4.1.3.bundle.min.js]
no-manual-page [usr/bin/instaloader]

kali@kali:~/kali/packages/instaloader$
```

이제 변경 사항을 커밋하고 같은 명령으로 패키지를 다시 빌드하여 오류 없이 성공했는지 확인할 수 있어요:

```console
kali@kali:~/kali/packages/instaloader$ gbp buildpackage --git-builder=sbuild
[...]

+------------------------------------------------------------------------------+
| Package contents                                                             |
+------------------------------------------------------------------------------+

[...]

Install lintian build dependencies (apt-based resolver)
-------------------------------------------------------

[...]

Running lintian...
E: instaloader source: source-is-missing [docs/_static/bootstrap-4.1.3.bundle.min.js]
I: instaloader: unused-override source-is-missing [docs/_static/bootstrap-4.1.3.bundle.min.js] [usr/share/lintian/overrides/instaloader:2]
N: 0 hints overridden; 1 unused override

E: Lintian run failed (runtime error)

[...]
kali@kali:~/kali/packages/instaloader$
```

이런! 여전히 실패한 것 같고, `source-is-missing` 오류에 대한 오버라이드가 사용되지도 않았어요! 자세히 살펴보면 `no-manual-page`와 `source-is-missing`에 대한 이전 경고 사이에 차이가 있어요. `:` 앞의 섹션은 오류 수준과 패키지 이름을 알려주는데, `source-is-missing` 오류에는 `source`가 포함되어 있어요. 이는 문제가 출력 또는 _바이너리 패키지_가 아닌 _소스 패키지_ 또는 가져온 패키지에 있기 때문이에요. 이를 해결하기 위해 `debian/`에 새로운 `source` 디렉토리와 새로운 `lintian-overrides` 파일을 만들어야 해요. 지금 해 볼게요:

```console
kali@kali:~/kali/packages/instaloader$ mkdir debian/source/

kali@kali:~/kali/packages/instaloader$

kali@kali:~/kali/packages/instaloader$ vim debian/source/lintian-overrides

kali@kali:~/kali/packages/instaloader$

kali@kali:~/kali/packages/instaloader$ cat debian/source/lintian-overrides
source-is-missing [docs/_static/bootstrap-4.1.3.bundle.min.js]

kali@kali:~/kali/packages/instaloader$
```

이전 파일에서도 오버라이드를 제거하는 것을 잊지 마세요!

```console
kali@kali:~/kali/packages/instaloader$ vim debian/instaloader.lintian-overrides

kali@kali:~/kali/packages/instaloader$

kali@kali:~/kali/packages/instaloader$ cat debian/instaloader.lintian-overrides
no-manual-page [usr/bin/instaloader]

kali@kali:~/kali/packages/instaloader$
```

다시 한번 변경 사항을 커밋하고 패키지를 다시 빌드하여 오류 없이 성공했는지 확인할 수 있어요:

```console
kali@kali:~/kali/packages/instaloader$ gbp buildpackage --git-builder=sbuild
[...]

+------------------------------------------------------------------------------+
| Package contents                                                             |
+------------------------------------------------------------------------------+

[...]

Install lintian build dependencies (apt-based resolver)
-------------------------------------------------------

[...]

Running lintian...

I: Lintian run was successful.

[...]
kali@kali:~/kali/packages/instaloader$
```

## 수동 설치

이제 패키지를 테스트해 보겠습니다:

```console
kali@kali:~/kali/packages/instaloader$ sudo apt install ~/kali/build-area/instaloader_4.4.4-0kali1_all.deb
Selecting previously unselected package instaloader.
(Reading database ... 154513 files and directories currently installed.)
Preparing to unpack .../instaloader_4.4.4-0kali1_all.deb ...
Unpacking instaloader (4.4.4-0kali1) ...
Setting up instaloader (4.4.4-0kali1) ...
Processing triggers for kali-menu (2020.3.0) ...
kali@kali:~/kali/packages/instaloader$
kali@kali:~/kali/packages/instaloader$ instaloader
usage:
instaloader.py [--comments] [--geotags]
               [--stories] [--highlights] [--tagged] [--igtv]
               [--login YOUR-USERNAME] [--fast-update]
               profile | "#hashtag" | %%location_id | :stories | :feed | :saved
instaloader.py --help
kali@kali:~/kali/packages/instaloader$
```

성공했어요!

이제 좋아 보이네요(완벽하진 않지만), 그리고 눈썰미 있는 분들은 (출력에서) 왜 그런지 알아챌 수 있을 거예요 - 출력에는 파일 확장자가 있지만(`instaloader.py`), 호출하는 데 사용된 명령에는 확장자가 없어요(`instaloader`).
이 문제를 해결하기 위해 애플리케이션을 패치하거나 애플리케이션을 호출하는 다른 방법을 찾아야 할 거예요. 하지만 이 내용은 다른 가이드에서 다룰 거예요.

## 저장 및 공유

이제 원격 저장소(`debian/control`에 정의한 것)에 푸시하기 전에 모든 것이 로컬 git에 있는지 확인해 볼게요:

```console
kali@kali:~/kali/packages/instaloader$ git status
On branch kali/master
nothing to commit, working tree clean
kali@kali:~/kali/packages/instaloader$
kali@kali:~/kali/packages/instaloader$ git branch -v
* kali/master  10a9e96 Use lintian-override to clear all warnings and errors
  pristine-tar 439fe30 pristine-tar data for instaloader_4.4.4.orig.tar.gz
  upstream     494f718 New upstream version 4.4.4
kali@kali:~/kali/packages/instaloader$
kali@kali:~/kali/packages/instaloader$ git remote -v
kali@kali:~/kali/packages/instaloader$
```

아직 원격 저장소 설정이 없으므로, 계속하기 전에 GitLab으로 가서 [새 프로젝트를 생성](https://gitlab.com/projects/new)해야 해요.

그 후:

```console
kali@kali:~/kali/packages/instaloader$ git remote add origin git@gitlab.com:kalilinux/packages/instaloader.git
kali@kali:~/kali/packages/instaloader$
kali@kali:~/kali/packages/instaloader$ git remote -v
origin  git@gitlab.com:kalilinux/packages/instaloader.git (fetch)
origin  git@gitlab.com:kalilinux/packages/instaloader.git (push)
kali@kali:~/kali/packages/instaloader$
```

이제 로컬 작업을 새 원격 저장소로 보내야 해요(태그도 잊지 마세요):

```console
kali@kali:~/kali/packages/instaloader$ git push --all
Enumerating objects: 95, done.
Counting objects: 100% (95/95), done.
Delta compression using up to 2 threads
Compressing objects: 100% (88/88), done.
Writing objects: 100% (95/95), 291.16 KiB | 7.46 MiB/s, done.
Total 95 (delta 1), reused 0 (delta 0), pack-reused 0
remote:
remote: To create a merge request for pristine-tar, visit:
remote:   https://gitlab.com/kalilinux/packages/instaloader/-/merge_requests/new?merge_request%5Bsource_branch%5D=pristine-tar
remote:
remote: To create a merge request for upstream, visit:
remote:   https://gitlab.com/kalilinux/packages/instaloader/-/merge_requests/new?merge_request%5Bsource_branch%5D=upstream
remote:
To gitlab.com:kalilinux/packages/instaloader.git
 * [new branch]      kali/master -> kali/master
 * [new branch]      pristine-tar -> pristine-tar
 * [new branch]      upstream -> upstream
kali@kali:~/kali/packages/instaloader$
kali@kali:~/kali/packages/instaloader$ git push --tags
Enumerating objects: 1, done.
Counting objects: 100% (1/1), done.
Writing objects: 100% (1/1), 172 bytes | 172.00 KiB/s, done.
Total 1 (delta 0), reused 0 (delta 0), pack-reused 0
To gitlab.com:kalilinux/packages/instaloader.git
 * [new tag]         upstream/4.4.4 -> upstream/4.4.4
kali@kali:~/kali/packages/instaloader$
```

이 시점에서 [칼리 리눅스 버그 트래커](https://bugs.kali.org/)에 티켓을 열어서 여러분이 만든 도구와 패키지를 제안할 수 있고, 우리 도구 팀이 그곳에서 처리할 거예요.
