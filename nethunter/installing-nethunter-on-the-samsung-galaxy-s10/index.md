---
title: 삼성 갤럭시 S10에 넷헌터 설치하기
description:
icon:
weight:
author: ["v0lk3n","yesimxev",]
번역: ["xenix4845"]
---

# 정보

> 이 설치 가이드와 사용된 파일들은 삼성 갤럭시 S10 엑시노스9820 버전용이에요.

## 기능

| 기능  | 지원 |
| :--------------- | -----:|
| BT_RFCOMM | ✅ |
| INTERNAL_BT | ✅ |
| RTL_BT | ✅ |
| HID-4 | ✅ |
| Injection | ✅ |
| ATH9K_HTC | ✅ |
| RTL88XX | ✅ |
| RTL8812AU | ✅ |
| RTL8821AU | ✅ |
| RTL8814AU | ✅ |
| RTL8188EUS (모듈) | ✅ |
| RTL88x2BU | ✅ |
| NFS | ✅ |
| CAN (선택적 모듈 포함) | ✅ |
| Nexmon Monitor | ✅ |
| Nexmon Injection | ✅ |
| 와이파이 5Ghz | ✅ |

> 포팅 가이드에서 찾을 수 있는 모든 설정이 적용되었어요.

## 지원 버전

| ROM  | 상태 |
| :--------------- | -----:|
| [LineageOS 21 (A14)](https://github.com/V0lk3n/nethunter_kernel_samsung_exynos9820/tree/nethunter-lineage-21) | 구 버전 |
| [LineageOS 22.1 (A15)](https://github.com/V0lk3n/nethunter_kernel_samsung_exynos9820/tree/nethunter-lineage-22.1) | 구 버전 |
| <a href="https://github.com/V0lk3n/nethunter_kernel_samsung_exynos9820/tree/nethunter-lineage-22.2">LineageOS 22.2 (A15)</a> | 구 버전 |
| <a href="https://github.com/V0lk3n/nethunter_kernel_samsung_exynos9820/tree/nethunter-lineage-23.0">LineageOS 23.0 (A16)</a> | 새 버전 |

> 이 가이드는 LineageOS 22.2를 사용하지만 다른 버전과 동일한 설치 과정을 거쳐요

# 설치

설치를 시작해 봅시다. 다음 단계를 거쳐야 해요:
- 순정 롬 플래시
- OEM 잠금 해제
- LineageOS와 리커버리 플래시
- 기기 루팅
- Magisk 모듈 설치
- 칼리 넷헌터와 커널 플래시
- 부팅 시 경고 제거용 부트로더 플래시
- 최종 조정 및 문제 해결

## 순정 롬 플래시
순정 상태로 되돌리거나 설치를 처음부터 다시 깨끗하게 하고싶으면 여기에서 맞는 펌웨어를 받으세요. (권장)

만약 최신 업데이트를 설치한 상태에서 깨끗하게 플래시 하는 것을 원치 않는다면 다음 단계로 건너뛰세요:

https://www.sammobile.com/samsung/galaxy-s10/firmware/SM-G973F/

콘텐츠를 압축 풀면 다음과 같이 끝나게 돼요 :
```bash
AP_G973FXXSGHWC1_CL25257816_QB62768582_REV01_user_low_ship_meta_OS12.tar.md5
BL_G973FXXSGHWC1_CL25257816_QB62768582_REV01_user_low_ship.tar.md5
CP_G973FXXSGHWB3_CP23788344_CL25257816_QB62585640_REV01_user_low_ship.tar.md5
CSC_OXM_G973FOXMGHWA3_CL25257816_QB61057831_REV01_user_low_ship.tar.md5 <= (데이터 삭제 / 공장 초기화 권장)
HOME_CSC_OXM_G973FOXMGHWA3_CL25257816_QB61057831_REV01_user_low_ship.tar.md5 <= (데이버 보존)
```

OS 없이 부팅을 시도하여 실패할 경우 휴대폰을 다운로드 모드로 부팅하고, 자동으로 다운로드 모드에 진입하기 까지 기다리세요.

순정 롬을 플래시하세요, 예를 들어 리눅스에서는 odin4를 사용할 수 있어요 : https://github.com/Adrilaw/OdinV4


## OEM 잠금 해제

OEM 잠금을 해제하려면 먼저 개발자 모드를 활성화해야 해요.

"설정 > 휴대전화 정보 > 소프트웨어 정보"를 열고, 개발자 모드가 활성화될 때까지 "빌드 번호"를 연속으로 탭하세요.

개발자 모드가 "설정"에 나타날 거예요.

개발자 모드에서 OEM 잠금 해제 옵션을 찾아 활성화하세요. fastboot로 들어가서 "볼륨+"를 길게 누른 다음 "볼륨+"를 눌러 OEM 잠금 해제를 승인하고 휴대폰을 초기화하세요.

> OEM 잠금 해제 옵션이 보이지 않으면, 기기의 날짜/시간을 수정하는 등의 해결책을 시도해 볼 수 있어요. 구글에서 이에 대해 검색해 보세요.

## USB 디버깅

기기를 부팅하고 초기 설정을 진행한 뒤, 개발자 모드를 다시 활성화하고 와이파이를 연결하세요.

"설정 > 휴대전화 정보 > 소프트웨어 정보"를 열고, 개발자 모드가 활성화될 때까지 "빌드 번호"를 연속으로 탭하세요.

개발자 모드에서 OEM 잠금 해제 항목 아래에 "부트로더 잠금 해제됨"이라고 표시되는지 확인하고 USB 디버깅을 활성화하세요.

## ROM 플래시

LineageOS 빌드, vbmeta, 리커버리, MindTheGapps를 다운로드하세요.

LineageOS 22.2 : [다운로드](https://github.com/V0lk3n/nethunter_kernel_samsung_exynos9820/releases/download/nethunter-22.2/lineage-22.2-20250627-nightly-beyond1lte-signed.zip)

리커버리 : [다운로드](https://github.com/V0lk3n/nethunter_kernel_samsung_exynos9820/releases/download/nethunter-22.2/recovery.img)

vbmeta : [다운로드](https://github.com/V0lk3n/nethunter_kernel_samsung_exynos9820/releases/download/nethunter-22.2/vbmeta.img)

MindTheGapps : [다운로드](https://github.com/V0lk3n/nethunter_kernel_samsung_exynos9820/releases/download/nethunter-22.2/MindTheGapps-15.0.0-arm64-20250214_082511.zip)

### 리커버리 플래시

USB 케이블을 PC에 연결한 상태에서 "볼륨 다운 키와 빅스비 키"를 동시에 눌러 기기를 다운로드 모드로 부팅하세요. Heimdall을 사용하여 리커버리를 플래시하세요. 이 부분은 [LineageOS 설치 가이드](https://wiki.lineageos.org/devices/beyond1lte/install/#preparing-for-installation)를 따라할 수 있어요.

```bash
 heimdall flash --RECOVERY recovery.img --VBMETA vbmeta.img --no-reboot
```

### LineageOS ROM 플래시

리커버리가 플래시되면, "볼륨- + 전원 키"를 7초간 눌러 기기를 재시작하고, 바로 "볼륨+ + 빅스비 + 전원 키"를 눌러 리커버리로 부팅하면 초기화되지 않아요.

리커버리로 부팅되면, "Factory reset"으로 이동하여 모든 것을 포맷하세요.

뒤로 가서 "Apply update > Apply from ADB"로 이동하고 adb sideload를 사용하여 LineageOS 빌드를 플래시하세요.

```bash
adb -d sideload lineage-22.2-20250627-nightly-beyond1lte-signed.zip
```

이제 MindTheGapps를 플래시하세요. "Apply update > Apply from ADB"를 선택하세요.

```bash
adb -d sideload MindTheGapps-15.0.0-arm64-20250214_082511.zip
```

휴대폰에 "Signature verification failed Install anyway?" 경고가 나타나면 "Yes"를 누르고, MindTheGapps 플래시가 완료될 때까지 기다리세요.

완료되면 "Reboot System Now"를 누르고 초기 설정을 다시한 뒤, 와이파이에 연결하세요 (루팅 단계에 필요해요).

개발자 모드를 다시 활성화하세요.

"설정 > 휴대전화 정보 > 소프트웨어 정보"를 열고, 개발자 모드가 활성화될 때까지 "빌드 번호"를 연속으로 탭하세요.

개발자 모드에서 USB 디버깅을 활성화하세요.

> 더 나은 사용 경험을 위해 고급 재부팅을 활성화하는 것을 권장해요. "시스템 > 버튼 > 전원 메뉴 > 고급 재부팅". 필요할 때 다운로드/리커버리 모드로 부팅하는 좋은 방법이에요.

## 루팅

Magisk 28.1을 다운로드하세요.

> 이 가이드 작성 시점에서 최신 Magisk는 29이지만 넷헌터에서 많은 문제가 있어요. 그래서 28.1을 사용해야 해요.

Magisk 28.1 : [다운로드](https://github.com/V0lk3n/nethunter_kernel_samsung_exynos9820/releases/download/nethunter-22.2/Magisk-v28.1.zip)

리커버리로 재부팅하고 "Apply update > Apply from ADB"로 이동하여 Magisk를 플래시하세요.

```bash
adb -d sideload Magisk-v28.1.zip
```

휴대폰에 "Signature verification failed Install anyway?" 경고가 나타나면 "Yes"를 누르고, Magisk 플래시가 완료될 때까지 기다리세요.

플래시가 완료되면 시스템으로 재부팅하고 Magisk 앱을 여세요.

설치를 완료하라는 메시지가 나타나면 yes를 선택하고 방법으로 "Direct Installation"을 선택하세요.

완료되면 재부팅하세요.


## 넷헌터

제가 선호하는 방법은 설치 프로그램을 직접 빌드하는 거예요. 하지만 원한다면 [다운로드](https://kali.download/nethunter-images/kali-2025.3/kali-nethunter-2025.3-beyond1lte-los-fifteen-full.zip)할 수도 있어요.

자신만의 설치 프로그램을 만들고 싶지 않다면 다음 단계로 건너뛰세요.

먼저 소스에서 빌드해봅시다.

```bash
# kali-nethunter-installer 클론 및 설정
$ git clone https://gitlab.com/kalilinux/nethunter/build-scripts/kali-nethunter-installer.git
$ cd kali-nethunter-installer
$ ./bootstrap.sh
[?] Would you like to grab the full history of kernels? (y/N):
[?] Would you like to use SSH authentication (faster, but requires a GitLab account with SSH keys)? (y/N): N
[i] Running command: git clone --depth 1 https://gitlab.com/kalilinux/nethunter/build-scripts/kali-nethunter-kernels.git kernels
Cloning into 'kernels'...

# 전체 설치 프로그램 빌드
## LOS 21
$ ./build.py -k beyond1lte-los -14 -fs full
## LOS 22.2
$ ./build.py -k beyond1lte-los -15 -fs full
## LOS 23.0
$ ./build.py -k beyond1lte-los -16 -fs full
```

설치 프로그램을 기기에 전송하세요.

```bash
adb push nethunter-20250629_171321-beyond1lte-los-fifteen-kalifs_full.zip /sdcard/
```

Magisk를 열고 "Modules > Install from Storage"로 이동하여 넷헌터 설치 프로그램을 선택하고 설치하세요.

넷헌터 설치가 완료될 때까지 기다리고, 메시지가 나타나면 재부팅하세요.

## Magisk 모듈 (선택)

Magisk Overlayfs 모듈을 다운로드하세요.

Magisk Overlayfs : [다운로드](https://github.com/V0lk3n/nethunter_kernel_samsung_exynos9820/releases/download/nethunter-22.2/magisk-overlayfs-release.zip)

PlayIntegrityFix 모듈을 다운로드하세요.

PlayIntegrityFix : [다운로드](https://github.com/V0lk3n/nethunter_kernel_samsung_exynos9820/releases/download/nethunter-22.2/PlayIntegrityFix_v3.3-inject-manual.zip)

패키지를 안드로이드 기기에 전송하세요.

```bash
adb push magisk-overlayfs-release.zip /sdcard/
adb push PlayIntegrityFix_v3.3-inject-manual.zip /sdcard/
```

Magisk를 열고 "Modules > Install from storage"로 이동하여 Magisk Overlayfs 모듈을 선택하세요. "Ok"를 눌러 설치하고 설치 완료 후 재부팅하세요.

Magisk를 다시 열고, 설정에서 "Zygisk"를 활성화하세요.

"Modules > Install from storage"로 이동하여 PlayIntegrityFix 모듈을 선택하세요. "Ok"를 눌러 설치하고 설치 완료 후 재부팅하세요.

## Nexmon

### Nexmon 설정

<a href="https://gitlab.com/yesimxev">yesimxev</a>의 Nexmon Magisk 모듈을 다운로드하세요.

Nexmon S10 : [다운로드](https://github.com/V0lk3n/nethunter_kernel_samsung_exynos9820/releases/download/nethunter-22.2/nexmon-s10.zip)

모듈을 기기에 전송하세요.

```bash
adb push nexmon-s10.zip /sdcard/
```

Magisk를 열고 "Modules > Install from Storage"로 이동하여 Nexmon S10 모듈을 선택하세요.

설치가 완료될 때까지 기다리고 휴대폰을 재부팅하세요.

### Nexmon 사용법

안드로이드 터미널에서 모니터 모드 시작하세요.

```bash
$ svc wifi disable
$ ifconfig wlan0 up
$ nexutil -s0x613 -i -v2
```

모니터 모드 중지

```bash
$ nexutil -m0
$ svc wifi enable
```

칼리 터미널에서 airodump를 실행하세요 (새 터미널 창마다 export가 필요해요)

```bash
$ export LD_PRELOAD=/lib/kalilibnexmon.so
$ airodump-ng wlan0
```

kalilibnexmon.so : <a href="https://gitlab.com/kalilinux/nethunter/build-scripts/kali-nethunter-kernels/-/raw/main/ten/angler-los/system/lib64/kalilibnexmon.so">다운로드</a>

chroot 환경에 /lib/kalilibnexmon.so로 복사하세요.

### Hijacker 설정

Hijacker 앱을 열고 다음 설정을 구성하세요.

| 설정  | 값 |
| :--------------- | -----:|
| 접두사 | LD_PRELOAD=/data/user/0/com.hijacker/files/lib/libnexmon.so |
| 모니터 모드 활성화 | if [ \`dumpsys wifi \| grep "Wi-Fi is" \| cut -d" " -f3\` == "enabled" ]; then svc wifi disable; sleep 2; ifconfig wlan0 up; fi; nexutil -s0x613 -i -v2 |
| 모니터 모드 비활성화 | nexutil -m0; svc wifi enable |
| Airodump 시작 시 모니터 모드 시작 | ✅ |
| 밴드 | 둘 다 |

만약 와이파이가 5Ghz에 연결되어 있었다면, 시작 시 해당 채널만 표시될 수 있어요. 중지 + 시작 아이콘을 눌러 다시 스캔하면 모든 2.4Ghz 채널을 확인할 수 있어요.

5Ghz가 지원되지만, 안드로이드 단말기에서 채널을 수동으로 변경하려면 채널 36의 경우 `nexutil -k36/80`, 채널 40의 경우 `nexutil -k40/80` 등을 사용해야 해요. 이 기능은 Hijacker 앱에 추가될 예정이에요.

# 보너스: 스플래시 스크린 플래시

부팅 경고가 성가실 수 있어요. 이런 경고를 없애기 위해 사용자 정의 스플래시 스크린을 플래시하고 싶을 거예요.

[XDA 스레드](https://xdaforums.com/t/g97xf-soldier9312s-splash-screen-changer-1-0-13-05-2019.3929748/)

[다운로드](https://androidfilehost.com/?w=files&flid=293633)

원하는 스플래시 스크린을 선택하여 다운로드하세요.

리커버리로 부팅하고 "Apply update > Apply from ADB"로 이동하여 adb sideload를 사용해 스플래시 스크린을 플래시하세요.

```bash
adb -d sideload G97X_Splash_Screen_Changer_by_SoLdieR9312_splash.zip
```

플래싱이 완료될 때까지 기다리면 휴대폰이 자동으로 재시작돼요.

# 크레딧

<a href="https://linktr.ee/v0lk3n">V0lk3n</a>에 의해 커널 및 문서가 관리됨

<a href="https://gitlab.com/yesimxev">yesimxev</a> Nexmon 모듈 제작

도와주신 분들 :
- 갤럭시 S10에 도움과 지원을 준 **Arti**
- 최신 RTL 드라이버 제공 및 도움을 준 <a href="https://github.com/akabul0us">Akabulous</a>
- <a href="https://github.com/seemoo-lab/nexmon">Nexmon</a>
- Nexmon의 <a href="https://x.com/MarkusTieger">MarkusTieger</a>
