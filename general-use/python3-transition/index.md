---
title: Python 3 전환에 대해 알아야 할 모든 것
description:
icon:
weight: 15
author: ["rhertzog",]
번역: ["xenix4845"]
---

### 전환에 관하여

Kali Linux는 완전히 Python 3로 전환했어요. 이는 Python 2를 사용하던 Kali에 패키지된 모든 도구가 제거되거나 Python 3를 사용하도록 변환되었음을 의미해요. Python 3로 변환된 모든 도구는 오직 shebang으로 `/usr/bin/python3`를 사용하는 스크립트만 포함하고 있어요.

Debian에서 직접 가져온 패키지에 관해서는, 대부분의 패키지에서 동일하게 했지만, Python 2에 계속 의존할 수 있도록 허용된 몇 가지 예외가 있어요. 그러나 이러한 패키지들은 모든 스크립트가 shebang으로 `/usr/bin/python2`를 사용하고, (이전의 `python` 대신) `python2` 바이너리 패키지에 의존하도록 업데이트되었어요.

이러한 변경 덕분에, Debian은 더 이상 `/usr/bin/python`을 제공할 필요가 없으며 최근 업그레이드는 효과적으로 해당 심볼릭 링크를 제거할 거예요.

불행히도, 웹에서 Python 스크립트를 다운로드하면, 그것은 아마도 shebang으로 `/usr/bin/python`을 가지고 있을 거예요. shebang 줄을 수정하지 않고 실행하려고 하면 다음과 같은 오류가 발생해요:

```plaintext
zsh: /home/kali/test.py: bad interpreter: /usr/bin/python: no such file or directory
```

Debian에서는 다음을 설치하여 `/usr/bin/python` 심볼릭 링크를 복원할 수 있어요:

- `python-is-python2`: `python2`를 가리키게 하려면
- `python-is-python3`: `python3`를 가리키게 하려면

### Kali에서 이전 버전과의 호환성 유지하기

위의 오류를 피하는 방법을 모르는 많은 사용자가 있기 때문에, 우리는 Kali가 기본적으로 Python 2를 계속 제공하고(Debian이 여전히 제공하는 한) `/usr/bin/python`이 이를 가리키도록 결정했어요. 또한 임의의 익스플로잇 스크립트가 성공적으로 실행될 합리적인 가능성을 가질 수 있도록 몇 가지 일반적인 외부 모듈(예: `requests`)도 유지하고 있어요.

그러나 Python2용 pip(일명 python-pip)는 사라졌고, `/usr/bin/pip`는 `/usr/bin/pip3`와 동일하며 Python 3용 모듈을 설치해요. 자세한 정보는 아래 FAQ를 참조하세요.

이 호환성은 `kali-linux-headless`가 `python2`, `python-is-python2` 및 `offsec-awae-python2`를 권장하도록 구현되어, 기본적으로 설치되면서도 제거하고 싶은 사용자들이 제거할 수 있어요.

사용자들이 이 상황을 인식할 수 있도록, 로그인 시 눈에 띄는 메시지를 표시해요:
```plaintext
┏━(Message from Kali developers)
┃
┃ We have kept /usr/bin/python pointing to Python 2 for backwards
┃ compatibility. Learn how to change this and avoid this message:
┃ ⇒ https://www.kali.org/docs/general-use/python3-transition/
┃
┗━(Run "touch ~/.hushlogin" to hide this message)
```

사용자들이 이 글을 읽고 마주칠 다양한 문제를 해결하는 방법을 알게 되기를 바라요.

### 자주 묻는 질문들

#### Python 스크립트를 다운로드했는데, 어떻게 해야 하나요?

shebang 줄을 검사해야 해요. shebang 줄은 스크립트의 첫 번째 줄로, `#!`로 시작하고 스크립트를 실행하는 데 사용될 인터프리터의 경로가 따라와요.

인터프리터가 `/usr/bin/python`이라면, 문서를 읽고 스크립트가 Python 3로 실행될 수 있는지 확인해야 해요. 만약 그렇다면, shebang 줄을 `/usr/bin/python3`를 가리키도록 업데이트해야 해요. 그렇지 않다면 `/usr/bin/python2`를 가리키도록 업데이트해야 해요.

유지할 수 있는, 좋은 shebang 줄들:

- `#!/usr/bin/python3`
- `#!/usr/bin/python2`
- `#!/usr/bin/env python3`
- `#!/usr/bin/env python2`

업데이트가 필요한 나쁜 shebang 줄들:

- `#!/usr/bin/python`
- `#!/usr/bin/env python`

#### 로그인 메시지를 제거하려면 어떻게 해야 하나요?

메시지는 `/usr/bin/python`이 구식 Python 2를 가리키는 동안에만 표시돼요. 이제 이 상황에 대해 알게 되었고 오래된 스크립트의 shebang 줄을 수정하는 방법을 알게 되었으니, `/usr/bin/python`을 안전하게 제거할 수 있어요:

```console
kali@kali:~$ sudo apt remove python-is-python2
```

또는 Python 3를 가리키게 하기로 결정할 수 있어요:

```console
kali@kali:~$ sudo apt install -y python-is-python3
```

위의 두 가지 작업 중 하나를 수행하면 로그인 메시지가 제거돼요.

또는, `/usr/bin/python`이 `python2`를 가리키게 유지하면서도 메시지를 비활성화하고 싶다면, 다음과 같이 할 수 있어요:

```console
kali@kali:~$ mkdir -p ~/.local/share/kali-motd
kali@kali:~$ touch ~/.local/share/kali-motd/disable-old-python-warning
```

#### 실행되지 않는 Python 2 스크립트가 있는데, 어떻게 해야 하나요?

Python 2 스크립트가 `offsec-awae-python2` 호환성 패키지에 포함된 모듈 외의 모듈을 사용한다면([여기에서 목록 참조](https://gitlab.com/kalilinux/packages/offsec-courses/-/tree/kali/master/python2-wheels)), 추가 모듈을 설치할 수 있는 완전히 격리된 Python 2 환경을 설정하기 위해 `pyenv`를 시도할 수 있어요. 우리의 [Kali에서 수명이 다한 Python 버전 사용하기](/docs/general-use/using-eol-python-versions/) 문서를 참조하세요.

#### Python 2용 pip를 원하는데, 어떻게 다시 가져올 수 있나요?

`pyenv`를 시도해 보세요. 우리의 [Kali에서 수명이 다한 Python 버전 사용하기](/docs/general-use/using-eol-python-versions/) 문서를 참조하세요.

#### Python 스크립트를 작성했는데, 어떻게 해야 하나요?

최종 사용자에게 친절하게:

- 코드가 Python 3 또는 Python 2로 실행되는지 명확하게 문서화하세요
- shebang 줄로 `/usr/bin/python3` 또는 `/usr/bin/python2`를 사용하세요, 이는 `/usr/bin/python`보다 더 표현력이 있으며 원하는 결과를 얻을 가능성이 더 높아요
- 아직 Python 3 호환성이 없다면 업데이트하세요

<!--
## Python2 & PIP

@g0tmi1k, I'm sure I remember something being said. but is it now covered in the kali-docs?

@Gamb1t-Joe-O Looking at https://www.kali.org/docs/general-use/using-eol-python-versions/ I wonder how that interact with sudo when you want to run an old python2 script as root. It might be worth saying a word about this and in particular clearly indicate that they should never ever run "sudo pip" as that would likely not use pyenv and use the system wide script and install stuff that would conflict with packaged modules.
kali.orgkali.org
Using EoL Python Versions on Kali | Kali Linux Documentation
Official documentation of Kali Linux, an Advanced Penetration Testing Linux distribution used for Penetration Testing, Ethical Hacking and network security assessments.

BTW now that we have documented the above, we have removed python-pip (i.e. the python2 version of pip) and users calling "pip" will install stuff for Python 3.
-->
