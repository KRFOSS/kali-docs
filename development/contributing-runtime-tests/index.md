---
title: autopkgtest로 런타임 테스트 기여하기
description:
icon:
date: 2020-07-11
weight:
author: ["gamb1t",]
keywords: ["",]
og_description:
번역: ["xenix4845"]
---

## Kali가 여러분의 도움을 필요로 하는 이유

칼리 리눅스는 롤링 배포판(계속 업데이트되는 배포판)이기 때문에 의존성 변화로 인해 간혹 패키지가 작동하지 않는 경우가 있어요. 이를 최대한 방지하기 위해 [자동화된 런타임 테스트](https://autopkgtest.kali.org/)를 설정해서 패키지가 [kali-rolling](/docs/general-use/kali-branches/) 브랜치로 이동하기 전에 통과해야 하도록 했어요. 하지만 현재 대부분의 패키지에는 포괄적인 autopkgtest(자동 패키지 테스트)가 없어서 이 프로세스가 완전한 잠재력을 발휘하지 못하고 있어요. 커뮤니티가 테스트가 부족한 패키지에 테스트를 기여해 주시면 큰 도움이 돼요.

### autopkgtest 배경 지식

autopkgtest는 실제 Debian 시스템에서 패키지를 가능한 정확하게 테스트하기 위해 만들어졌어요. 이 방법은 가상화와 컨테이너를 활용해 정확하고 재사용 가능한 테스트 환경을 만들 수 있어요. 이 주제에 대한 정보는 훨씬 더 많아요. 여기서 다루는 것보다 더 깊이 알고 싶다면 다음 자료들을 참고하세요(이 글을 작성할 때도 정보 출처로 사용했어요):

* [Debian CI 공식 문서 페이지](https://ci.debian.net/doc/file.TUTORIAL.html)
* [autopkgtest에 관한 좋은 강연](http://meetings-archive.debian.net/pub/debian-meetings/2015/debconf15/Tutorial_functional_testing_of_Debian_packages.webm)
* [설치 후 테스트 작성에 관한 심층 논문](https://gitlab.com/terceiro/installed-tests-patterns/raw/pdf/final/installed-tests-patterns.pdf)
* [Debian 위키의 모범 사례 페이지](https://wiki.debian.org/ContinuousIntegration/AutopkgtestBestPractices)

이 문서를 다 읽은 후에는 위 자료들도 한번 살펴보시길 권해요. 여기서는 이 방대한 주제의 작은 부분만 다룰 거예요.

### 테스트 파일 구조화하기

소스 패키지에 autopkgtest를 추가하려면 `debian/tests/control` 파일을 만들면 돼요. 이 파일은 실행해야 할 모든 테스트를 설명해요. `debian/control` 파일과 마찬가지로, 메일 헤더처럼 구성된 항목들을 사용해요(예: `Field-Name: some value`). 가장 간단한 경우에는 control 파일에서 테스트를 완전히 설명할 수 있지만, 대부분의 복잡한 테스트에서는 옆에 둔 외부 테스트 스크립트를 참조할 거예요. 이 스크립트는 `debian/tests/` 폴더에 넣으면 돼요.

어떤 일을 하고 있고 어떤 것들이 가능한지 잘 이해하려면 [control 파일 공식 문서](https://salsa.debian.org/ci-team/autopkgtest/-/blob/master/doc/README.package-tests.rst)를 살펴보세요. 많은 옵션들이 잘 설명되어 있어요. 실험과 학습을 통해 유용하게 활용할 수 있지만, 지금은 기본적인 것에만 집중할게요. 그들의 예제 control 파일을 살펴봅시다:

```plaintext
Tests: fred, bill, bongo
Depends: pkg1, pkg2 [amd64] | pkg3 (>= 3)
Restrictions: needs-root, breaks-testbed
```

이것을 간단히 분석해볼게요. 세 개의 테스트 스크립트가 있어요: fred, bill, bongo. 이 세 테스트 스크립트는 `debian/tests/` 폴더에 있어요. 이 테스트들이 정확히 무엇을 하는지는 모르지만, `Depends` 필드를 통해 두 개의 패키지가 필요하다는 걸 알 수 있어요(이 예제는 일부러 복잡한 의존성 구문을 보여주는데, 대개는 필요 없지만 설명을 위해 말하자면 `pkg1`은 항상 필요하고, amd64 머신에서는 `pkg2`도 필요하며, 다른 머신에서는 버전 3 이상의 `pkg3`이 필요하다는 뜻이에요). 또한 이 테스트 모음은 루트 사용자 또는 높은 권한이 필요하고, 테스트 환경을 손상시킬 가능성이 있다는 것도 알 수 있어요(즉, autopkgtest는 테스트 환경을 쉽게 복원할 수 없다면 테스트 실행을 거부해요).

그들이 가지고 있는 또 다른 예제를 살펴봅시다:

```plaintext
Test-Command: foo-cli --list --verbose
Depends: foo
Restrictions: needs-root
```

여기서는 `Test-Command`를 통해 테스트를 실행하고 있어요. 이렇게 하면 테스트를 위한 스크립트를 만들 필요가 없어서, 한 줄짜리 스크립트를 호출하는 것을 방지할 수 있어요. 이 경우 `foo-cli`를 실행하는데, Depends 필드를 보면 이것이 `foo` 패키지의 일부라는 것을 알 수 있어요. 한 줄짜리 명령어만 있어서 이 테스트는 표면적인(superficial) 것 같은데, 이는 Restrictions에 명시되어야 해요. 실제 패키지였다면 더 자세히 조사해볼 필요가 있었을 거예요. 테스트가 한 줄짜리 명령어만 사용한다면 대부분 `Restrictions: superficial`을 포함해야 해요.

control 파일에는 스크립트나 한 줄짜리 명령어 같은 테스트 방법, 테스트가 의존하는 것들, 그리고 테스트가 가질 수 있는 잠재적 제한 사항이나 문제점이 포함돼요. 테스트에 빌드된 바이너리 패키지만 필요하다면, 명시적인 `Depends` 필드를 지정할 필요 없이 기본값인 `Depends: @`을 사용할 수 있어요.

테스트를 위해 `foobar`라는 추가 패키지를 설치하고 싶다면, `Depends: @, foobar`를 사용하면 돼요. 여기서 `@`는 테스트가 포함된 소스 패키지에서 생성된 바이너리 패키지를 나타내는 축약형이에요.

기억해야 할 몇 가지 중요한 제한 사항들(다른 모든 가능한 옵션도 살펴보세요!):

- `allow-stderr` - `stderr`로의 출력을 허용하고, 출력이 있어도 실패하지 않아요.
- `rw-build-tree` - 특정 조건이 적용된 상태에서 테스트가 빌드된 소스 트리에 접근할 수 있어요.
- `isolation-container` - 테스트의 일부인 서비스나 열린 TCP 포트는 실패를 방지하기 위해 자체 컨테이너나 VM을 사용해요.
- `isolation-machine` - 테스트가 재부팅이나 커널과 상호작용하는 것처럼 일반적으로 오류를 발생시키는 방식으로 컨테이너/VM과 상호작용할 가능성이 있어요.
- `needs-internet` - 테스트에 인터넷에 대한 제한 없는 액세스가 필요해요.

### 예제로 배우기

autopkgtest에 대해 배울 수 있는 몇 가지가 이미 실제로 보이고 있어요. 테스트가 어떻게 생겼는지, 어떤 유형이 더 유익한지, 그리고 볼 수 있는 몇 가지 트릭을 살펴보겠습니다.

[cloud_enum](https://gitlab.com/kalilinux/packages/cloud-enum/-/tree/kali/master/debian/tests) control 파일은 다음과 같아요:

<p class="codeblock-label">cloud_enum control 파일</p>

```plaintext
Test-Command: cloud_enum --help
Depends: @
Restrictions: superficial
```

반면에 [python-pip](https://gitlab.com/kalilinux/packages/python-pip/-/tree/kali/master/debian/tests) control 파일은 이렇게 생겼어요:

<p class="codeblock-label">python-pip control 파일</p>

```plaintext
Tests: pip3-root.sh
Restrictions: needs-root

Tests: pip3-user.sh
Restrictions: breaks-testbed

# https://bugs.debian.org/cgi-bin/bugreport.cgi?bug=823358
Tests: pip3-editable.sh
```
여기에는 다음과 같은 테스트가 포함되어 있어요:

<p class="codeblock-label">python-pip pip3-user.sh 테스트 파일</p>

```sh
#!/bin/sh

export HOME=$AUTOPKGTEST_TMP
export PATH=$PATH:$HOME/.local/bin
export PIP_DISABLE_PIP_VERSION_CHECK=1

if [ $(id -u) = 0 ]
then
    adduser --quiet --system --group --no-create-home testing
    user=testing
    mkdir $HOME/.cache
    chown -R $user $HOME
    runuser="runuser -p -u $user --"
else
    runuser=""
fi

$runuser python3 -m pip install world
$runuser python3 -m pip list --format=columns
$runuser python3 -m pip show world
ls -ld $HOME/.local/lib/python3.*/site-packages/world-*.dist-info
$runuser python3 -m pip uninstall -y world
$runuser python3 -m pip list --format=columns
# Temporarily disabled.  See #912379
#$runuser python3 -m pip list --outdated
if [ $(id -u) = 0 ]
then
    deluser --quiet testing
fi
```

둘 사이에는 큰 차이가 있어요. 둘 다 기술적으로 통과 또는 실패를 만들어내고, 첫 번째 옵션인 cloud_enum control 파일이 더 쉽고 좋아 보일 수 있어요. 하지만 이는 표면적인 테스트로, "테스트가 충분한 테스트 범위를 제공하지 않아서, 통과했다고 해서 테스트 중인 패키지가 실제로 기능한다는 것을 의미하지 않는다"는 뜻이에요. 그래서 우리는 python-pip 테스트 환경을 선호해요. 더 자세하고 다양한 유형의 최종 사용자 환경 테스트가 포함되어 있거든요.

방금 많은 텍스트와 코드를 별 설명 없이 보여드렸는데, 너무 깊이 빠지기 전에 차이점을 이해하는 것이 중요해요. 이제 한 발 물러서서 더 자세히 살펴봅시다.

`cloud_enum` control 파일을 자세히 살펴보면 다음 세 가지가 있어요:

```plaintext
Test-Command: cloud_enum --help
Depends: @
Restrictions: superficial
```
첫 번째로 실행할 명령어를 알려줘요. 이것은 표면적인 테스트이기 때문에, 스크립트를 실행하라고 하지 않고 도움말 출력을 테스트하는 한 줄만 있어요. 그런 다음 '@'를 사용해 패키지가 의존하는 것과 동일한 의존성에 의존하도록 지정해요. 테스트에 다른 의존성이 필요하다면 여기에 나열할 수 있어요. 다른 테스트에서 이러한 방법을 볼 수 있을 거예요. 마지막으로 이 테스트가 표면적인 테스트라고 알려줘요. `cloud_enum`이 루트 권한이 필요하다거나, 이 테스트 후에 테스트 환경을 폐기해야 한다는 등의 다른 정보도 알려줄 수 있어요.

다음으로 배울 control 파일은 [pytest-factoryboy](https://gitlab.com/kalilinux/packages/pytest-factoryboy/-/tree/kali/master/debian/tests)의 것이에요:

```plaintext
Tests: test3-pytest-factoryboy
Depends: @, python3-pytest-pep8
```
보시다시피, 파이썬 모듈에 의존하고 있어요. 이 파이썬 모듈은 다음 테스트에서 볼 수 있듯이 도구를 테스트하는 데 도움을 줄 거예요:

```sh
#!/bin/sh
set -e
cp -r tests "$AUTOPKGTEST_TMP/" && cd "$AUTOPKGTEST_TMP"
for py in $(py3versions -i); do
    $py -Wd -m pytest -v -x tests 2>&1;
done
```
또한 좋은 관행인 다른 것도 볼 수 있어요. `$AUTOPKGTEST_TMP`는 이러한 테스트에서 사용할 수 있으며, `breaks-testbed` 플래그가 설정되지 않은 경우 시스템을 정리하고 다음 테스트를 시작하는 데 도움이 돼요.

[hyperion](https://gitlab.com/kalilinux/packages/hyperion/-/tree/kali/master/debian/tests)은 많은 정보를 보여주는 또 다른 패키지예요. 하지만 파일 크기와 관련된 세부 사항 때문에 여기서는 설명하지 않을게요.

### 무엇을 해야 하나요

이제 시스템이 설정되었고 실제 테스트도 봤으니, 이제 테스트를 직접 작성해야 해요. 테스트가 없거나 표면적인 테스트만 있는 패키지를 찾았다면, 먼저 그 도구에 대해 배워야 해요. 서버에 핑을 보낸 다음 응답에 따라 사용자에게 무언가를 알려주는 도구가 있다고 가정해봐요. 실패는 어떻게 생겼나요? 성공은 어떻게 생겼나요? 테스트를 망칠 수 있는 특이 케이스가 있나요?

도구의 목적에 대해 배운 후 다음 단계는 autopkgtest에게 무엇을 알려야 할지 파악하는 거예요. 이전 예제의 도구를 사용하면, 핑을 제대로 보내기 위해 루트 권한이 필요할까요? 도구가 작동하도록 하기 위해 머신이나 환경에서 무엇을 더 해야 할까요? 테스트 후에는 어떨까요? 출력 파일이 생성되어 $AUTOPKGTEST_TMP를 사용해야 할까요?

도구에 대해 테스트할 수 있는 것을 배우고 기록한 후에는, 앞서 제공된 모범 사례와 팁을 사용하여 테스트 작성을 시작해야 해요. 이러한 리소스에서 파생된 많은 가용 리소스가 있고, 필요하다면 예제로 배울 수 있는 많은 패키지가 있어요.

Kali는 GitLab을 사용하므로, [소스 패키지](https://gitlab.com/kalilinux/packages)를 포크하고 새 브랜치에 테스트를 추가해야 해요. GitLab CI 설정이 autopkgtest를 실행하므로, 파이프라인 출력을 확인하여 테스트가 올바르게 실행되었고 성공했는지 확인할 수 있어요. 작동하지 않는다면 테스트 스크립트에 만족할 때까지 반복(더 많은 커밋 추가)할 수 있어요.

완성된 성공적인 테스트를 얻었다면, 이미 GitLab에 작업이 있으니 머지 리퀘스트를 제출해야 해요. 패키지에 기여하는 [저장 및 공유](/docs/development/intro-to-packaging-example/#save--share) 섹션에서 좋은 예를 확인할 수 있어요.

환경 자체에 대한 자세한 정보는 `autopkgtest-build-qemu`와 `autopkgtest-virt-qemu` 맨 페이지가 유용해요. `/usr/share/doc/autopkgtest/`에 있는 문서도 도움이 돼요.

### 로컬에서 테스트 실행하기

로컬에서 테스트를 실행해야 한다면, 다행히 꽤 간단하지만 몇 가지 준비가 필요해요.

먼저 실제 테스트 소프트웨어와 테스트에 사용할 디스크 이미지를 만들기 위한 `vmdb2`를 다운로드해요:

```console
kali@kali:~$ sudo apt install -y autopkgtest vmdb2
```

그런 다음 이미지와 데이터를 저장할 디렉토리를 만들어야 해요:

```console
kali@kali:~$ sudo mkdir /srv/autopkgtest-images/
```

다음 명령어는 autopkgtest와 호환되는 kali-rolling 기반 이미지를 만들어요. 앞으로의 테스트에 이 이미지를 사용할 수 있어요:

```console
kali@kali:~$ sudo autopkgtest-build-qemu kali-rolling /srv/autopkgtest-images/kali-rolling.img http://http.kali.org/kali
```

이제 테스트를 실행하려면 다음 명령어를 실행하기만 하면 돼요: `autopkgtest ~/packages/mytest/ -- qemu /srv/autopkgtest-images/kali-rolling.img`. 이 명령어는 테스트하려는 패키지가 있는 디렉토리를 가리켜요.

필요할 수도 있는 다른 많은 변형들이 있으며, /usr/share/doc/autopkgtest/README.running-tests.html에서 찾을 수 있어요. 그러나 로컬 테스트에서는 여러 가지 문제가 발생할 수 있고, GitLab을 사용하는 경우 필요한 도움을 받기가 더 쉽기 때문에 GitLab CI를 사용하는 것이 좋아요.
