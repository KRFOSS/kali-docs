---
title: 원플러스 7에 넷헌터 설치하기
description:
icon:
weight:
author: ["re4son",]
번역: ["xenix4845"]
---

![](one-plus-7p.png)

# 초기화부터 넷헌터 실행까지 4단계:
  
1. 언브릭 도구로 최신 Android 10 플래시하기
2. TWRP와 Magisk 플래시하기
3. 데이터 파티션의 강제 암호화 비활성화하기
4. 넷헌터 설치하기
5. OnePlus 업데이트 서비스 비활성화하기
  
## 1. 최신 기본(OOS) Android 10 플래시하기
  
1.01.	윈도우 컴퓨터에 [guacamoleb_14_P.32_210127.zip](https://kali.download/nethunter-images/devices/guacamole/guacamoleb_14_P.32_210127.zip) 다운로드 및 압축 해제
1.02	관리자 명령 프롬프트를 열고 "bcdedit /set testsigning on" 입력 후 재부팅  
1.03	MsmDownloadTool V4.0.exe 실행  
1.04	원플러스 7 전원 끄고, 볼륨 + 와 볼륨 - 동시에 누르고, 5초 세고 노트북에 연결  
1.05	기기가 감지되면(삑 소리) 시작 누르기  
1.06	재부팅하고 기기 설정  
1.07	개발자 옵션에서 OEM 잠금 해제 및 USB 디버깅 활성화  
1.08	sudo apt install adb  
1.09	adb kill-server && ./adb start-server  
1.10	adb reboot bootloader  
1.11	sudo fastboot oem unlock  
1.12	재부팅하고 폰 설정  
  
## 2. TWRP와 Magisk 플래시하기
  
2.01	원플러스7에 복사  
2.02	원플러스7에 [Magisk-v19.3.zip](https://kali.download/nethunter-images/devices/guacamole/Magisk-v19.3.zip) 다운로드
2.03	PC에 [twrp-3.3.1-1-guacamoleb.img](https://kali.download/nethunter-images/devices/guacamole/twrp-3.3.1-1-guacamoleb.img) 다운로드
2.04	안드로이드 플랫폼 도구 설치  
2.05	휴대폰의 개발자 옵션에서 USB 디버깅 활성화  
2.06	adb kill-server && ./adb start-server  
2.07	adb reboot bootloader  
2.08	sudo fastboot boot twrp-3.3.1-1-guacamoleb.img  
2.09	기기가 자동으로 TWRP로 재부팅  
2.10	TWRP에서 twrp-installer-3.3.1-1-guacamoleb.zip 및 Magisk-v19.3.zip 설치, 재부팅  
2.11	재부팅  
  
## 3. 데이터 파티션의 강제 암호화 비활성화하기
  
3.01	리커버리 모드로 재부팅  
3.02	/data 포맷  
3.03	리커버리 모드로 재부팅  
3.04	magisk 설치  
3.05	Disable_Dm-Verity_ForceEncrypt_11.02.2020.zip 설치  
3.06	시스템으로 재부팅  
3.07	폰 설정하되 지문이나 기타 보안 설정 건너뛰기. 나중에 설정 (폰이 다시 암호화되는지 확실하지 않음)  
  
## 4. 넷헌터 설치하기
  
4.01	[다운로드 페이지](/get-kali/#kali-mobile)에서 이미지 다운로드  
4.02    현재 설치 프로그램에는 커널을 별도로 설치해야 하는 버그가 있어, [여기에서 다운로드](https://kali.download/nethunter-images/devices/guacamole/kernel-nethunter-2021.3-oneplus7-oos-ten.zip)  
4.03	리커버리로 재부팅  
4.04	넷헌터 zip 설치  
4.06    magisk 다시 설치  
4.07	disable-force-encrypter 다시 설치  
4.08    재부팅  
4.09	넷헌터 앱 실행, 초기 설정이 완료될 때까지 기다린 다음 재부팅  
4.10	스토어에서 넷헌터 앱 업데이트  
  
## 5. 원플러스 업데이트 서비스 비활성화하기
  
5.01	Android 터미널을 루트로 열기  
5.02	su -c pm disable com.oneplus.opbackup  
5.03	이제 "시스템 업데이트" 서비스가 비활성화됨  
  
### 원플러스 7에서 칼리 넷헌터 즐기기  
  
  
이슈와 풀 리퀘스트를 제출하여 개발에 도움을 주세요. 많은 감사를 드립니다.
