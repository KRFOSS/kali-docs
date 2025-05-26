---
title: 칼리 리눅스 & VirtualBox 게스트 확장 (레거시)
description:
icon:
archived: "true"
weight: 310
author: ["g0tmi1k",]
번역: ["xenix4845"]
---

**이 페이지는 구버전이에요**. **최신 버전**은 여기에서 찾을 수 있어요: [VirtualBox 게스트 확장 설치하기](/docs/virtualization/install-virtualbox-guest-additions/).

- - -

VirtualBox 내에서 칼리 리눅스를 "게스트"로 실행한다면, 이 글이 "게스트 확장" 도구를 성공적으로 설치하는 데 도움을 줄 거예요.

호환성 업데이트와 핵심 애플리케이션 및 게스트 확장의 향상된 안정성을 포함한 개선사항을 활용하려면 **VirtualBox 4.2.xx 이상**을 사용해야 해요.

## 준비하기

칼리 리눅스 가상 머신을 시작하고, 터미널 창을 열고 다음 명령어를 실행하여 리눅스 커널 헤더를 설치하세요:

```console
kali@kali:~$ sudo apt update
[...]
kali@kali:~$
kali@kali:~$ sudo apt install -y linux-headers-$( uname -r )
[...]
kali@kali:~$
```

- - -

완료되면 이제 **게스트 확장** CD-ROM 이미지를 연결할 수 있어요. VirtualBox 메뉴에서 **장치**를 선택한 다음 **게스트 확장 설치**를 선택하여 할 수 있어요.

이렇게 하면 칼리 리눅스 가상 머신의 가상 CD 드라이브에 게스트 확장 ISO가 마운트돼요. CD 자동 실행을 묻는 메시지가 나타나면 **취소** 버튼을 클릭하세요.

![](Figure-17-Cancel-Auto-Run.png)

## 설치하기

터미널 창에서 게스트 확장 CD-ROM에서 `VboxLinuxAdditions.run` 파일을 로컬 시스템의 경로로 복사하세요. **실행 가능한지 확인하고 파일을 실행**하여 설치를 시작하세요:

```console
kali@kali:~$ cp /media/cdrom/VBoxLinuxAdditions.run ~/Downloads/
kali@kali:~$ chmod 0755 ~/Downloads/VBoxLinuxAdditions.run
kali@kali:~$ cd ~/Downloads/
kali@kali:~/Downloads$ ./VBoxLinuxAdditions.run
```

게스트 확장 설치를 완료하려면 칼리 리눅스 VM을 재부팅하세요. 이제 완전한 마우스 및 화면 통합과 호스트 시스템과 폴더를 공유하는 기능을 갖게 될 거예요.

## 호스트 시스템과 공유 폴더 생성하기

이 섹션에서는 호스트 시스템의 폴더를 칼리 리눅스 VirtualBox "게스트"와 공유하는 방법을 설명해요.

VirtualBox 매니저에서 칼리 리눅스 VM 인스턴스를 선택하고 오른쪽 창에서 **공유 폴더** 링크를 클릭하세요. 공유 폴더를 추가하기 위한 팝업 창이 실행될 거예요. 이 창에서 **폴더 추가** 아이콘을 클릭하세요.

폴더 경로 텍스트 박스에 공유하고 싶은 폴더의 경로를 제공하거나, 드롭다운 화살표를 클릭하여 호스트 시스템에서 폴더 경로를 찾아보세요. **자동 마운트**와 **영구 만들기**를 허용하는 체크박스를 선택하고 메시지가 나타날 때 두 번 모두 **확인** 버튼을 클릭하세요.

![](Figure-20-Shared-folder-config.png)

공유 폴더는 이제 미디어 디렉토리에서 사용할 수 있을 거예요. 디렉토리에 더 쉽게 접근하기 위해 북마크나 링크를 만들 수 있어요.

![](Figure-21-Shared-folder-in-Kali.png)
