---
title: 넷헌터 루트리스
description:
icon:
weight:
author: ["re4son",]
번역: ["xenix4845"]
---

<!-- Based on https://gitlab.com/kalilinux/nethunter/build-scripts/kali-nethunter-rootless -->

## 넷헌터 루트리스 에디션

##### *약속 없는 최대의 유연성*

보증을 무효화하지 않고 스톡 루팅되지 않은 안드로이드 기기에 칼리 넷헌터를 설치하세요.

![](010-NH-Rootless-Installation_Start_s.png)

![](020-NH-Rootless-KeX_s.png)

전제 조건:
--------------

안드로이드 기기
(스톡 수정되지 않은 기기, 루트나 커스텀 리커버리 불필요)

설치:
--------------

- [store.nethunter.com](https://store.nethunter.com/)에서 넷헌터-스토어 앱을 설치하세요
- 넷헌터 스토어에서 __Termux__, __NetHunter-KeX client__, __Hacker's keyboard__를 설치하세요
  _참고:_
       _설치 후 스토어 클라이언트에서 "install" 버튼이 "installed"로 바뀌지 않을 수 있어요 - 그냥 무시하세요._
      _일부 기기에서는 termux를 처음 시작할 때 "installing"을 표시하며 멈춘 것처럼 보일 수 있어요 - 그냥 엔터를 누르세요._

- Termux를 열고 다음을 입력하세요:

```console
kali@kali:~$ termux-setup-storage
kali@kali:~$ pkg install wget
kali@kali:~$ wget -O install-nethunter-termux https://offs.ec/2MceZWr
kali@kali:~$ chmod +x install-nethunter-termux
kali@kali:~$ ./install-nethunter-termux
```

사용법:
-------

Termux를 열고 다음 중 하나를 입력하세요:

| 명령어                   | 기능                                                      |
| ------------------------- | ------------------------------------------------------- |
| `nethunter`               | 칼리 넷헌터 명령줄 인터페이스 시작             |
| `nethunter kex passwd`    | KeX 비밀번호 구성 (첫 사용 전에만 필요) |
| `nethunter kex &`         | 칼리 넷헌터 데스크톱 경험 사용자 세션 시작   |
| `nethunter kex stop`      | 칼리 넷헌터 데스크톱 경험 중지                  |
| `nethunter <command>`     | 넷헌터 환경에서 <command> 실행                  |
| `nethunter -r`            | 루트로 칼리 넷헌터 CLI 시작                        |
| `nethunter -r kex passwd` | 루트용 KeX 비밀번호 구성                     |
| `nethunter -r kex &`      | 루트로 칼리 넷헌터 데스크톱 경험 시작         |
| `nethunter -r kex stop`   | 칼리 넷헌터 데스크톱 경험 루트 세션 중지    |
| `nethunter -r kex kill`   | 모든 KeX 세션 종료                                   |
| `nethunter -r <command>`  | 루트로 넷헌터 환경에서 `<command>` 실행        |

참고: `nethunter` 명령어는 `nh`로 줄여 쓸 수 있어요.
_팁: 비밀번호를 설정하지 않고 kex를 백그라운드에서 실행하는 경우(`&`), 비밀번호 입력이 요청되면 먼저 포그라운드로 가져오세요. 예: `fg <job id>` - 나중에 `Ctrl + z`와 `bg <job id>`로 다시 백그라운드로 보낼 수 있어요_

KeX를 사용하려면 KeX 클라이언트를 시작하고 비밀번호를 입력한 후 연결을 클릭하세요
_팁: 더 나은 보기 경험을 위해 KeX 클라이언트의 "Advanced Settings"에서 커스텀 해상도를 입력하세요_

## 넷헌터 에디션:

다양한 넷헌터 에디션의 비교는 [이 표](/docs/nethunter/#1-0-nethunter-editions)를 참조하세요.

## 팁:

1. 설치 후 첫 번째로 `sudo apt update && sudo apt full-upgrade -y`를 실행하여 [칼리를 업데이트](/docs/general-use/updating-kali/)하세요. 저장 공간이 충분하다면 `sudo apt install -y kali-linux-default`도 실행하는 것이 좋아요.
2. 모든 침투 테스트 도구가 작동해야 하지만 일부는 제한이 있을 수 있어요. 예를 들어 metasploit은 작동하지만 데이터베이스 지원이 없어요. 작동하지 않는 도구를 발견하면 [포럼](https://forums.kali.org/forumdisplay.php?14-NetHunter-Forums)에 게시해주세요.
3. "top"과 같은 일부 유틸리티는 루팅되지 않은 폰에서 실행되지 않아요.
4. 루트가 아닌 사용자도 chroot에서 루트 접근 권한을 가져요. 이것은 proot의 특성이에요. 이 점을 알아두세요.
5. Galaxy 폰은 루트가 아닌 사용자가 sudo를 사용하는 것을 막을 수 있어요. 대신 "su -c"를 사용하세요.
6. 모든 넷헌터 세션을 중지하고 termux 세션에서 다음을 입력하여 rootfs의 정기적인 백업을 수행하세요:
   `tar -cJf kali-arm64.tar.xz kali-arm64 && mv kali-arm64.tar.xz storage/downloads`
   이렇게 하면 백업이 안드로이드 다운로드 폴더에 저장돼요.
   _참고: 오래된 기기에서는 "arm64"를 "armhf"로 변경하세요_
7. 팁과 아이디어를 교환하고 넷헌터를 더욱 발전시키려는 커뮤니티의 일원이 되기 위해 [포럼](https://forums.kali.org/forumdisplay.php?14-NetHunter-Forums)에 참여해주세요.
