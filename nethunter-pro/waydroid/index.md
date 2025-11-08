---
title: 넷헌터 프로에서 Waydroid 사용하기
description:
icon:
weight:
author: ["ShubhamVis98",]
번역: ["xenix4845"]
---

### Waydroid란?

Waydroid는 리눅스 시스템에서 안드로이드 운영체제를 컨테이너로 실행할 수 있게 해주는 오픈소스 프로젝트예요. 덕분에 같은 기기에서 리눅스 앱과 안드로이드 앱을 함께 실행할 수 있죠. 이것은 리눅스 컨테이너(LXC)를 사용해 안드로이드 환경을 호스트 시스템과 분리하면서도 두 환경 간의 상호작용을 가능하게 해요.

### 설치 방법

{{< youtube BeCkoZ4EfOs >}}

**필요한 패키지 설치하기**
```
sudo apt install curl ca-certificates -y
```

**공식 저장소 추가하기**
```
curl -sSL https://repo.waydro.id -o wd.sh
sudo bash wd.sh bookworm
```

**apt로 waydroid 설치하기**

`apt` 명령을 실행할 때 패키지 의존성 오류가 나타난다면, 위 단계에서 `trixie`로 스위트를 변경해보세요.

```
sudo apt install waydroid
sudo waydroid init
reboot
```

재부팅 후에는 Phosh 앱 서랍에서 waydroid를 시작할 수 있어요.
