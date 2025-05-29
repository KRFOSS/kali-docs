---
title: 칼리 넷헌터 설치하기
description:
icon:
weight:
author: ["re4son", "yesimxev",]
번역: ["xenix4845"]
---

<!--
## 개요

넷헌터를 설치하려면 다음 단계가 필요해요:

1. [사전 빌드된 이미지 다운로드 또는 자체 이미지 빌드하기](#1-넷헌터-사전-빌드된-이미지와-지원)
2. [기기를 개발자 모드로 설정하기](#2-기기를-개발자-모드로-설정하기)
3. [기기 잠금 해제하기](#3-안드로이드-기기의-잠금-해제-루팅-및-커스텀-리커버리-설치하기)
4. [TWRP 설치하기](#3-안드로이드-기기의-잠금-해제-루팅-및-커스텀-리커버리-설치하기)
5. [Magisk 플래시하기](#3-안드로이드-기기의-잠금-해제-루팅-및-커스텀-리커버리-설치하기)
6. [안드로이드 9 이상: "data" 포맷 및 Universal DM-Verity & ForceEncrypt Disabler 플래시하기](#4-universal-dm-verity--forceencrypt-disabler-플래시하기)
7. [넷헌터 설치하기](#5-넷헌터-이미지-설치하기)
8. [안드로이드 10 이상: 넷헌터 스토어에서 넷헌터 앱 업데이트하기](#5-넷헌터-이미지-설치하기)
9. [넷헌터 앱을 실행하여 설치 완료하기](#5-넷헌터-이미지-설치하기)
10. [Magisk를 통해 설치하기](#6-magisk-모듈로-넷헌터를-설치하는-새로운-방법)
-->

## 1. 넷헌터 사전 빌드된 이미지와 지원

칼리 넷헌터 팀은 선별된 기기 목록에 대해 사전 제작된 이미지를 빌드하고 [공식 넷헌터 다운로드 페이지](/get-kali/#kali-mobile)에 게시해요.

[기기](https://nethunter.kali.org/image-models.html)가 [사전 빌드 이미지](https://nethunter.kali.org/images.html)로 제공되지 않지만 [넷헌터에서 지원](https://nethunter.kali.org/device-kernels.html)되는 경우, ["넷헌터 빌드하기" 문서](/docs/nethunter/building-nethunter/)의 단계를 따라 자체 이미지를 쉽게 빌드할 수 있어요.

기기가 지원되지 않는 경우에도 제한된 하드웨어 기능으로 루팅된 모든 기기에서 넷헌터 라이트를 사용할 수 있어요.

- [일반 ARM64 전체 설치 프로그램: `kali-nethunter-20YY.X-generic-arm64-kalifs-full.zip`](https://kali.download/nethunter-images/current/)
- [일반 ARM64 최소 설치 프로그램: `kali-nethunter-20YY.X-generic-arm64-kalifs-minimal.zip`](https://kali.download/nethunter-images/current/)

다른 아키텍처도 있어요(예: armhf, i386, amd64), [여기](https://kali.download/nethunter-images/current/)에서 확인하세요.

## 2. 기기를 "개발자 모드"로 설정하기

설치를 시작하기 전에 기기에서 **개발자 모드**를 활성화해야 해요.
**설정** -> **휴대폰 정보**로 이동하여 개발자 모드가 활성화되었다는 알림이 표시될 때까지 **빌드 번호**를 **7번** 탭하면 됩니다.

기본 설정 페이지로 돌아가면 **개발자 옵션**이라는 새 섹션이 표시됩니다.

새로운 **개발자 옵션** 섹션을 탭하고 **고급 재부팅**과 **안드로이드 디버깅** 옵션을 모두 활성화하세요.

## 3. 안드로이드 기기의 잠금 해제, 루팅 및 커스텀 리커버리 설치하기

<!-- If updating, make sure to update: ./kali-docs/nethunter/installing-nethunter/index.md & ./kali-nethunter-kernels/bin/generate-android-versions.py-->
넷헌터는 [4.4/Kitkat부터 15/Fifteen까지의 안드로이드 버전](https://nethunter.kali.org/android-versions.html)을 실행하는 [99개 이상의 다양한 기기](https://nethunter.kali.org/device-kernels.html)를 지원해요.

넷헌터 설치 절차를 표준화했지만, **기기 잠금 해제, 루팅 및 커스텀 리커버리를 설치하는 단계는 기기마다 다르며 안드로이드 버전에 따라서도 다를 수 있어요**.

넷헌터를 위한 선호되는 **커스텀 리커버리**는 **[TWRP](https://twrp.me/Devices/)**예요.

넷헌터를 위해 기기를 **루팅**하는 데 선호되는 소프트웨어는 **[Magisk](https://xdaforums.com/t/magisk-the-magic-mask-for-android.3473445/)**예요.

기기의 잠금을 해제하고, 루팅하고, 커스텀 리커버리를 설치하는 적절한 가이드는 [XDA 개발자 포럼](https://xdaforums.com/)과 같은 인터넷 리소스를 참조하세요.

몇몇 기기에 대해서는 특정 가이드를 작성했어요:

- [제미나이 PDA 넷헌터](/docs/nethunter/installing-nethunter-on-the-gemini-pda/) _([제미나이 PDA ARM](/docs/arm/gemini-pda/))_
- [원플러스 원](/docs/nethunter/installing-nethunter-on-the-oneplus-one/)
- [원플러스 7](/docs/nethunter/installing-nethunter-on-the-oneplus-7/)
- [틱워치 프로](/docs/nethunter/installing-nethunter-on-the-ticwatch-pro/)
- [틱워치 프로 3](/docs/nethunter/installing-nethunter-on-the-ticwatch-pro-3/)

## 4. Universal DM-Verity & ForceEncrypt Disabler 플래시하기

**중요 참고 사항** 안드로이드 9, 10 및 11 사용자: 넷헌터를 설치하기 전에 [Universal DM-Verity, ForceEncrypt Disabler](https://xdaforums.com/t/deprecated-universal-dm-verity-forceencrypt-disk-quota-disabler-11-2-2020.3817389/)를 플래시하고 데이터 파티션을 포맷해야 해요.
Magisk는 암호화된 데이터 파티션에서 사용자 컨텍스트 변경을 지원하지 않으며, 데이터 파티션이 암호화된 경우 ssh를 통해 칼리 rootfs에 연결할 때 오류가 발생할 수 있어요(예: "Required key not available").

## 5. 넷헌터 이미지 설치하기 (리커버리)

이제 안드로이드 폰이 준비되었으니, 넷헌터 이미지를 기기로 전송하고, 리커버리 모드로 재부팅한 다음, 기기에 zip 파일을 플래시하세요. 완료되면 재부팅하고 넷헌터 앱을 실행하여 설정을 완료하세요!

**중요 참고 사항** 안드로이드 10 및 11 사용자: 넷헌터를 플래시한 후 넷헌터 스토어에서 넷헌터 앱을 업데이트하세요. Android 10은 "범위 지정 저장소" 제한을 도입했으며 이로 인해 넷헌터가 전통적으로 구성 파일을 저장하는 데 사용하는 저장소 위치를 사용할 수 없게 되었어요. 위치를 이동하고 가져오기/내보내기 기능을 구현하는 중이지만, 넷헌터를 플래시한 후 앱을 업데이트하면 새 기능이 구현될 때까지 현재 저장소 위치에 계속 액세스할 수 있는 해결 방법을 제공해요.

## 5. 넷헌터 이미지 설치하기 (Magisk 모듈)

_이것이 새롭고 권장되는 방법이에요!_

**중요 참고 사항** 강제 암호화가 비활성화되지 않은 기기:

- 동일한 칼리 넷헌터 설치 프로그램 zip 파일을 사용하여 Magisk에서 모듈로 설치
- 설치하는 동안 화면을 켜진 상태로 유지하세요. 화면이 잠기면 Android가 앱을 종료할 수 있어요
- 재부팅
