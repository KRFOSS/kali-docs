---
title: pipx를 통한 Python 애플리케이션 설치하기
description:
icon:
weight: 16
author: ["arnaudr",]
번역: ["xenix4845"]
---

## 소개: `pip install`에게 작별을 고하세요

[칼리 리눅스 2024.4](https://www.kali.org/blog/kali-linux-2024-4-release/)부터, 외부 Python 패키지를 설치하기 위해 `pip`를 사용하는 것은 _강력히 권장되지 않아요_. 대신, `pipx`를 사용하는 것이 좋아요. 표면적으로는 비슷한 사용자 경험을 제공하지만, 내부적으로는 pip의 가장 큰 문제점인 환경 격리 부재를 해결해요.

`pip`를 사용하여 시스템 전체 설치(`sudo pip install`)나 사용자 홈 디렉토리 설치(`pip install --user`)를 시도하면, 다음과 같은 메시지가 표시돼요:

```console
┌──(kali㉿kali)-[~]
└─$ sudo pip install xyz
error: externally-managed-environment

? This environment is externally managed
╰─> To install Python packages system-wide, try apt install
    python3-xyz, where xyz is the package you are trying to
    install.
    
    If you wish to install a non-Kali-packaged Python package,
    create a virtual environment using python3 -m venv path/to/venv.
    Then use path/to/venv/bin/python and path/to/venv/bin/pip. Make
    sure you have pypy3-venv installed.
    
    If you wish to install a non-Kali-packaged Python application,
    it may be easiest to use pipx install xyz, which will manage a
    virtual environment for you. Make sure you have pipx installed.
    
    For more information, refer to the following:
    * https://www.kali.org/docs/general-use/python3-external-packages/
    * /usr/share/doc/python3.12/README.venv

note: If you believe this is a mistake, please contact your Python
installation or OS distribution provider.  You can override this,
at the risk of breaking your Python installation or OS, by passing
--break-system-packages.
hint: See PEP 668 for the detailed specification.
```

간단히 말해, 이 변경의 이유는 Kali 시스템에서 Python 패키지를 설치하기 위해 `apt`(Kali의 패키지 관리자)와 `pip`를 함께 사용하는 것이 실제로 지원되지 않았기 때문이에요. `apt`와 `pip` 모두 동일한 환경에 Python 패키지를 설치하기 때문에, 기본적으로 서로 충돌하여 빠르게 망가질 수 있어요. [pipx](https://pipx.pypa.io/)는 이 문제에 대한 해결책이니 사용해 주세요.

더 길고 공식적인 설명은 [PEP 668 – 외부 관리되는 Python 기본 환경 표시](https://peps.python.org/pep-0668/)를 참조하거나, 더 간단한 설명은 블로그 포스트 [Pip install과 Python의 외부 관리](/blog/python-externally-managed/)를 참조하세요.

아래에서는 위의 오류 메시지에서 제안한 내용을 따라 구체적인 예제를 제공할게요.

## APT를 통한 패키지 및 프로그램 설치 선호하기

찾고 있는 Python 프로그램이 이미 칼리 리눅스에 패키지로 제공되는지 항상 확인하고, 제공된다면 APT로 설치하세요.

예를 들어, [Faraday의 README](https://github.com/infobyte/faraday)를 확인해 보세요. 이 페이지에는 Docker 이미지, PyPi 패키지(`pip`로 설치), 또는 Faraday가 직접 게시한 배포판 패키지를 포함한 여러 설치 방법이 언급되어 있어요.

그 어떤 것도 하기 전에, Faraday가 이미 칼리 리눅스에 패키지로 제공되는지 확인할 수 있어요:

```console
┌──(kali㉿kali)-[~]
└─$ apt search faraday
[...]
```

출력이 너무 길어서, `faraday`로 시작하는 패키지 이름만 찾아보겠어요:

```console
┌──(kali㉿kali)-[~]
└─$ apt search faraday | grep ^faraday
faraday/kali-rolling 5.7.0-0kali1 all
faraday-agent-dispatcher/kali-rolling 3.2.1-0kali2 all
faraday-cli/kali-rolling 2.1.8-0kali1 all
```

이제 가까워졌어요: `faraday`라는 패키지가 있습니다. 원하는 것이 맞는지 확인해 보겠어요:

```console
┌──(kali㉿kali)-[~]
└─$ apt show faraday
Package: faraday
[...]
Homepage: https://faradaysec.com
[...]
Description: Collaborative Penetration Test IDE
[...]
```

맞아요! 간단하게 설치할 수 있어요:

```console
┌──(kali㉿kali)-[~]
└─$ sudo apt install faraday
```

완료됐어요!

## Kali에 패키지가 없거나 너무 오래됐나요? pipx로 설치하세요

이 예시에서는 [XSStrike](https://github.com/s0md3v/XSStrike)를 설치해 볼게요. 이번에는 APT가 아무 결과도 반환하지 않아요:

```console
┌──(kali㉿kali)-[~]
└─$ apt search xsstrike
```

그래서 `pipx`로 설치할 거예요. 이 방법은 프로젝트가 Python 패키지 인덱스에 게시되어 있다고 가정하며, [실제로 그렇습니다](https://pypi.org/project/xsstrike/).

설치는 매우 간단해요:

```console
┌──(kali㉿kali)-[~]
└─$ pipx install xsstrike
  installed package xsstrike 3.2.2, installed using Python 3.12.6
  These apps are now globally available
    - xsstrike
done!
```

이게 전부에요. 이제 실행할 수 있어요:

```console
┌──(kali㉿kali)-[~]
└─$ xsstrike -h

usage: xsstrike [-h] [-u target] [--data paramdata] [-e encode] [--fuzzer]
                [--update] [--timeout timeout] [--proxy] [--crawl] [--json]
                [--path] [--seeds args_seeds] [-f args_file] [-l level]
                [--headers [add_headers]] [-t threadcount] [-d delay]
                [--skip] [--skip-dom] [--blind]
                [--console-log-level {debug,info,run,good,warning,error,critical,vuln}]
                [--file-log-level {debug,info,run,good,warning,error,critical,vuln}]
                [--log-file log_file] [-n payload_count]
[...]
```

벌써 작동하네요!

## Pipx 문제 해결

### pipx 설치

[칼리 리눅스 2024.4](https://www.kali.org/blog/kali-linux-2024-4-release/)부터 `pipx`가 사전 설치되어 있어야 해요. 그렇지 않은 경우, 평소처럼 `apt`를 통해 설치할 수 있어요:

```console
┌──(kali㉿kali)-[~]
└─$ sudo apt install -y pipx
```

### `~/.local/bin`을 경로에 추가하기

`~/.local/bin`은 `pipx`가 Python 애플리케이션을 설치하는 디렉토리예요. 이 디렉토리는 `PATH` 환경 변수에 포함되어야 하므로, pipx를 통해 예를 들어 `xyz`라는 애플리케이션을 설치하면 터미널에서 간단히 `xyz`를 입력하여 실행할 수 있어요.

[칼리 리눅스 2024.4](https://www.kali.org/blog/kali-linux-2024-4-release/)부터 `~/.local/bin`은 이미 `PATH`에 포함되어 있어야 해요. 터미널을 열고 다음 명령을 실행하여 확인할 수 있어요:

```console
┌──(kali㉿kali)-[~]
└─$ echo $PATH
/home/kali/.local/bin:[...]
```

출력 어딘가에 `/home/kali/.local/bin`이 보인다면 문제 없어요.

어떤 이유로 그것이 없다면, `pipx`로 프로그램을 설치한 후 다음과 같은 메시지가 표시될 수 있어요:

```console
┌──(kali㉿kali)-[~]
└─$ pipx install xyz
  installed package xyz 1.0, installed using Python 3.12.6
  These apps are now globally available
    - xyz
   Note: '/home/kali/.local/bin' is not on your PATH environment variable.
    These apps will not be globally accessible until your PATH is updated.
    Run `pipx ensurepath` to automatically add it, or manually modify your
    PATH in your shell's config file (e.g. ~/.bashrc).
done!
```

메시지와 다르게, `pipx ensurepath`를 실행할 필요는 없어요. 대신, 로그아웃한 다음 다시 로그인하세요. 그런 다음 터미널을 열고 `echo $PATH`를 실행하면 출력 어딘가에 `/home/kali/.local/bin`이 보일 거예요.

아주 신비한 이유로 여전히 보이지 않는다면, `pipx ensurepath`를 실행하고 지시를 따르는 것이 좋아요.
