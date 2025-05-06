---
title: "문제 해결의 기초"
description:
icon:
weight:
author: ["zzuniPark"]
---

Linux 트러블슈팅은 여러 구성 요소들이 복합적으로 얽혀 있어서 매우 혼란스러울 수 있습니다. 이 문서는 가능한 한 많은 내용을 다루되 이해하기 쉽게 작성하는 것을 목표로 합니다. 빠진 내용이 있거나 개선할 부분이 있다고 생각되시면, 이슈를 제출하여 더 완전하도록 도와주세요.

### 로그 파일

-   `/var/log/` — 문제 해결에 필요한 대부분의 로그 파일이 들어 있는 디렉터리입니다.
-   `/var/log/syslog` — 커널 및 기타 프로그램·서비스의 메시지를 포함하고 있습니다.
-   `/var/log/apt/history.log`, `/var/log/apt/term.log`, `/var/log/dpkg.log` — 패키지 업데이트 중 오류가 발생한 지점을 확인하는 데 도움이 되는 파일들입니다.
-   `/var/log/auth.log` — 시스템 인증 이벤트를 기록합니다.
-   `/var/log/dmesg` — 커널 링 버퍼 정보를 포함하고 있습니다.
-   `/var/log/message` — 시스템 메시지를 기록합니다.
-   `/var/log/Xorg.0.log` — X 서버의 로그 메시지를 포함하고 있습니다.
-   `/var/log/lightdm/lightdm.log` — LightDM의 이벤트를 기록합니다.
-   `/var/log/kern.log` — 커널 메시지만 별도로 기록합니다.
-   `~/.xsession-errors` — X 윈도우 세션 내에서 발생한 그래픽 환경 오류를 기록합니다.

프로그램에 따라 자체 위치에 로그 파일을 둘 수도 있습니다. `man <패키지명>` 페이지를 확인하시면 해당 프로그램의 로그 파일 위치나 다른 관련 정보를 찾는 데 도움이 됩니다.

### 명령어 및 프로세스

여기에 나열된 모든 명령어는 `man` 페이지를 통해 자세한 사용법을 확인하시는 것이 좋습니다. 또한, 그래픽 도구에서 오류가 표시되지 않는 문제가 발생하면, 해당 도구를 명령줄에서 실행해 보시면 에러 메시지를 직접 확인할 수 있습니다.

-   [journalctl](https://manpages.debian.org/buster/systemd/journalctl.1.en.html) — systemd 저널의 내용을 보여줍니다.
-   [dmesg](https://manpages.debian.org/buster/util-linux/dmesg.1.en.html) — 커널 이벤트를 표시하며, 오류 원인을 파악하는 데 유용합니다.
-   [ip](https://manpages.debian.org/buster/iproute2/ip.8.en.html) — 네트워크 설정을 관리합니다.
-   [service](https://manpages.debian.org/buster/init-system-helpers/service.8.en.html) — System V init 스크립트 또는 upstart 작업을 실행합니다.
-   [systemctl](https://manpages.debian.org/buster/systemd/systemctl.1.en.html) — systemd 시스템 및 서비스 관리자를 제어합니다.
-   [df](https://manpages.debian.org/buster/coreutils/df.1.en.html) — 남은 디스크 공간을 확인하여 디스크 부족으로 인한 문제를 예방합니다.
-   [top](https://manpages.debian.org/buster/procps/top.1.en.html) — CPU·메모리 등 시스템 자원 사용 현황을 실시간으로 보여줍니다.
-   [dpkg](https://manpages.debian.org/buster/dpkg/dpkg.1.en.html) — 패키지 설치 상태를 확인하고 문제를 해결할 수 있습니다.
-   [apt](https://manpages.debian.org/buster/apt/apt.8.en.html) — 패키지 관리 및 문제 해결을 지원합니다.
-   [ps](https://manpages.debian.org/buster/procps/ps.1.en.html) 및 [kill](https://manpages.debian.org/buster/procps/kill.1.en.html) — 문제를 일으키는 프로세스를 찾아 종료할 수 있습니다.
-   [tail](https://manpages.debian.org/buster/coreutils/tail.1.en.html) (`-f` 옵션) — 로그 파일에 새로운 내용이 추가되는 것을 실시간으로 관찰할 수 있습니다.

### 가장 중요한 도구

Google은 거의 모든 문제 해결 사례에서 가장 중요한 도구가 될 것입니다. 로그 파일과 오류 출력을 활용하여 Google에서 검색하면, 원인과 해결 방법을 찾을 수 있는 경우가 많습니다.
