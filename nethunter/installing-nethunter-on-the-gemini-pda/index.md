---
title: 제미나이 PDA에 넷헌터 설치하기
description:
icon:
weight:
author: ["re4son",]
번역: ["xenix4845"]
---

![](NetHunter-Gemini_tiny.png)

# 넷헌터 실행을 위한 4단계:

1. 기본 루팅된 안드로이드 이미지 플래시하기
2. Magisk Manager를 실행하여 루팅 과정 마무리하기
3. TWRP 리커버리 설치하기
4. 넷헌터 설치하기

## 1. 기본 루팅된 안드로이드 플래시하기

제미나이 PDA는 루팅되지 않은 안드로이드 이미지와 함께 제공되며, 이를 교체해야 해요.
"Pentester Pro" 이미지를 설치하면 루팅된 안드로이드와 칼리 리눅스 파티션이 함께 제공돼요. 자세한 내용은 여기를 참조하세요: [칼리 리눅스 제미나이 PDA](/docs/arm/gemini-pda/)

~~또는 다른 파티션 레이아웃을 원한다면 자신만의 이미지를 만드세요:[support.planetcom.co.uk/index.php/Linux_Flashing_Guide](https://support.planetcom.co.uk/index.php/Linux_Flashing_Guide)~~
**참고:** 이 글을 작성하는 시점(2019년 2월 26일)에는 공식 파티션 도구가 루팅된 안드로이드 이미지를 제공하지 않아요. 루팅된 것으로 광고된 이미지는 루팅되지 않은 버전과 동일해요.

새로 이미지를 설치한 제미나이를 안드로이드로 재부팅하고:
- 키보드 설정하기
- Wi-Fi에 연결하기
- "Play 스토어" 앱을 열고 google 계정으로 로그인하기
- 모든 앱 업데이트하기

## 2. Magisk Manager를 실행하여 루팅 과정 완료하기

- "Magisk Manager" 앱을 실행하고 앱을 업데이트하라는 메시지를 따르세요

때로는 안드로이드 이미지와 함께 제공되는 magisk 버전이 최신 버전의 magisk manager와 호환되지 않아 약간의 해결책이 필요할 수 있어요.
"Magisk Manager"가 설치된 "Magisk" 버전과 호환되지 않는다는 오류 메시지가 표시되면, 관리자 앱을 다운그레이드하고, TWRP를 통해 magisk를 업그레이드한 다음, 다시 관리자 앱을 업그레이드하세요:
- 기존 Magisk Manager 제거
- [github.com/topjohnwu/Magisk/releases](https://github.com/topjohnwu/Magisk/releases)에서 Magisk Manager v6.1.0 다운로드
- "보안" 설정으로 이동하여 "알 수 없는 소스에서 앱 설치 허용" 활성화
- 다운로드 폴더에서 magisk manager apk 설치
- Magisk Manager 열기, 업데이트에 "아니오" 선택
- "설정"에서 "업데이트 확인" 비활성화
- Magisk Manager 종료
- ~~[github.com/topjohnwu/Magisk/releases](https://github.com/topjohnwu/Magisk/releases)에서 최신 버전의 "Magisk"("Magisk Manager" 아님) 다운로드~~
- ~~TWRP 설치를 계속 진행하고 편리할 때 TWRP를 통해 새 magisk 버전을 설치하세요. 설치가 완료되면 "Magisk Manager" 앱을 열고 메시지에 따라 앱을 업데이트하세요~~
참고: 최신 버전의 Magisk는 자동 회전을 손상시키는 것 같아요. 지금은 이전 버전을 유지하는 것이 좋겠어요.

## 3. TWRP 리커버리 설치하기

- Play 스토어에서 "Official TWRP App" 설치
- "Official TWRP App" 열기
- 계정 선택
- "동의합니다", "루트 권한으로 실행" 체크
- "확인" 누르기
- "TWRP Flash" 누르기
- 루트 액세스 허용 메시지가 나타나면 "확인" 누르기
- 슈퍼유저 권한이 자동으로 부여되지 않으면 Magisk Manager를 열고 TWRP를 수동으로 활성화하세요
- 기기 선택 "Planet Gemini PDA -- geminipda"
- 최신 버전 선택
- 이미지 다운로드
- 뒤로 가기
- 이미지 선택
- 리커버리에 플래시

## 4. 넷헌터 설치하기

- 넷헌터 이미지를 여기에서 다운로드하세요: [kali.org/get-kali/](https://www.kali.org/get-kali/#kali-mobile) -> Gemini -> Gemini PDA (Nougat)
- Gemini PDA를 컴퓨터에 연결하세요
- 넷헌터 이미지를 Gemini PDA로 전송하세요
- "Official TWRP App"을 통해 Gemini PDA를 리커버리로 재부팅하세요 (TWRP Flash->Menu->Reboot-Reboot Recovery)
- "설치" 누르기
- 넷헌터 이미지 선택
- 스와이프하여 플래시 확인
- 재부팅
- "넷헌터" 앱 시작
- "허용"을 7번 클릭하고 루트 액세스 허용
- 설정 완료하기
- 재부팅

### 제미나이 PDA에서 칼리 넷헌터 즐기기

## 현재 상태

- HID 공격은 아직 지원되지 않아요. 드라이버는 계속 개발 중이에요.
- SearchSploit이 아직 완전히 작동하지 않아요.

이슈와 풀 리퀘스트를 제출하여 개발에 도움을 주세요. 많은 감사를 드립니다.
