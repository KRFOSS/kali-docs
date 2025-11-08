---
title: USB 쓰기 검증하기
description:
icon:
weight: 75
author: ["serval123",]
번역: ["xenix4845"]
---

칼리 ISO를 USB에 쓴 후에는 다음을 확인하는 것이 좋아요:

1. 파일이 USB에 올바르게 복사되었는지

2. USB가 부팅 가능한지

3. ISO가 손상되지 않았는지 확인하기

## USB 내용 확인하기

### 리눅스/MacOS에서

```console
kali@kali:~$ lsblk
```

위 명령을 실행하면 boot/efi, live와 같은 파티션이 보여야 해요.

### 윈도우에서

디스크 관리자를 사용하여 USB에 생성된 파티션을 볼 수 있어요.
**주의: 윈도우는 USB에 사용된 파일 시스템을 인식하지 못해 손상되었다고 표시하고 포맷하라는 메시지를 표시할 거예요. 절대 포맷하지 마세요.**

## 체크섬 확인하기

### 리눅스/MacOS에서

```console
kali@kali:~$ shasum -a 256 kali-linux-2025.3-live-amd64.iso 
```
ISO 파일 이름을 다운로드한 것으로 바꿔주세요. 체크섬을 ISO를 다운로드한 페이지에서 제공하는 값과 비교해 확인하세요.

### 윈도우에서

certutil을 사용할 수 있다면 다음을 실행하세요:

```console
certutil -hashfile kali-linux-2025.3-live-amd64.iso sha256
```

다운로드를 확인하세요. 특정 윈도우 버전에서는 SHA256 체크섬을 계산하는 기본 기능이 없을 수 있어요. certutil이 설치되어 있지 않다면 Microsoft File Checksum Integrity Verifier 또는 Hashtab과 같은 유틸리티를 사용하여 다운로드를 확인할 수 있어요.

마지막으로 USB에서 부팅을 시도해 보세요.

## 문제 해결

### USB가 부팅되지 않음

1. Balena Etcher를 사용하여 ISO를 플래시했는지 확인하세요.
2. BIOS에서 보안 부팅이 활성화되어 있다면 비활성화하세요.

### 체크섬 불일치

이는 ISO 파일이 다운로드 중 손상되었음을 의미해요. 칼리 ISO를 다시 다운로드한 후 USB에 다시 작성하세요.