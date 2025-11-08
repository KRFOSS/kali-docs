---
title: 넷헌터 프로 - SDM845(OnePlus6/6T, POCO F1)에서 OTG 켜기
description:
icon:
weight:
author: ["ShubhamVis98",]
번역: ["xenix4845"]
---

{{% notice info %}}
OTG(On-The-Go)는 스마트폰이나 태블릿이 호스트 역할을 하여 USB 장치(키보드, 마우스, 저장장치 등)를 직접 연결할 수 있게 해주는 기능이예요. 넷헌터에서는 이 기능이 해킹 도구를 연결하는 데 중요해요.
{{% /notice %}}

### 영상 가이드

{{< youtube bjhgKxhgmIY >}}

### 사전 요구 사항 설치하기

```
sudo apt install -y abootimg device-tree-compiler
```
### 주의사항:

POCO F1과 같은 일부 기기는 작동하기 위해 외부 전원이 공급되는 OTG 케이블이 필요합니다. 따라서 OTG 케이블에 외부 전원을 연결했는지 확인하세요.

### dtb(디바이스 트리 블롭) 수정 단계

**아래는 POCO F1 EBBG 모델용 단계예요. 어떤 모델인지 확인하려면 `cat /proc/cmdline` 명령어를 실행해 보세요.**
**A/B 파티션을 사용하는 기기라면 `qbootctl` 도구로 현재 활성화된 파티션을 확인할 수 있어요.**

```
# 임시 작업 디렉토리 생성하기
TMPDIR=$(mktemp -d) && cd $TMPDIR

# 기기의 dtb 파일 복사하기 (예: POCO F1 EBBG 모델의 경우 beryllium-ebbg)
cp /usr/lib/linux-image-$(linux-version list | tail -1)/qcom/sdm845-xiaomi-beryllium-ebbg.dtb orig.dtb

# dtb 파일을 dts 파일로 변환하고 dr_mode 값 변경하기
dtc -o tmp.dts orig.dtb
sed -i 's/dr_mode = "peripheral"/dr_mode = "host"/' tmp.dts

# 수정한 dts 파일을 다시 dtb로 만들기
dtc -o host.dtb tmp.dts

# 커널과 수정된 dtb 파일을 하나로 합치기
cat /boot/vmlinuz-$(linux-version list | tail -1) host.dtb > kernel_dtb

# 부트 파티션에서 현재 boot.img 가져오기
dd if=/dev/disk/by-partlabel/boot of=boot.img

# boot.img 파일의 커널 업데이트하기
abootimg -u boot.img -k kernel_dtb

# 수정된 boot.img 설치하기
dd if=boot.img of=/dev/disk/by-partlabel/boot
```

모든 단계를 완료했다면 기기를 재부팅해 보세요!
