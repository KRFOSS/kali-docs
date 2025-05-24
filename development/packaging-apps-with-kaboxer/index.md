---
title: Kaboxer로 애플리케이션 패키징하기
description:
icon:
weight: 100
author: ["lolando", "rhertzog",]
번역: ["xenix4845"]
---

일부 애플리케이션은 제대로 패키징할 수 없는 경우가 있어요. 예를 들어 칼리에서 더 이상 사용할 수 없는 오래된 라이브러리에 의존성이 있는 경우가 그래요. 다른 것들은 시스템의 다른 애플리케이션을 망가뜨릴 수 있는 동작 때문에 격리된 환경에서 실행되어야 해요.

이러한 문제들을 극복하기 위해 우리는 Kaboxer를 만들어서 컨테이너 내에서 그런 애플리케이션들을 관리하고 칼리 리눅스(및 기타 데비안 기반) 시스템에서 다른 애플리케이션처럼 사용할 수 있게 했어요. 메뉴에서 실행할 수 있고 `apt install`로 설치할 수 있어요.

흥미롭게 들리나요? 이 새로운 프레임워크로 애플리케이션을 패키징하는 방법을 살펴보죠. 먼저 도구 자체를 설치해요:

```console
kali@kali:~$ sudo apt install -y kaboxer
```

## Kaboxer 소개

Kaboxer의 아이디어는 실행 가능한 애플리케이션 이미지를 준비하고, 도커 레지스트리에서 온라인으로 사용할 수 있게 만든 다음, 사용자가 해당 이미지를 가져와서 컨테이너를 시작/중지하여 애플리케이션을 실행하도록 하는 것이에요. 이 모든 단계는 `kaboxer` 명령줄 도구로 처리돼요.

데비안 패키지를 통해 애플리케이션을 배포하는 일반적인 방식에 가깝게 유지하기 위해, Kaboxer는 설치 시점에 투명하게 도커 이미지를 다운로드하고 시스템에 애플리케이션을 원활하게 통합하는 패키지를 쉽게 빌드할 수 있게 해줘요. 이는 특히 debhelper 도구와의 통합(`dh_kaboxer`와 특정 빌드 시스템)을 제공함으로써 달성돼요.

이 튜토리얼에서는 이미지를 빌드하고 애플리케이션을 실행할 것이지만, 실제로는 두 단계가 보통 다른 상황에서 일어나요: 빌드는 결과 이미지를 공개 레지스트리에 업로드하는 서버에서 한 번 일어나고, 최종 사용자는 이미지를 다운로드해서 실행하기만 해요.

## Kaboxer로 간단한 애플리케이션 패키징하기

### 전제 조건

이 튜토리얼에서 설명하는 명령어들은 Kaboxer 소스 코드의 일부인 `hello-kbx` 디렉토리 내에서 실행되어야 해요. Git으로 가져올 수 있어요:

```console
kali@kali:~$ git clone https://gitlab.com/kalilinux/tools/kaboxer.git
Cloning into 'kaboxer'...
[...]

kali@kali:~$ cd kaboxer/hello-kbx
```

Kaboxer는 도커 데몬에 접근이 필요하므로 사용자가 필요한 권한을 가지고 있는지 확인해야 해요: `docker` 그룹(도커 소켓에 직접 쓰기 접근 권한이 있는)이나 `kaboxer` 그룹(`sudo`로 필요한 권한을 얻을 수 있는)의 일부여야 해요.

칼리 2020.4 이상을 설치했다면, 설치 중에 생성된 초기 사용자는 이미 `kaboxer` 그룹의 일부이고 필요한 권한을 가지고 있을 거예요. `groups` 명령어로 확인할 수 있어요:

```console
kali@kali:~$ groups
[...] kaboxer [...]
```

현재 사용자가 `kaboxer` 그룹의 일부가 아니라면, 다음 명령어로 그룹 멤버십을 부여해야 해요:

```console
kali@kali:~$ sudo adduser $USER kaboxer
```

새로운 그룹 멤버십은 사용자 세션을 다시 시작한 후에만 보일 거예요(`sg`, `newgrp` 또는 `su` 같은 명령어로 새로운 하위 세션을 생성한 후에도 가능해요).

### 도커 이미지 생성하기

Kaboxer는 현재 격리/컨테이너 메커니즘으로 도커만 지원해요. 따라서 Kaboxer로 패키징될 애플리케이션을 준비할 때("kaboxed"라고 줄여서 부름) 첫 번째 단계는 **Dockerfile**을 작성하는 것이에요. Kaboxer에서 필요한 몇 가지 옵션이 있지만, 그 외에는 표준 Dockerfile이에요. 예를 들어, `hello-cli` 애플리케이션은 다음 Dockerfile을 사용해요:

```dockerfile
FROM debian:stable-slim
RUN apt update && apt install -y \
    python3 \
    python3-prompt-toolkit
COPY ./hello /usr/bin/hello
RUN mkdir /kaboxer \
 && hello version > /kaboxer/version
```

마지막 줄들이 애플리케이션의 버전을 `/kaboxer/version`에 저장한다는 점을 제외하면 꽤 직관적이에요. 이는 Kaboxer가 패키징되는 애플리케이션의 "업스트림" 버전을 추적하는 데 사용돼요. 이미지의 시작점(데비안의 슬림 버전 기반)과 `hello-cli`의 의존성인 몇 개 패키지(`python3`와 `python3-prompt-toolkit`) 설치를 주목하세요. 이 경우 애플리케이션은 컨테이너 내부에 `/usr/bin/hello`로 설치되는 단일 `hello` 프로그램으로 구성돼요.

이제 이 Dockerfile이 있으면 앱용 도커 이미지를 빌드할 수 있어요.

### Kaboxer 메타 정보 추가하기

하지만 Kaboxer는 그 이상이에요. 이 이미지를 다양한 방식으로 배포할 수 있게 해주고, 더 중요하게는 해당 이미지를 실행하는 방법에 대한 지침을 추가하여 kaboxed 앱이 시스템 내에서 원활하게 통합되고 다른 앱처럼 실행될 수 있게 해줘요. 이는 `kaboxer.yaml` 파일을 통해 이루어져요; 같은 디렉토리의 다른 앱용 파일들과 구별하기 위해 `hello-cli.kaboxer.yaml`로 명명될 가능성이 높아요. 이 파일은 기계가 읽을 수 있도록 YAML 구문을 사용하지만, 사람이 읽기에도(그리고 더 중요하게는 사람이 작성하기에도) 꽤 쉬워요. 최소한의 `kaboxer.yaml` 파일은 다음과 같이 읽힐 수 있어요:

```yaml
application:
  id: hello-cli
  name: Hello World for Kaboxer (CLI)
  description: >
    hello-kbx is the hello-world application demonstrator for Kaboxer
packaging:
  revision: 1
components:
  default:
    run_mode: cli
    executable: /usr/bin/hello cli
```

필요한 정보는 명확한 구조로 섹션과 하위 섹션으로 나뉘어져 있어요. `application` 섹션은 앱 자체를 설명하고, `packaging` 섹션은 앱 패키징에 대한 메타데이터를 포함해요. 세 번째 섹션인 `components`는 앱의 각 구성 요소를 설명해요(`hello-cli`에는 하나만 있지만, 나중에 다중 구성 요소 앱을 볼 거예요). 각 하위 섹션 내에서 가장 중요한 필드는 컨테이너 내에서 앱을 시작하는 방법을 지정하는 `executable`과 이것이 어떤 종류의 앱인지 설명하는 `run_mode`예요. 우리의 경우 텍스트 모드 앱용인 `cli`를 선택했어요.

### 이미지 빌드하고 테스트하기

자, 이제 필요한 두 파일이 있어요. 이제 Kaboxer 이미지를 빌드해봅시다:

```console
kali@kali:~$ kaboxer build hello-cli
```

위에서 언급했듯이 Kaboxer는 Docker를 사용하는 데 권한이 필요하므로, `docker` 또는 `kaboxer` 그룹의 일부가 아니라면 이 명령어는 실패할 거예요.

빌드가 실패하면 Kaboxer는 무엇이 잘못되었는지 알아내는 데 많은 정보를 제공하지 않는다는 점을 주의하세요. 그런 경우에는 정확한 오류 메시지를 볼 수 있도록 `docker build`로 직접 Docker 이미지를 빌드해봐야 해요. 다음과 같이 할 수 있어요(`docker` 그룹 멤버십이나 루트 권한이 필요해요):

```console
kali@kali:~$ docker build -f hello-cli.Dockerfile -t kaboxer/hello-cli .
```

`kaboxer build`가 성공적으로 실행되면, 다음 명령어로 컨테이너에서 앱을 실행해봅시다:

```console
kali@kali:~$ kaboxer run hello-cli
[...]
PermissionError: [Errno 13] Permission denied: '/var/lib/hello-kbx'
```

앗! `hello-cli.kaboxer.yaml` 파일을 보면 `/var/lib/hello-kbx` 디렉토리가 `/data`에서 컨테이너에 마운트되기 위해 존재해야 한다는 것을 볼 수 있어요. 이 디렉토리를 생성하려면 루트 권한이 필요하고, 이는 APT로 `hello-cli-kbx`를 설치할 때 패키지 매니저가 할 일이에요.

여기 예시를 위해서는 루트 권한 없이 자동으로 생성될 수 있는 다른 디렉토리를 사용하는 것이 가장 간단한 해결책이에요. 대신 `/tmp/hello-kbx`를 사용해봅시다:

```console
$ sed -i 's;/var/lib/hello-kbx;/tmp/hello-kbx;' *.kaboxer.yaml
```

이제 앱을 다시 실행해봅시다:

```console
kali@kali:~$ kaboxer run hello-cli
fetch | save <value> | delete | exit ?
```

이제 `hello-cli`가 격리된 환경에서 실행되고 있어요!

혼자서는 할 수 있는 일이 많지 않고, 다음 단계는 `hello-server`를 빌드하고, 별도의 터미널에서 실행한 다음, `hello-cli`를 사용해서 서버와 통신하는 것이 될 거예요. 충분히 직관적이므로 이 부분은 다루지 않고, 대신 다음 주제인 데비안 패키징 파일로 넘어갑시다.

### 데비안 패키징 파일 추가하기

다른 애플리케이션과 마찬가지로 `dh_make`로 초기 패키징 파일을 추가할 수 있어요. 일반적인 변경사항 외에도 Kaboxer와의 통합을 활성화하기 위해 몇 가지 파일을 조정해요:

1. `debian/control`의 `Build-Depends`에 `kaboxer`를 추가하여 Kaboxer가 제공하는 debhelper 통합이 빌드 시점에 사용 가능하도록 해요
2. `debian/control`의 `Depends` 필드에 `${misc:Depends}`가 있는지 확인하여 `dh_kaboxer`가 적절한 의존성(현재 주로 Docker와 Kaboxer)을 주입할 수 있도록 해요
3. `dh $@` 호출을 `dh $@ --with kaboxer --buildsystem=kaboxer`로 변경하여 debhelper 통합을 활성화하도록 `debian/rules`를 수정해요

```console
kali@kali:~$ cat debian/control
Source: hello-kbx
[...]
Build-Depends: debhelper-compat (= 13), kaboxer
[...]

Package: hello-cli-kbx
Architecture: all
Depends: ${misc:Depends}
[...]

kali@kali:~$ cat debian/rules
#!/usr/bin/make -f

%:
	dh $@ --with=kaboxer --buildsystem=kaboxer
```

이 시점에서 이미 데비안 패키지를 빌드할 수 있지만 도커 이미지를 검색하는 방법을 모르기 때문에 제대로 작동하지 않을 거예요.

이미지를 어떤 도커 레지스트리에 푸시하고 `kaboxer.yaml` 파일을 수정하여 이를 가리키도록 할 수 있어요:

```console
kali@kali:~$ cat kaboxer.yaml
[...]
container:
  type: docker
  origin:
    registry:
      url: https://registry.gitlab.com
      image: kalilinux/packages/hello-kbx/hello
```

또는 `debian/rules`에서 `DH_KABOXER_BUILD_STRATEGY` 변수를 설정하여 빌드 시스템이 이미지를 빌드하고 패키지에 저장하도록 할 수 있어요(주의: 패키지가 매우 커질 거예요!):

```console
kali@kali:~$ cat debian/rules
#!/usr/bin/make -f

export DH_KABOXER_BUILD_STRATEGY=tarball

%:
	dh $@ --with=kaboxer --buildsystem=kaboxer
```

## 더 많은 Kaboxer 기능

### 리소스 공유 (네트워크 또는 파일 시스템)

앱이 격리된 컨테이너에서 실행되더라도, 파일 시스템의 일부를 공유하거나 네트워크에 접근하게 하여 외부 세계와 어떤 방식으로든 상호작용하게 하고 싶을 때가 많아요.

파일 시스템의 일부를 공유하는 것은 구성 요소에 대한 "마운트"를 정의하는 간단한 문제예요. "마운트"는 소스 디렉토리(호스트에서)를 대상 디렉토리로 컨테이너 내부에서 사용할 수 있게 만들어요. 이를 통해 컨테이너가 중지되고 제거되더라도 소스 디렉토리에 저장된 데이터는 건드리지 않으므로 실행 간에 데이터를 지속할 수 있어요. 또한 컨테이너 간에 파일 시스템의 일부를 공유할 수 있게 해줘요. 예를 들어, `/var/lib/hello-kbx` 디렉토리를 컨테이너에서 `/data`로 사용할 수 있게 하고 싶다면, 구성 요소 정의에 다음 섹션을 추가할 거예요:

```yaml
components:
  default:
    [...]
    mounts:
      - source: /var/lib/hello-kbx
        target: /data
```

상호작용이 필요한 애플리케이션의 경우, 컨테이너 외부에서 앱에 접근할 수 있도록 하는 것도 종종 필요해요. 많은 경우에 하나의 포트를 노출하는 것만으로 충분할 거예요. 예를 들어, 다음은 컨테이너에서 포트 8123을 노출해요(다른 것은 안 해요: 앱 자체가 내부적으로 여러 포트를 사용하더라도, 게시된 포트만 컨테이너 외부에서 접근 가능할 거예요):

```yaml
components:
  default:
    [...]
    publish_ports:
      - 8123
```

### 다중 구성 요소 애플리케이션

Kaboxer는 서버 부분과 클라이언트 부분 같은 다른 구성 요소를 가진 애플리케이션 패키징도 허용해요; 필요에 따라 격리되어 실행되거나 공유 컨테이너에서 실행될 수 있어요.

간단한 "공유 컨테이너" 시나리오는 `hello-allinone-kbx`로 설명돼요; `hello` 애플리케이션은 같은 Kaboxer 앱에 세 개의 구성 요소를 모두 가지고 있고, 같은 컨테이너에서 실행돼요. 그를 위해서는 서버 부분이 컨테이너에서 실행되면, 다른 부분들은 실행 중인 컨테이너 내에서 시작되어야 해요. 클라이언트 구성 요소가 다음을 선언할 때 Kaboxer가 이를 자동화해요:

```yaml
components:
  [...]
  gui:
    [...]
    reuse_container: true
```

더 복잡한 시나리오에서는 애플리케이션이 구성 요소들을 서로 격리해야 하지만 여전히 네트워크를 통해 통신하도록 허용해야 할 수도 있어요. 물론 서버 컨테이너의 외부로 포트를 게시하는 것도 가능하지만, 그러면 전 세계에 열려있게 돼요; 그런 경우에는 사설 네트워크를 정의하고 컨테이너들을 이 네트워크에 연결하는 것이 더 간단해요. 예를 들어, `hello-cli-kbx`와 `hello-server-kbx` 모두 다음 네트워크를 정의해요:

```yaml
components:
  default:
    [...]
    networks:
      - hello-kbx
```

이는 서버 부분이 서버 컨테이너 외부의 호스트에서 접근할 수 없더라도, cli 컨테이너에서는 접근할 수 있다는 의미예요: cli가 `hello-server-kbx` 호스트에 연결할 수 있고 연결이 사설 네트워크를 통해 다른 컨테이너로 자동으로 라우팅될 거예요.

### 칼리 메뉴에 애플리케이션 통합하기

애플리케이션이 칼리용으로 패키징될 때는 쉬운 발견을 위해 칼리 메뉴에 통합돼요. Kaboxer의 도움으로 패키징된 애플리케이션도 예외가 되어서는 안 되죠. Kaboxer는 이미 `.desktop` 파일을 생성하고 있으므로, 남은 일은 `Categories` 항목이 칼리 메뉴에서 사용되는 값들로 채워지도록 하는 것뿐이에요. [이 파일](https://gitlab.com/kalilinux/packages/kali-menu/-/blob/kali/master/menus/kali-applications.menu)에서 카테고리 목록을 찾을 수 있어요.

데스크톱 파일에 카테고리를 추가하는 것은 `kaboxer.yaml` 파일(`application` 아래의 `categories` 필드)을 통해 쉽게 할 수 있어요. 예를 들어 애플리케이션을 "Bluetooth tools"와 "Wireless Attacks" 카테고리에 넣는 방법은 다음과 같아요:

```yaml
application:
  [...]
  categories: Utility;06-02-bluetooth-tools;06-wireless-attacks
```

### 편리한 명령줄 헬퍼

명령줄에서 애플리케이션을 실행한다면, 항상 `kaboxer run --some-option --another-option APP`를 입력하는 것이 번거로울 수 있어요. 따라서 Kaboxer 는 애플리케이션 ID, 구성 요소 이름(구성 요소가 여러 개인 경우), 그리고 `-kbx` 접미사를 따서 명명된 헬퍼를 자동으로 생성해요.

이 헬퍼들을 사용하면 `hello-server-kbx start`로 hello 서버를 시작하고, `hello-cli-kbx`로 cli를 실행하고, 마지막으로 `hello-server-kbx stop`으로 서버를 중지할 수 있어요.

## GitLab CI로 빌드 자동화하기

Kaboxer 애플리케이션을 매우 쉽게 유지 관리하기 위해, Git 저장소에 Kaboxer 파일들을 저장하고 변경사항을 푸시할 때마다 GitLab CI가 Docker 이미지를 다시 빌드하도록 하고 있어요.

Kaboxer 프로젝트는 프로젝트에서 원격으로 포함할 수 있는 [일반적인 GitLab CI 파일](https://gitlab.com/kalilinux/tools/kaboxer/-/blob/master/gitlab-ci/build-docker-image.yml)을 포함하고 있어요([여기 예시](https://gitlab.com/kalilinux/packages/covenant-kbx/-/blob/kali/master/debian/kali-ci.yml)). 이는 이미지를 빌드하고 GitLab에서 제공하는 프로젝트의 Docker 레지스트리에 업로드할 거예요. `kaboxer.yaml` 파일은 해당 Docker 레지스트리의 URL을 참조하도록 업데이트되어야 하고, Kaboxer는 필요할 때 해당 위치에서 이미지를 다운로드할 거예요.

저장소에는 다른 일반적인 애플리케이션처럼 칼리 패키지를 빌드할 수 있도록 데비안 패키징 파일도 포함되어 있어요.

## 더 나아가기

이 튜토리얼은 Kaboxer 시작하기에 관한 것일 뿐이에요; 모든 옵션은 적절한 매뉴얼 페이지, 즉 `kaboxer(1)`과 `kaboxer.yaml(5)`에 더 자세히 설명되어 있어요. `kaboxer` 패키지는 설정과 작동을 설명하는 꽤 포괄적인 샘플 `hello-kbx` 애플리케이션도 제공해요; `/usr/share/doc/kaboxer/examples/hello-kbx`를 확인해보세요.
