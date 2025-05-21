---
title: 칼리 리눅스에서 지원 종료된 Python 버전 사용하기
description:
icon:
weight: 15
author: ["gamb1t",]
번역: ["xenix4845"]
---

2019년 12월에 우리는 Python 2의 지원 종료(End-of-Life)에 어떻게 대응할 것인지에 대한 [블로그 포스트](/blog/python-2-end-of-life/)를 발표했어요. 그 이후로 사용자들이 사용하는 많은 도구들이 Python 3로 포팅되지 않아 사용할 때 문제가 발생하고 있어요. 이 페이지에서는 더 이상 지원되지 않는 버전을 안전하게 사용하는 방법을 다룰 거예요.

# pyenv

Python 2는 더 이상 Debian 저장소에서 유지 관리되지 않아요. 이는 우리가 이 문제를 해결할 방법을 찾아야 한다는 것을 의미해요. `pyenv`는 서로 충돌하지 않는 여러 Python 버전을 설치할 수 있게 해줌으로써 이 문제를 해결해요. 현재 Debian이나 Kali 저장소에는 없기 때문에 소스에서 설치해야 해요. 다행히도 개발자들이 편리한 [설치 스크립트](https://github.com/pyenv/pyenv-installer)를 제공했어요. 함께 설치와 설정을 진행해 봐요:

```console
kali@kali:~$ sudo apt install -y build-essential libssl-dev zlib1g-dev libbz2-dev libreadline-dev libsqlite3-dev wget curl llvm libncurses5-dev libncursesw5-dev xz-utils tk-dev libffi-dev liblzma-dev python3-openssl git
[...]
kali@kali:~$
```

다음으로 bash 설치 스크립트를 빠르게 실행할 거예요. `ZSH`가 기본 쉘이라면 이후에 `.zshrc` 파일을 편집해야 해요:

```console
kali@kali:~$ curl https://pyenv.run | bash
[...]
kali@kali:~$
```

`ZSH`를 사용하는 경우 이제 `.zshrc`에 적절한 줄을 추가할 거예요:

```console
kali@kali:~$ echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.zshrc
kali@kali:~$ echo 'export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.zshrc
kali@kali:~$ echo -e 'if command -v pyenv 1>/dev/null 2>&1; then\n  eval "$(pyenv init --path)"\nfi' >> ~/.zshrc
```

`$SHELL`이 비어 있는지 확인하고 비어 있다면 zsh를 추가해요.

```console
kali@kali:~$ [ -z "$SHELL" ] && SHELL=/usr/bin/zsh
```

설정을 계속 진행해주세요.

```console
kali@kali:~$ exec $SHELL
kali@kali:~$
kali@kali:~$ pyenv
pyenv 1.2.20
Usage: pyenv <command> [<args>]

Some useful pyenv commands are:
   activate    Activate virtual environment
   commands    List all available pyenv commands
   deactivate   Deactivate virtual environment
   doctor      Verify pyenv installation and development tools to build pythons.
   exec        Run an executable with the selected Python version
   global      Set or show the global Python version(s)
   help        Display help for a command
   hooks       List hook scripts for a given pyenv command
   init        Configure the shell environment for pyenv
   install     Install a Python version using python-build
   local       Set or show the local application-specific Python version(s)
   prefix      Display prefix for a Python version
   rehash      Rehash pyenv shims (run this after installing executables)
   root        Display the root directory where versions and shims are kept
   shell       Set or show the shell-specific Python version
   shims       List existing pyenv shims
   uninstall   Uninstall a specific Python version
   --version   Display the version of pyenv
   version     Show the current Python version(s) and its origin
   version-file   Detect the file that sets the current pyenv version
   version-name   Show the current Python version
   version-origin   Explain how the current Python version is set
   versions    List all Python versions available to pyenv
   virtualenv   Create a Python virtualenv using the pyenv-virtualenv plugin
   virtualenv-delete   Uninstall a specific Python virtualenv
   virtualenv-init   Configure the shell environment for pyenv-virtualenv
   virtualenv-prefix   Display real_prefix for a Python virtualenv version
   virtualenvs   List all Python virtualenvs found in `$PYENV_ROOT/versions/*'.
   whence      List all Python versions that contain the given executable
   which       Display the full path to an executable

See `pyenv help <command>' for information on a specific command.
For full documentation, see: https://github.com/pyenv/pyenv#readme
kali@kali:~$
```

이제 Python 2를 설치하고 기본 Python 버전으로 설정할 수 있어요:

```console
kali@kali:~$ pyenv install 2.7.18
Downloading Python-2.7.18.tar.xz...
-> https://www.python.org/ftp/python/2.7.18/Python-2.7.18.tar.xz
Installing Python-2.7.18...
Installed Python-2.7.18 to /home/kali/.pyenv/versions/2.7.18

kali@kali:~$
kali@kali:~$ pyenv global 2.7.18
kali@kali:~$
kali@kali:~$ pyenv versions
  system
* 2.7.18 (set by /home/kali/.pyenv/version)
kali@kali:~$
kali@kali:~$ exec $SHELL
kali@kali:~$
kali@kali:~$ python
Python 2.7.18 (default, Apr 20 2020, 20:30:41)
[GCC 9.3.0] on linux2
Type "help", "copyright", "credits" or "license" for more information.
>>>
kali@kali:~$
```

이제 우리가 사용하는 도구에 필요한 의존성을 필요에 따라 설치할 수 있어요. Python 3로 다시 전환하고 싶을 때는 전역을 시스템으로 설정하기만 하면 돼요.

기억해야 할 한 가지는 `pip`를 통해 의존성을 설치하는 것을 고수하는 것이에요. `apt`를 통해 그리고 pip를 통해 Python 2 의존성을 설치하려고 하면 친절하지 않을 거예요. 따라서 이 경우에는 pip만 사용하는 것이 좋아요.

# Get Pip

또 다른 사용 가능한 옵션은 [get-pip](https://pip.pypa.io/en/stable/installation/)이에요. git-pip를 사용하면 간단히 파이썬 스크립트를 실행하고 사용 중인 버전에 pip를 설치할 수 있어요. 이 경우에는 Python 2예요. 다음과 같이 할 수 있어요:

```console
kali@kali:~$ curl https://bootstrap.pypa.io/pip/2.7/get-pip.py -o get-pip.py
kali@kali:~$ python2.7 get-pip.py
```

이것이 완료되면 파이썬 버전 다음에 `-m pip` 플래그를 사용한다는 것을 기억한다면 평소처럼 pip를 사용할 수 있어요. 예를 들어, *requests* 모듈이 필요한 스크립트를 실행하려면 다음과 같이 설치하고 실행할 수 있어요:

```console
kali@kali:~$ python2.7 -m pip install requests
kali@kali:~$ python2.7 <file>
```
