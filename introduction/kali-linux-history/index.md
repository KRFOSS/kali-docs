---
title: Kali Linux 역사
description:
icon:
weight: 450
author: ["g0tmi1k",]
번역: ["xenix4845"]
---

Kali Linux는 침투 테스팅 운영 체제를 구축하는 수년간의 지식과 경험을 바탕으로 만들어졌으며, 이는 여러 이전 프로젝트에 걸쳐 있습니다. 이 모든 프로젝트의 수명 동안 [팀](/about-us/)이 항상 작았기 때문에 개발자는 몇 명뿐이었습니다. 그 결과, Kali는 수년에 걸쳐 만들어졌고 긴 여정을 거쳐왔습니다.

첫 번째 프로젝트는 **Whoppix**라고 불렸으며, 이는 **WhiteHat Knoppix**의 약자였습니다. 이름에서 알 수 있듯이, 기본 OS로 Knoppix를 사용했습니다. Whoppix는 v2.0부터 v2.7까지의 버전이 있었습니다.

이는 다음 프로젝트인 **WHAX** _(또는 긴 이름으로, **WhiteHat Slax**)_로 이어졌습니다. 이름이 바뀐 이유는 기본 OS가 Knoppix에서 Slax로 변경되었기 때문입니다. WHAX는 Whoppix를 이어받는다는 의미로 v3부터 시작했습니다.

같은 시기에 유사한 OS인 **Auditor Security Collection** _(주로 **Auditor**로 줄여 부름)_이 개발되고 있었으며, 이 역시 Knoppix를 사용했습니다. 이러한 노력이 (WHAX와) 합쳐져서 **[BackTrack](https://www.backtrack-linux.org/)**이 탄생했습니다. BackTrack은 v1부터 v3까지는 Slackware를 기반으로 했지만, 나중에 v4와 v5에서는 Ubuntu로 전환했습니다.

이 모든 경험을 바탕으로, **Kali Linux**는 [2013년](/docs/introduction/press-release/)에 BackTrack 이후에 출시되었습니다. Kali는 처음에는 Debian 안정 버전을 기반으로 했으나, Kali가 롤링 OS가 되면서 [Debian](/docs/policy/kali-linux-relationship-with-debian/) 테스팅 버전으로 전환했습니다.

- - -

아래는 Kali Linux가 어떻게 만들어졌는지에 대한 대략적인 개요입니다:

| 날짜            | 프로젝트 출시             | 기본 OS                     |
|----------------|---------------------------|-----------------------------|
| 2004년 8월 30일 | Whoppix v2                | Knoppix                     |
| 2005년 7월 17일 | WHAX v3                   | Slax                        |
| 2006년 5월 26일 | BackTrack v1              | Slackware Live CD 10.2.0    |
| 2007년 3월 6일  | BackTrack v2              | Slackware Live CD 11.0.0    |
| 2008년 6월 19일 | BackTrack v3              | Slackware Live CD 12.0.0    |
| 2010년 1월 9일  | BackTrack v4 (Pwnsauce)   | Ubuntu 8.10 (Intrepid Ibex) |
| 2011년 5월 10일 | BackTrack v5 (Revolution) | Ubuntu 10.04 (Lucid Lynx)   |
| 2013년 3월 13일 | Kali Linux v1 (Moto)      | Debian 7 (Wheezy)           |
| 2015년 8월 11일 | Kali Linux v2 (Sana)      | Debian 8 (Jessie)           |
| 2016년 1월 16일 | Kali Linux Rolling        | Debian Testing              |

<!--
- 2004-08-30 - [whoppix v2](https://distrowatch.com/table.php?distribution=whoppix)
- 2005-07-17 - [WHAX 3.0](https://distrowatch.com/?newsid=02780)
- 2006-05-26 - [BackTrack v1 FINAL](https://web.archive.org/web/20080626100030/http://www.remote-exploit.org/backtrack_download_veryold.html)
  - https://secmaniac.blogspot.com/2006/05/backtrack-security-final-release.html
- 2006-10-13 - [BackTrack v2 BETA #1](https://web.archive.org/web/20080626100030/http://www.remote-exploit.org/backtrack_download_veryold.html)
  - https://web.archive.org/web/20061027172529/http://www.offsec.com/downloads.html
  - https://secmaniac.blogspot.com/2006/10/backtrack-v20-public-beta-has-been.html
- 2006-11-19 - [BackTrack v2 BETA #2](https://web.archive.org/web/20070202024932/http://www.remote-exploit.org/backtrack_download.html)
- 2007-03-06 - [BackTrack v2 FINAL](https://web.archive.org/web/20090529075045/http://www.remote-exploit.org:80/backtrack_devlog.html)
  - https://secmaniac.blogspot.com/2007/02/backtrack-20-final-due-end-of-febuary.html
- 2007-12-14 - [BackTrack v3 BETA](https://web.archive.org/web/20090529075045/http://www.remote-exploit.org:80/backtrack_devlog.html)
  - https://web.archive.org/web/20080501210105/http://www.remote-exploit.org:80/news.html
  - https://secmaniac.blogspot.com/2007/12/backtrack-3-beta-out.html
- 2008-06-19 - [BackTrack v3 FINAL](https://web.archive.org/web/20090529075045/http://www.remote-exploit.org:80/backtrack_devlog.html)
- 2009-02-11 - [BackTrack v4 BETA](https://web.archive.org/web/20090523080314/http://www.remote-exploit.org/backtrack_download.html)
- 2010-01-09 - [BackTrack v4 FINAL](https://web.archive.org/web/20100114220541/http://www.backtrack-linux.org/backtrack/backtrack4-release/)
  - https://www.offsec.com/blog/backtrack-4-final-release/
- 2010-08-04 - [BackTrack v4 R1](https://web.archive.org/web/20101130093002/http://www.backtrack-linux.org/backtrack/backtrack-4-r1-public-release/)
- 2010-11-19 - [BackTrack v4 R2](https://web.archive.org/web/20110128024129/http://www.backtrack-linux.org/backtrack/backtrack-4-r2-download/)
- 2011-05-10 - [BackTrack v5 FINAL](https://web.archive.org/web/20120226060247/http://www.backtrack-linux.org/backtrack/backtrack-5-release/)
- 2011-08-18 - [BackTrack v5 R1](https://web.archive.org/web/20120223221229/http://www.backtrack-linux.org/backtrack/backtrack-5-r1-released)
- 2012-03-01 - [BackTrack v5 R2](https://web.archive.org/web/20120303005136/http://www.backtrack-linux.org/backtrack/backtrack-5-r2-released/)
- 2012-08-13 - [BackTrack v5 R3](https://web.archive.org/web/20130314031147/http://www.backtrack-linux.org/backtrack/backtrack-5-r3-released/)
-->

{{% notice info %}}
이는 주요 릴리스만 나열한 것이며, 버그 수정, 릴리스, 도구 업데이트를 위한 마이너 릴리스도 있었습니다.
{{% /notice %}}

[Kali Linux의 역사에 대한 더 자세한 내용은 이 페이지](https://web.archive.org/web/20210914172345/https://kali.training/topic/a-bit-of-history/)와 우리의 [보도 자료](/docs/introduction/press-release/)를 참조하세요. 그리고 [Kali Linux의 릴리스에 대한 더 자세한 정보는 이 페이지](/releases/)를 참조하세요.
