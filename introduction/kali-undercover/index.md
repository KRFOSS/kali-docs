---
title: 칼리 리눅스, 눈에 띄지 않게 사용하기
description:
icon:
weight: 100
author: ["Croluy","theGorkha",]
번역: ["xenix4845"]
---

**칼리 언더커버(Undercover)**는 칼리 리눅스의 겉모습을 **윈도우 10**처럼 바꿔주는 스크립트 모음이에요.

이 기능은 [**칼리 리눅스 2019.4**](/blog/kali-linux-2019-4-release/) 버전에서 *눈에 띄지 않게 숨기자*는 재미있는 아이디어로 출시되었어요.

![](kali-undercover-1.gif)

#### "언더커버" 모드로 전환하기

언더커버 모드로 바꾸는 건 정말 쉬워요. 이 명령어만 입력하면 돼요:

```console
kali@kali:~$ kali-undercover
kali@kali:~$
```

아니면 데스크톱 메뉴에서 **"칼리 언더커버 모드(Kali Undercover Mode)"**를 찾아 클릭해도 돼요.

**휙!** 이제 주변 사람들이 여러분이 윈도우 10을 사용한다고 생각할 만한 겉모습으로 바뀌어서, 지나가는 사람들의 시선으로부터 _(거의)_ 안전하게 숨었어요.

#### 원래 테마로 돌아가기

다시 칼리 리눅스 원래 모습으로 **돌아가려면** 같은 명령어를 한 번 더 입력하면 돼요:

```console
kali@kali:~$ kali-undercover
kali@kali:~$
```

**짜잔!** 다시 돌아왔어요! 이제 모든 데스크톱 설정이 원래대로 복구됐어요.

- - -

#### 칼리 리눅스에서 언더커버 모드의 필요성

**칼리 언더커버** 모드를 만든 주된 이유는 _공공장소_에서 칼리 리눅스를 사용할 때 **불필요한 관심을 피하기 위해서**예요.

이런 상황을 상상해보세요: 여러분은 의뢰인의 허락을 받고 보안 점검(침투 테스팅)을 하기 위해 의뢰인의 사무실이나 로비에서 정보 수집이나 **칼리 리눅스**로 작업을 하고 있어요.

그런데 의뢰인의 사무실에 있는 누군가 또는 지나가는 사람이 칼리 리눅스 화면이나 배경화면을 보게 된다면, 여러분이 _허가받은 작업_ 을 하고 있음에도 무언가 수상한 일을 꾸미고 있다고 생각해서 관리자에게 신고할 수도 있어요. 몰래 작업하려고 들인 노력이 모두 물거품이 되겠죠! 그리고 그 이유가 뭘까요? 단지 **배경화면** 때문이라니! 조용히 작업해달라고 부탁한 의뢰인에게는 정말 곤란한 상황이죠.

그래서 공공장소에서 원치 않는 시선을 피하려면 **"언더커버!"** 모드로 전환하는 게 최선이에요.
