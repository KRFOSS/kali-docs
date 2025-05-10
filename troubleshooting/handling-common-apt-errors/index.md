---
title: 자주 발생하는 APT 문제 다루기
description:
icon:
weight:
author: ["gamb1t", "rhertzog",]
번역: ["xenix4845"]
---

이 페이지는 앞으로 더 많은 내용이 추가될 예정이에요. 만약 여기서 다루지 않은 APT 관련 문제가 있다면, 문제 해결 방법이나 관련 내용을 [머지 리퀘스트](https://gitlab.com/kalilinux/documentation/kali-docs/-/merge_requests)로 제안해주시거나, 해결 방법을 모르겠다면 [이슈](https://gitlab.com/kalilinux/documentation/kali-docs/-/issues)를 남겨주세요. 여러분의 경험이 다른 사용자들에게 큰 도움이 될 수 있어요!

## 먼저 꼭 해야 할 것: 'apt update' 실행하기

혹시 `apt update` 실행하셨나요? 꼭 하셔야 해요. 패키지를 설치(`apt install ...`)하거나 시스템을 업그레이드(`apt full-upgrade`)하기 전에 **항상** `apt update`를 먼저 실행해야 해요. apt는 이런 식으로 동작하고, 이 과정을 거치지 않으면 정말 이상한 문제들이 생길 수 있어요.

```console
kali@kali:~$ sudo apt update
Get:1 http://http.kali.org/kali kali-rolling InRelease [41.2 kB]
[...]
```

## 패키지를 설치하려는데 설치가 안 될 때: 'apt full-upgrade' 사용하기

```console
kali@kali:~$ sudo apt upgrade
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
Calculating upgrade... Done
Some packages could not be installed. This may mean that you have
requested an impossible situation or if you are using the unstable
distribution that some required packages have not yet been created
or been moved out of Incoming.
The following information may help to resolve the situation:

The following packages have unmet dependencies:
 libwacom9 : Depends: libwacom-common (= 2.2.0-1) but 1.8-2 is to be installed
E: Broken packages
```

APT를 쓰다 보면 이런 "문제"를 자주 만날 수 있어요. 하지만 사실 이건 진짜 오류는 아니에요.

APT의 기본을 잠깐 짚고 넘어갈게요. 시스템을 업그레이드할 때는 `apt upgrade`와 `apt full-upgrade` 두 가지 명령어가 있어요.  
- `upgrade`는 현재 설치된 패키지들의 업그레이드만 진행하고, 필요하다면 새 패키지를 설치하지만 기존 패키지는 절대 삭제하지 않아요. 즉, 보수적인 업그레이드 방식이에요.
- `full-upgrade`는 시스템 전체를 업그레이드하기 위해 필요하다면 기존 패키지를 삭제하기도 해요.

Kali Linux처럼 롤링 배포판(rolling distribution, 계속 업데이트되는 배포판)에서는 패키지와 의존성이 자주 바뀌기 때문에, 새 패키지를 설치하려면 기존 패키지를 지워야 할 때가 많아요.  
_그래서 "apt upgrade"는 사실 별로 쓸모가 없고, 오히려 문제를 만들 수 있어요. 시스템을 최신 상태로 유지하려면 항상 "apt full-upgrade"를 쓰는 걸 추천해요._

물론 "full-upgrade"는 "upgrade"보다 약간 더 위험할 수 있어요. 가끔(정말 드물게) 중요한 패키지를 지우려고 할 수도 있거든요. 그래서 삭제될 패키지 목록을 꼭 확인해야 해요. 만약 삭제될 패키지가 너무 많거나(업그레이드/설치될 패키지에 비해), 뭔가 이상하다 싶으면 잠시 멈추는 게 좋아요. 경험이 많은 분들은 대처할 수 있지만, 잘 모르겠다면 며칠 뒤에 다시 시도해보세요. 패키지 상황이 바뀌어서 더 나은 결과가 나올 수 있어요.

혹시 정말 이상하다면 <https://bugs.kali.org>에 버그 리포트를 남겨주세요. 실행한 APT 명령어와 출력 결과를 같이 올려주시면, Kali Linux 개발자들이 상황을 파악해서 도와드릴 수 있어요. 패키지 의존성에 버그가 있어서 APT가 잘못 동작하는 경우라면, 저희가 고칠 수 있답니다.

정리하자면: 시스템 업그레이드는 항상 `apt full-upgrade`로 하세요.

## The following package has been kept back (패키지가 보류됨)

```console
kali@kali:~$ sudo apt full-upgrade
[...]
The following packages have been kept back:
  kali-desktop-xfce
[...]
```

가끔 APT가 어떤 패키지를 업그레이드하지 않고 보류하는 경우가 있어요. 보통은 다른 패키지를 삭제해야 해서 그래요. 이럴 땐 직접 해당 패키지를 설치(업그레이드)해주면 돼요:

```
kali@kali:~$ sudo apt install kali-desktop-xfce
[...]
The following packages will be REMOVED:
  pipewire-media-session
The following packages will be upgraded:
  [...] kali-desktop-xfce [...]
Do you want to continue? [Y/n] n
```

위 예시에서 보면 `pipewire-media-session` 때문에 `kali-desktop-xfce` 업그레이드가 막혀 있었어요. 하지만 직접 설치 명령을 내리면(이미 설치된 패키지에 대해 "apt install"을 실행하면 실제로는 업그레이드가 돼요), 어떤 패키지가 삭제될지 명확하게 보여주고, 동의하면 진행할 수 있어요. 이렇게 하면 문제를 쉽게 풀 수 있어요.

## 패키지가 더 최신 버전을 필요로 하는데, 새 버전이 없을 때

이런 상황은 `kali-dev`나 `kali-experimental` 같은 브랜치를 쓸 때 더 자주 발생할 수 있어요. 주로 두 가지 이유가 있는데,  
첫째는 해당 패키지가 아직 테스트 중이라 곧 저장소에 올라올 예정이거나,  
둘째는 의존성 충돌이 있어서 먼저 해결되어야 하는 경우예요.

내 상황이 어떤 경우인지 알고 싶다면, Debian 쪽 패키지 페이지를 찾아보거나 Kali 패키지라면 [bugs.kali.org](https://bugs.kali.org/)에 버그 리포트가 있는지 확인해보세요. 어쨌든 대부분의 경우 1~2주 내에 해결되는 경우가 많아요.

## 패키지가 파일을 덮어쓰려고 해서 오류가 날 때

패키지 간에 파일이 이동되었는데, 패키지 메타데이터에 제대로 표시가 안 되어 있을 때 이런 일이 생길 수 있어요. 대부분은 설치나 업그레이드를 다시 시도하면 해결돼요. 그래도 안 된다면, APT 명령어에 `-o dpkg::options::="--force-overwrite"` 옵션을 추가해서 dpkg(패키지 관리자)에게 덮어쓰기 오류를 무시하라고 지시할 수 있어요.

이 오류에 대해 더 자세히 알고 싶다면 [여기](https://raphaelhertzog.com/2011/08/01/understanding-dpkgs-file-overwrite-error/)에서 추가 정보를 확인할 수 있어요.
