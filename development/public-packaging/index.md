---
title: 퍼블릭 패키징
description:
icon:
archived: "true"
weight:
author: ["gamb1t",]
번역: ["xenix4845"]
---

# 칼리 생태계에 참여하기

칼리 리눅스는 정기적으로 유지 관리하고 추가하는 많은 도구들을 가지고 있어요. 하지만 요청 수가 많기 때문에 추가될 패키지에 대한 모든 요청을 항상 고려할 수는 없어요. 이 때문에 우리가 요청을 보고 제대로 고려할 수 있는 가능성을 높이기 위해 사용자가 따라야 할 요구사항을 개발했어요. [버그 트래커](https://bugs.kali.org/)를 방문해봤다면 특정 패키지 요청에 게시된 요구사항을 눈치챘을 수도 있어요. 그렇지 않다면 걱정 마세요. 요구사항은 다음과 같아요:

```plaintext
- [Name] - 도구의 이름
- [Version] - 어떤 버전의 도구가 추가되어야 하나요?
--- 소스 제어(git 같은)를 사용한다면 일치하는 릴리스가 있는지 확인해주세요(예: git tag)
- [Homepage] - 도구를 온라인에서 어디에서 찾을 수 있나요? 더 많은 정보를 얻기 위해 어디로 가야 하나요?
- [Download] - 도구를 얻기 위해 어디로 가야 하나요? 다운로드 페이지나 최신 버전 링크
- [Author] - 누가 도구를 만들었나요?
- [License] - 소프트웨어는 어떻게 배포되나요? 어떤 조건이 함께 오나요?
- [Description] - 도구가 무엇에 관한 것인가요? 무엇을 하나요?
- [Dependencies] - 도구가 작동하기 위해 무엇이 필요한가요?
- [Similar tools] - 다른 어떤 도구들이 있나요?
- [Activity] - 프로젝트는 언제 시작되었나요? 여전히 활발하게 배포되고 있나요?
- [How to install] - 어떻게 컴파일하나요?
--- 참고: 소스 코드로 획득하는 것(예: git clone/svn checkout)은 사용할 수 없어요 - 또한 헤드에서 다운로드하는 것도 마찬가지예요. "tag"나 "release" 버전을 사용해주세요.
- [How to use] - 이를 시연하기 위한 기본 명령어/기능은 무엇인가요?
```

이 시점에서 '이것이 내가 참여하는 것과 어떤 관련이 있을까?'라고 물어볼 수도 있어요. 그건 간단해요: [GitLab으로의 이동](https://gitlab.com/kalilinux) 덕분에 사용자들이 이제 도구를 추가하기 위한 대부분의 작업을 스스로 할 수 있게 되었어요. 요청된 모든 도구를 여전히 추가하지는 않을 것이라는 점을 명심하세요; 아마도 같은 일을 하지만 더 오래된 다른 도구가 있거나, 도구가 너무 새로워서 더 많은 사용자의 의견을 얻을 시간이 필요할 수도 있어요. 참여하고 싶은 사람들이 먼저 해야 할 몇 가지가 있는데, 이제 그것을 안내해드릴게요.

관련 문서:
- [패키징을 위한 시스템 설정](/docs/development/setting-up-packaging-system/)

- - -

참여하고 패키지를 유지 관리하고 싶은 분들을 위해, 병합 요청을 생성해야 해요. 이 과정에서 알아야 할 두 가지가 있어요. 첫 번째는 초기 패키지를 생성하는 방법이고 다른 하나는 계속 지원하는 방법이에요. 이해를 쉽게 하기 위해 시각적으로 살펴봅시다.

## 초기 폴더 생성하기

"패키징"을 시작하기 전에 폴더를 제대로 준비해야 해요. 패키징하려는 도구가 이미 준비되어 있고 여러분이 소유자라고 가정하면, 별도의 브랜치를 생성하고 "debian" 디렉토리에 직접 추가하는 것이 권장돼요. 이것이 완료되면 "데비안 파일 생성하기"로 건너뛰고 거기서부터 따라하세요.

그렇지 않으면 릴리스가 있다면 릴리스를 가져와야 해요. 릴리스가 **있다면** 해당 헤더로 건너뛰세요. 릴리스가 **없다면** 저장소를 복제하고 다음 명령어를 실행하세요:

```console
kali@kali:~$ git clone https://github.com/ambionics/phpggc.git phpgcc-official
kali@kali:~$
kali@kali:~$ cd phpggc-official/
kali@kali:~$
kali@kali:~/phpggc-official$ git archive --format=tar master | gzip -c > ../PACKAGE_YEARMONTHDAY.orig.tar.gz
kali@kali:~/phpggc-official$
```

패키지와 날짜를 모두 적절한 이름으로 변경해야 해요.

빈 git 저장소를 생성한 다음 복제하고, 그 다음 도구를 가져올 수 있어요. _이 단계를 위해 자신만의 패키지용 새로운 빈 저장소를 생성해야 해요._

```console
kali@kali:~$ git clone git@gitlab.com:PackageAllTheThings/phpggc.git
kali@kali:~$
kali@kali:~$ cd phpggc/
kali@kali:~/phpggc$ gbp import-orig ../phpggc_0.20191028.orig.tar.gz
kali@kali:~/phpggc$
kali@kali:~/phpggc$ git checkout -b kali/master
kali@kali:~/phpggc$
kali@kali:~/phpggc$ git branch -D master
kali@kali:~/phpggc$
kali@kali:~/phpggc$ git push -u origin --all
kali@kali:~/phpggc$
```

#### 릴리스로 작업하기
릴리스를 가져오기 위해서는 릴리스가 없을 때와 유사한 과정을 따라요. 먼저 최신 릴리스 tar.gz 링크를 가져와서 파일을 적절한 데비안 형식으로 출력해요:

```console
kali@kali:~$ wget -O phpggc_1.0.1.orig.tar.gz https://gitlab.com/PackageAllTheThings/phpggc/archive/v1.0.1.tar.gz
kali@kali:~$
kali@kali:~$ git clone git@gitlab.com:PackageAllTheThings/phpggc.git
kali@kali:~$ cd phpggc/
kali@kali:~/phpggc$ gbp import-orig ../phpggc_1.0.1.orig.tar.gz
kali@kali:~/phpggc$
kali@kali:~/phpggc$ git checkout -b kali/master
kali@kali:~/phpggc$
kali@kali:~/phpggc$ git branch -D master
kali@kali:~/phpggc$
kali@kali:~/phpggc$ git push -u origin --all
kali@kali:~/phpggc$
```

## 데비안 파일 생성하기

먼저 기본 데비안 파일들을 생성하고 필요하지 않은 일부를 제거해야 해요. 프롬프트가 나타나면 패키지 타입으로 single을 선택하고, 다른 모든 것이 올바르다고 가정하면 나머지는 기본값을 사용해요:

```console
kali@kali:~/phpggc$ dh_make -p phpggc_0.20191028
kali@kali:~/phpggc$
kali@kali:~/phpggc$ cd debian/
kali@kali:~/phpggc/debian$ rm *.ex *.EX README.* *.docs
kali@kali:~/phpggc/debian$
```

다음으로 적절한 정보로 일부 파일을 편집해야 해요:

```console
kali@kali:~/phpggc/debian$ vim control
kali@kali:~/phpggc/debian$
kali@kali:~/phpggc/debian$ cat control
Source: phpggc
Section: net
Priority: optional
Maintainer: Kali Developers <devel@kali.org>
Uploaders: First Last <email@domain.com>
Build-Depends: debhelper (>= 11)
Standards-Version: 4.1.3
Homepage: https://github.com/ambionics/phpggc
Vcs-Git: https://gitlab.com/PackageAllTheThings/packages/phpggc.git
Vcs-Browser: https://gitlab.com/PackageAllTheThings/packages/phpggc

Package: phpggc
Architecture: any
Depends: ${shlibs:Depends}, ${misc:Depends}, php-cli
Description: A library of unserialize() payloads
 PHPGGC is a library of unserialize() payloads
 along with a tool to generate them, from
 command line or programmatically.
kali@kali:~$
```

여기서 주목해야 할 것들이 많아요. Section, priority, maintainer, uploaders, homepage, depends, description이 모두 변경돼요. 하나씩 살펴보면:

- 'section'은 도구가 어떤 타입인지예요. 일반적인 영역(web, net 등)에서 무엇일 수 있는지 최선의 추측을 하세요. 필요하면 변경할 거예요
- Priority는 optional로 설정할 수 있어요
- Maintainer는 항상 "Kali Developers <devel@kali.org>"이어야 하고 Uploaders는 여러분의 이름(계정 이름일 수 있음)과 계정과 연결된 이메일이어야 해요
- Homepage는 도구가 원래 있던 곳이에요. Depends는 도구가 작동하기 위해 설치되어야 하는 것으로, 이 경우 php가 필요해요
- Vcs-*는 패키지를 푸시하는 저장소로 설정되어야 해요.
- Description은 짧은 설명과 패키지에 포함된 내용을 설명하는 확장된 설명의 조합이에요

```console
kali@kali:~/phpggc/debian$ vim changelog
kali@kali:~/phpggc/debian$
kali@kali:~/phpggc/debian$ cat changelog
phpggc (0.20191028-0kali1) kali-dev; urgency=medium

  * Initial release

 -- First Last <email@domain.com>  Mon, 28 Oct 2019 19:29:57 -0400
kali@kali:~$
```

urgency 전에 unstable이 아닌 kali-dev로 설정해야 해요. 그렇지 않으면 나중에 sbuild에서 문제가 발생할 거예요. "Initial release" 이후의 모든 것을 제거할 수 있어요. 또한 업스트림 데비안 패키지에서 사용될 버전과의 충돌을 피하기 위해 기본 "-1" 대신 "-0kali1"의 데비안 수정본을 사용해요:

```console
kali@kali:~/phpggc/debian$ vim copyright
kali@kali:~/phpggc/debian$
kali@kali:~/phpggc/debian$ cat copyright
Format: https://www.debian.org/doc/packaging-manuals/copyright-format/1.0/
Upstream-Name: phpggc
Upstream-Contact: contact@ambionics.io
Source: https://github.com/ambionics/phpggc

Files: *
Copyright: 2019 ambionics <contact@ambionics.io>
License: Apache License 2.0
 /usr/share/common-licenses/Apache-2.0

Files: debian/*
Copyright: 2019 First Last <email@domain.com>
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
 .
 On Debian systems, the complete text of the GNU General
 Public License version 2 can be found in "/usr/share/common-licenses/GPL-2".
kali@kali:~$
```

copyright를 처음 열면 많은 잡동사니가 있을 거예요. 대부분 삭제할 수 있지만 일부 정보가 중요할 수 있으므로 제거하는 내용을 읽어야 해요. `Files: *`의 저작권과 라이선스는 원본 패키지가 사용하는 것이 될 거예요. 이 경우 원본 패키지는 Apache License 2.0을 사용했고, 데비안에 전체 라이선스가 이미 있으므로 위와 같이 링크할 수 있어요. 놓친 라이선스가 있는지 대략적인 아이디어를 제공하는 유용한 명령어는 `licensecheck -r . --copyright`예요:

```console
kali@kali:~/phpggc/debian$ vim watch
kali@kali:~/phpggc/debian$
kali@kali:~/phpggc/debian$ cat watch
version=4
opts=filenamemangle=s/.+\/v?(\d\S*)\.tar\.gz/phpggc-$1\.tar\.gz/ \
  https://github.com/ambionics/phpggc/tags .*/v?(\d\S*)\.tar\.gz
kali@kali:~$
```

이 파일은 조금 위협적으로 보일 수 있지만, 실제로 변경해야 하는 것은 시연된 바와 같이 매우 쉬워요:

```plaintext
opts=filenamemangle=s/.+\/v?(\d\S*)\.tar\.gz/PACKAGENAME-$1\.tar\.gz/ \
  ORIGINALGITLINK/tags .*/v?(\d\S*)\.tar\.gz
```

이 파일은 업스트림의 릴리스 버전 번호의 변경사항을 _감시_하여 나중에 패키지를 적시에 더 쉽게 업데이트할 수 있게 해줘요. [데비안 위키](https://wiki.debian.org/debian/watch)에서 더 많은 예시를 볼 수 있어요.

## 마무리 작업

지금 패키지를 빌드한다면 설치되지 않을 거예요. 이를 해결하기 위해 .install 파일과 헬퍼 스크립트를 생성해봅시다. 이 두 파일을 생성하는 이유는 대부분의 경우 둘 다 작동하기 때문이에요. 일부 경우에는 심볼릭 링크 사용과 같은 다른 방법이 작동하지 않을 수 있고 변경사항을 만들어야 해요. 지금 모든 시나리오를 설명할 수 없으므로 대부분의 경우 작동하는 것으로 가겠어요:

```console
kali@kali:~/phpggc/debian$ mkdir -p helper-script/
kali@kali:~/phpggc/debian$
kali@kali:~/phpggc/debian$ vim phpggc.install
kali@kali:~/phpggc/debian$
kali@kali:~/phpggc/debian$ cat phpggc.install
lib usr/share/phpggc/
phpggc usr/share/phpggc/
templates usr/share/phpggc/
gadgetchains usr/share/phpggc/
debian/helper-script/phpggc usr/bin/
kali@kali:~/phpggc/debian$
kali@kali:~/phpggc/debian$ vim helper-script/phpggc
kali@kali:~/phpggc/debian$
kali@kali:~$ cat helper-script/phpggc
#!/bin/sh
cd /usr/share/phpggc/
exec ./phpggc
kali@kali:~/phpggc/debian$
```

여러분 중 일부는 뭔가 이상한 것을 눈치채고 .install 파일의 형식이 어떻게 된 건지 궁금해할 수도 있어요. 패키지 빌더가 해석하는 방식으로는 "usr/" 시작 부분의 `/`가 있으면 문제가 생기고, 마찬가지로 끝에 슬래시가 없어도 문제가 돼요. ".install" 파일에 설치될 모든 파일을 포함시켜요. 헬퍼 스크립트에서는 해당 디렉토리로 가서 파일을 실행해요.

이제 모든 것이 완료되었으므로 모든 것을 git에 푸시하고 시도해볼 수 있어요!

```console
kali@kali:~/phpggc/debian$ cd ../
kali@kali:~/phpggc$
kali@kali:~/phpggc$ git add .
kali@kali:~/phpggc$
kali@kali:~/phpggc$ git commit -m "Packaged up!"
kali@kali:~/phpggc$
kali@kali:~/phpggc$ git push
kali@kali:~/phpggc$
kali@kali:~/phpggc$ gbp buildpackage --git-builder=sbuild
kali@kali:~$
```

이는 조금 시간이 걸릴 수 있고, 결국 몇 가지가 발생할 수 있어요. lintian이 "Failed"라고 하고 오류가 있다면, 구글링해보는 것을 권장하고 해결책을 찾을 수 없다면 도움을 드릴 수 있는 [포럼](https://forums.kali.org/forumdisplay.php?8-Kali-Linux-Development)에 게시글을 제출하세요. lintian이 실패하지 않으면 `/home/$USER/kali/build-area/`에서 패키지를 찾을 수 있어요. dpkg를 사용해서 패키지를 설치하고 실행해보며 테스트해보세요.

## 메뉴 아이콘

메뉴 아이콘을 위해 `.desktop` 파일을 생성해야 한다면, [GitLab의 kali-menu 패키지](https://gitlab.com/kalilinux/packages/kali-menu)에 병합 요청을 제출하는 것이 가장 좋아요. 패키지를 포크하고, 복제하고, 원하는 파일을 추가한 다음, 변경사항과 함께 병합 요청을 제출할 수 있어요. 아래는 `.desktop` 파일이 어떻게 만들어져야 하는지의 예시예요. "Categories"를 도구에 가장 가깝게 맞는 것으로 변경해야 하고, 하나 이상을 포함하는 것도 가능해요:

```console
kali@kali:~$ vim desktop-files/phpggc.desktop
kali@kali:~$
kali@kali:~$ cat desktop-files/phpggc.desktop
[Desktop Entry]
Name=PHPGGC
Encoding=UTF-8
Exec=/usr/share/kali-menu/exec-in-shell phpggc
Icon=kali-menu
StartupNotify=false
Terminal=true
Type=Application
Categories=08-exploitation-tools;
X-Kali-Package=phpggc
kali@kali:~$
```

# 트래커에 제출하기

마지막으로 할 일이 하나 남았어요; 우리에게 제출하는 것! 이를 위해 [칼리의 이슈 트래커](https://bugs.kali.org/)로 가봅시다. "New Tool Requests" 카테고리로 새로운 이슈를 제출하려고 해요. 제목은 "phpggc: a library of unserialize() payloads along with a tool to generate them"으로 하고 설명에는 앞서 나온 목록을 포함할 거예요:

```plaintext
- [Name] - PHPGGC
- [Version] - 0.20191028
- [Homepage] - https://github.com/ambionics/phpggc
- [Package] - https://gitlab.com/PackageAllTheThings/phpggc
- [Author] - Ambionics
- [License] - Apache License 2.0
- [Description] - PHPGGC is a library of unserialize() payloads along with a tool to generate them, from command line or programmatically
- [Dependencies] - PHP
- [Similar tools] - unknown
- [Activity] - There was a commit last month
- [How to use] - phpggc -h
```

완료되면 이슈를 제출하기만 하면 우리가 검토할 거예요.

## 승인 후 어떤 일이 일어나는가

패키지를 칼리에 승인하면, 우리는 git 저장소를 kalilinux/packages GitLab 그룹으로 복제해요(포킹하고 관계를 삭제하는 방식으로). 이 때문에 향후 변경사항은 병합 요청으로 제출되어야 해요. 이를 위해서는 사용자가 우리의 git 저장소를 자신의 계정으로 포크해야 해요.
