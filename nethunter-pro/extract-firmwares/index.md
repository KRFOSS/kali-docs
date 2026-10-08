---
title: 메인라인 리눅스용 제조사 펌웨어 추출 및 SquashFS 압축
description:
icon:
weight:
author: ["ShubhamVis98",]
번역: ["xenix4845"]
---

## 개요

**동적 제조사 파티션**을 사용하는 기기에서는 메인라인 리눅스 배포판을 실행하기가 어려울 수 있어요. 이러한 기기에서는 펌웨어가 고정된 위치에 마운트되어 있지 않기 때문에 `droid-juicer` 같은 도구로 펌웨어를 추출할 수 없어요. 이 문서에서는 실행 중인 안드로이드 운영체제에서 제조사 펌웨어를 수동으로 추출한 뒤, `pil-squasher`를 기반으로 한 `fw-xtractor` 도구를 사용해 메인라인 리눅스에서 사용할 수 있는 형태로 변환하는 방법을 단계별로 안내해요.

### 문제점

- 동적 제조사 파티션이 있는 기기에서는 **`droid-juicer`**가 작동하지 않아요.
- 기존의 블록 장치 추출 방식으로 제조사 파티션에 접근할 수 없어요.
- 메인라인 리눅스에는 미리 추출하여 압축한 펌웨어 파일이 필요해요.
- 퀄컴(QCOM) 안드로이드 기기에서 메인라인 운영체제를 실행하려면 수동 추출이 필요해요.

---

## 사전 준비

시작하기 전에 다음 항목을 준비하세요.

1. **동적 제조사 파티션이 있는 안드로이드 기기**
2. 호스트 컴퓨터에 설치된 **ADB(Android Debug Bridge)**
3. 안드로이드 기기에서 활성화된 **USB 디버깅**
4. 안드로이드 기기에 복제한 **fw-xtractor** 저장소
5. 리눅스 명령줄에 관한 기본 지식
6. 안드로이드 기기의 **슈퍼유저(root) 권한**

### 필요한 도구 설치

```bash
# ADB 설치
# Debian
sudo apt install android-tools-adb

# macOS
brew install android-platform-tools

# ADB 또는 터미널 앱을 통해 안드로이드 기기에서 fw-xtractor 저장소를 복제하거나 ZIP 파일 다운로드
cd /data/local/tmp
git clone https://github.com/Shubhamvis98/fw-xtractor.git
# 또는
wget -O fwx.zip https://github.com/Shubhamvis98/fw-Xtractor/archive/refs/heads/main.zip
unzip fwx.zip
```

### droid-juicer 저장소 복제 또는 기기 설정 파일 다운로드

```bash
git clone https://gitlab.com/mobian1/droid-juicer.git
# 예: 포코 F1용 xiaomi,beryllium.toml
cp droid-juicer/configs/xiaomi,beryllium.toml fw-xtractor/my_config.toml
```

### fw-xtractor로 제조사 파티션의 펌웨어 추출 및 압축

```bash
cd fw-xtractor
./fw-Xtractor my_config.toml
cd output
tar -cpzf ../fw.tgz .
```

이제 생성된 `fw.tgz` 파일을 저장한 다음, 루트 파일 시스템의 `/lib/firmware/updates/` 경로에 압축을 풀고 넷헌터 프로(NHPro)로 다시 부팅하면 돼요.

이후에도 커널 로그에 일부 펌웨어가 누락되었다는 메시지가 나타날 수 있어요. 이 경우 다음 명령어로 초기 램 파일 시스템(initramfs)을 갱신하고 업데이트된 `boot.img`를 플래싱하세요.

```bash
sudo update-initramfs -u
sudo /etc/kernel/postinst.d/zz-qcom-bootimg $(linux-version list | tail -1)
reboot
```
