---
title: TicWatch Pro에 NetHunter 설치하기
description:
icon:
weight:
author: ["yesimxev",]
번역: ["xenix4845"]
---

![](NetHunter-TicWatch.png)

모든 버전이 지원됩니다 (TicWatch Pro, Pro 2020, Pro 4G/LTE)

# 포장 풀기부터 NetHunter 실행까지 6단계:

1. 부트로더 잠금 해제하기
2. 벤더 이미지, TWRP, 최적화된 WearOS 플래싱하기
3. TWRP에서 시스템 파티션 크기 조정하기
4. Magisk 앱을 플래싱하고 실행하여 루팅 과정 완료하기
5. NetHunter 플래싱하기
6. NetHunter 시계 화면(워치페이스) 설정하기

## 1. 부트로더 잠금 해제하기

- 시계를 USB 케이블로 PC에 연결하고 터미널을 실행하세요.
- 이미 휴대폰으로 시계 설정을 마쳤다면 설정에 접근할 수 있고, 그렇지 않으면 환영 화면에서 양쪽 버튼을 몇 초 동안 길게 누르세요.
- 시스템 -> 정보 -> 빌드 번호를 10번 탭해서 개발자 설정을 활성화하세요.
- ADB(Android Debug Bridge)를 활성화하고 USB를 다시 연결한 후 PC에서의 디버깅을 허용하세요.
- 터미널에서 `adb reboot bootloader` 명령으로 부트로더로 재부팅하세요.
- `fastboot flashing unlock` 명령으로 부트로더 잠금을 해제하세요.

## 2. 벤더 이미지, TWRP, 최적화된 WearOS 플래싱하기

설치 파일들을 다운로드해서 폴더에 압축을 풀어주세요.
Magisk 21.0 버전이 권장되며, 아래 링크에 포함되어 있습니다.

- ADB를 다시 활성화하고 `adb reboot bootloader` 명령으로 부트로더로 재부팅하세요.
- `fastboot flash vendor vendor.img`
- `fastboot flash recovery twrp-3.4.0-0-catfish.img`
- 측면 버튼을 사용하여 복구 모드를 선택하세요 (아래쪽 버튼으로 전환, 위쪽 버튼으로 선택)
- "Wipe(초기화) -> 다음 페이지 -> Format Data(데이터 포맷)" 선택
- Recovery(복구 모드)로 재부팅
- "Install(설치) -> ADB Sideload"를 선택하고 "Wipe Dalvik Cache, Wipe Cache(캐시 초기화)" 체크
- `adb sideload 2-ROM-PWDD.190617.074-AUG-09.zip`
- 재부팅 후 초기 설정 진행 (WearOS 앱을 통해 휴대폰과 페어링)

## 3. TWRP에서 시스템 파티션 크기 조정하기

- ADB를 다시 활성화하세요
- `adb reboot recovery`
- Wipe(초기화) -> 다음 페이지 -> File System Options(파일 시스템 옵션) - System 선택 - Resize(크기 조정) (0 대신 /system에 약 175MB 여유 공간 확보)

## 4. Magisk 앱을 플래싱하고 실행하여 루팅 과정 완료하기

- `adb sideload Magisk-v21.0.zip`
- System으로 재부팅
- Magisk Manager 실행
- 더 쉬운 사용을 위해 자동 업데이트 비활성화, 자동 응답에서 권한 부여 설정, 토스트 알림 비활성화를 권장합니다

## 5. NetHunter 플래싱하기

- `adb reboot recovery`
- 시스템 파티션이 마운트되어 있는지 확인하세요: Mount(마운트) -> System ("mount as RO"가 비활성화되어 있어야 함), 일부 사용자들은 NetHunter 설치 프로그램에서 /system이 마운트되지 않는다고 보고합니다
- Install(설치) -> ADB Sideload 선택
- `adb sideload` NetHunter 이미지
- 재부팅
- NetHunter 앱 실행 및 chroot(루트 환경) 설정
- 재부팅

## 6. NetHunter 시계 화면(워치페이스) 설정하기

- Play 스토어에서 Facer 앱을 휴대폰에 설치
- NetHunter 검색
- Facer 보조 앱을 시계에 설치
- 선택 및 동기화

### TicWatch Pro에서 Kali NetHunter 즐기기

## 다운로드 링크

- 필요 파일:
  - [Magisk](https://kali.download/nethunter-images/devices/catfish/Magisk-v21.0.zip)
  - [catfish](https://kali.download/nethunter-images/devices/catfish/twrp-3.4.0-0-catfish.img)용 TWRP 이미지
  - [벤더 이미지](https://kali.download/nethunter-images/devices/catfish/vendor.img)
  - [최적화된 WearOS](https://kali.download/nethunter-images/devices/catfish/2-ROM-PWDD.190617.074-AUG-09.zip)
- [TicWatch Pro NetHunter zip](https://www.kali.org/get-kali/#kali-mobile) - TicWatch 섹션에서 최신 업데이트 받기

## 추가 지원 앱

- Drivedroid: 최신 버전을 설치하려면 `adb install` 명령 사용
다운로드 링크: https://store.nethunter.com/repo/com.softwarebakery.drivedroid_105000.apk
- TotalCommander(전체 명령어): Ducky 스크립트 같은 파일 선택에 유용, `adb install` 방식 사용
다운로드 링크: https://www.totalcommander.ch/android/tcandroid323-armeabi.apk

## 지원되는 기능

- Kali 서비스
- 사용자 정의 명령어(Custom Commands)
- MAC 주소 변경기
- HID(Human Interface Device) 공격
- DuckHunter(키보드 자동화 도구)
- Bad USB(악성 USB)
- Nmap 스캔(네트워크 스캐너)
- WPS 공격(와이파이 보안 취약점)
- 블루투스 Arsenal(블루투스 관련 도구 모음)

## 예정된 기능 (확정 아님)

- Nexmon: 칩셋이 지원되므로 거의 완성됨 - 2024년 2분기 예정
- Router Keygen(라우터 키 생성기): 최적화 예정
- Hijacker(네트워크 도구): nexmon이 성공하면 2024년 2분기 예정
- Mifare Classic Tool(NFC 도구): android.hardware.nfc가 활성화된 OS 빌드 필요 - 2024년 4분기 예정

## 하드웨어 제한 사항

- 외부 어댑터를 위한 전력이 충분하지 않음 / OTG(On-The-Go)용 xhcdi 칩을 사용할 수 없음

{{% notice info %}}
TicWatch Pro는 스마트워치이기 때문에 일반 스마트폰과 달리 하드웨어 자원이 제한적입니다. 그래서 일부 고급 기능들은 제한될 수 있습니다.
{{% /notice %}}
