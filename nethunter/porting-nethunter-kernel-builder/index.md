---
title: 커널 빌더로 새 기기에 넷헌터 포팅하기
description:
icon:
weight: 17
author: ["yesimxev","serval123"]
번역: ["xenix4845"]
---

넷헌터를 새로운 기기에 포팅하려면 넷헌터가 어떻게 분리되어 있는지 이해하는 것이 중요해요. 넷헌터는 rootfs(chroot라고도 하지만 여기서는 rootfs라고 부를게요)와 커널로 나뉘어요. 대부분의 경우 rootfs는 칼리 리눅스만 포함하고 있기 때문에 안드로이드 기기에 중요하지 않아요. 커널은 블루투스, 무선 USB, HID 키보드(등) 작동에 필수적이에요.

또한 커널을 플래시하기 위해 잠금 해제된 부트로더가 있는 기기가 필요하고, 기기에서 루트를 얻을 수 있어야 해요. 루트는 busybox와 bootkali 같은 애플리케이션을 시스템에 쓰고, 칼리가 실행되도록 하는 명령어를 실행하기 위해 필요해요.

**기기를 포팅하려고 한다면 모든 것은 커널에 달려 있어요. 기기는 잠금 해제/루팅이 가능해야 해요**.

## 시작하기

[메인 문서 페이지](/docs/nethunter/building-nethunter/)의 지침을 이미 따랐다고 가정할게요. 모든 의존성을 충족했고 준비가 되었어요. 가장 먼저 하고 싶은 일은 테스트 커널을 빌드하는 것이에요.

## 커널 버전

기기가 오래된 경우 커널 버전이 3.4+ 이상인지 확인해주세요. 칼리 롤링으로 전환하면서 커널이 칼리 로드를 지원할 수 없는 chroot 내부에서 오류가 보이기 시작하고 있어요.

## 커널 소스 찾기

구글 넥서스가 선택된 이유 중 하나는 모든 커널 소스가 [구글의 자체 웹사이트](https://android.googlesource.com)를 통해 제공되기 때문이에요.
소스를 찾는 것은 제조업체에 따라 쉬울 수도 어려울 수도 있어요. 가장 일반적인 것들은 다음과 같아요.
[구글](https://android.googlesource.com/kernel/msm/) (아키텍처를 선택하고 브랜치를 보세요)
[LG](http://opensource.lge.com/index)
[삼성](http://opensource.samsung.com/reception.do)
[HTC](https://www.htcdev.com/devcenter/downloads)
[OnePlus](https://github.com/OnePlusOSS)
[모토로라](https://github.com/MotorolaMobilityLLC)
[소니](https://github.com/sonyxperiadev/kernel)

또 다른 좋은 자료는 보통 [XDA 포럼](https://forum.xda-developers.com/)이에요. 다른 누군가가 이미 작동하는 커널을 빌드했을 수 있고 GPL 하에서 소스를 제공해야 하거든요. XDA의 대부분의 커널 개발 페이지는 소스에 대한 링크를 제공해야 해요.

또한 깃허브의 [lineage-os](https://github.com/orgs/LineageOS/repositories) 커널 소스를 사용할 수 있고, 이것들은 원본 소스에 비해 포팅 학습을 시작하기가 더 쉬워요.

## 테스트 커널 만들기

툴체인을 아직 다운로드하지 않았다고 가정하고, 커널 폴더의 루트에 커널 빌더를 복제하고 환경을 준비하는 것으로 시작할 수 있어요:

```console
kali@kali:~$ git clone https://gitlab.com/kalilinux/nethunter/build-scripts/kali-nethunter-kernel-builder
kali@kali:~$ cd kali-nethunter-kernel-builder/
```
***[선택 단계]*** local.config.examples에서 사용 가능한 여러 예제 구성 파일을 찾을 수 있어요.
기기와 일치하는 것을 찾으면 local.config라는 이름으로 kali-nethunter-kernel에 복사하세요 ***(그런 다음 편집해서 모든 것이 커널과 일치하는지 확인하세요!)***

대괄호에서 언급했듯이 이 단계는 선택사항이에요.
이는 기기가 local.config.examples에 나열되지 않은 경우 이 단계를 따르는 것이 권장되지 않기 때문이에요. 모든 기기가 다르고 잘못된 구성을 사용하면 컴파일 오류가 발생하므로, 그런 경우에는 다음 단계로 건너뛰는 것이 좋아요. 

```console
kali@kali:~$ cp local.config.examples/CONFIG_YOU_WANT local.config
kali@kali:~$ nano local.config
```

***[권장 단계]*** config를 local.config라는 파일로 복사한 다음 local.config를 편집하여 기기의 커널과 일치하도록 만들어서 자신만의 local.config를 만들 수 있어요.
local.config 편집을 마친 후에는 편집한 것을 제외하고 모든 것을 삭제하세요.
***참고: 무엇을 편집해야 할지 모르는 경우, local.config.examples 안의 다양한 구성을 보고 아이디어를 얻으세요.***  
여전히 무엇을 편집해야 할지 이해하지 못한다면 build.sh 안을 보고 어떻게 작동하는지 이해해보세요  

```console
kali@kali:~$ cp config local.config
kali@kali:~$ nano local.config
```
local.config 파일이 생성된 후 build.sh를 실행하여 빌드 프로세스를 시작할 수 있어요

```console
kali@kali:~$ ./build.sh
```

![](nethunter-porting1.png)

먼저 ***S. Setup Environment and download toolchains***를 선택하세요.  
다음으로 ***2. Configure & compile kernel from scratch***로 테스트 커널을 빌드하세요

프롬프트가 나타나면 기기의 defconfig를 선택한 다음 저장하고 종료하여 빌드 프로세스를 시작하세요. 
 
빌드가 성공했다면 ***8. Edit Anykernel config***로 코드명, boot_block, slot_device 등과 같은 기기 세부 정보를 추가하세요  
***6. Create Anykernel zip***으로 첫 번째 테스트 커널 설치 프로그램을 만든 다음 커널을 플래시하여 작동하는 모습을 보세요. 

빌드가 ***성공하지 않았다면*** 다음을 시도해볼 수 있어요

사용한 local.config 파일이 모든 것이 제대로 구성되어 있는지 확인하세요  
무엇이 오류를 일으켰는지 정확히 알아서 구글에서 무엇이 잘못되었는지 검색하세요  
***4. Apply Nethunter kernel patches*** 안에 동일한 오류를 해결하는 패치가 있는지 확인하고 있다면 패치하세요  
위의 방법 중 어느 것도 작동하지 않으면 build.sh 스크립트를 보는 것과 [넷헌터 포팅](https://www.kali.org/docs/nethunter/porting-nethunter/#making-a-test-kernel) 사이를 오가며 문제를 수동으로 해결하려고 시도하는 것이 좋아요. 
  
***참고: 계속해서 커널을 수정할 예정이라면 시간을 절약하기 위해 ***3. Configure & recompile kernel from previous run***을 사용할 수 있어요.***
