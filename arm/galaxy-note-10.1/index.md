---
title: 삼성 갤럭시 노트 10.1
description:
icon:
archived: "true"
weight:
author: ["steev",]
번역: ["xenix4845"]
---

{{% notice info %}}
칼리 리눅스는 더 이상 사전 빌드된 이미지나 자체 이미지를 생성하기 위한 빌드 스크립트를 제공하지 않습니다.
이 하드웨어는 더 이상 지원되지 않습니다.
이 페이지는 역사적 가치를 위해 남겨두었습니다.
{{% /notice %}}

- - -

삼성 갤럭시 노트 10.1은 삼성이 설계, 개발 및 판매한 10.1인치 태블릿 컴퓨터입니다. 이 태블릿은 1.4GHz 쿼드 코어 엑시노스 프로세서와 2GB RAM을 탑재하고 있습니다. 터치스크린과 무선 카드는 칼리에서 놀라울 정도로 잘 작동하지만, 블루투스와 오디오는 아직 이 이미지에서 기능하지 않습니다.

기본적으로 칼리 리눅스 삼성 갤럭시 노트 10.1 이미지는 다른 칼리 플랫폼에서 흔히 볼 수 있는 [**kali-linux-default** 메타패키지](/docs/general-use/metapackages/)를 **포함하지 않습니다**. 추가 도구를 설치하고자 하신다면 [메타패키지 페이지](/docs/general-use/metapackages/)를 참조해 주시기 바랍니다.

## 갤럭시 노트 10.1용 칼리 - 사용자 안내서

[칼리 리눅스 이미지 다운로드 및 검증](/docs/introduction/download-official-kali-linux-images/)이나 [해당 이미지로 부팅 가능한 장치 생성](/docs/usb/live-usb-install-with-windows/)에 익숙하지 않으시다면, 이러한 주제에 대해 더 자세히 설명한 전문 문서를 참조하시길 강력히 권장합니다.

갤럭시 노트 10.1에 표준 칼리 리눅스의 사전 빌드된 이미지를 설치하려면 다음 지침을 따르세요:

1. 내부 풀사이즈 SD 카드에 **최소 7GB의 여유 공간**이 필요합니다.
2. 아직 루팅하지 않았다면 삼성 갤럭시 노트 10.1을 루팅하세요.
3. [다운로드](/get-kali/) 영역에서 `Kali Galaxy Note 10.1` 이미지를 다운로드하고 검증하세요. 이미지 검증 과정은 [칼리 리눅스 다운로드](/docs/introduction/download-official-kali-linux-images/)에 더 자세히 설명되어 있습니다.
4. 다운로드한 칼리 이미지 이름을 `linux.img`로 변경하고 `/storage/sdcard0`에 복사하세요.
5. 여기에서 `recovery.img` 파일을 다운로드하여 `/storage/sdcard0`에 복사하세요.
6. 갤럭시 노트 10.1에서 루트 권한을 얻고, `/storage/sdcard0`로 이동한 후 복구 파티션을 백업하세요:

```console
$ dd if=/dev/block/mmcblk0p6 of=recovery.img_orig conv=fsync
```

7. 다운로드한 `recovery.img` 이미지를 **[dd](https://manpages.debian.org/testing/coreutils/dd.1.en.html)** 명령으로 복구 파티션에 기록하세요:

{{% notice info %}}
**주의!** 이 과정은 복구 파티션을 덮어쓰게 됩니다. 무엇을 하고 있는지 확실히 알고 진행하세요. 그렇지 않으면 기기가 벽돌(brick)이 될 수 있습니다.
{{% /notice %}}

```console
$ dd if=recovery.img of=/dev/block/mmcblk0p6 conv=fsync
```

8. 갤럭시 노트 10.1을 복구 모드로 재부팅하세요. **전원을 끈 다음**, **전원 버튼**과 **볼륨 업 버튼**을 동시에 길게 누르면 됩니다. "Samsung Galaxy Note 10.1" 텍스트가 나타나면, **전원 버튼은 놓되 볼륨 업 버튼은 계속 누르고 있으세요**. 이렇게 하면 칼리가 부팅되고 GNOME에 자동 로그인됩니다. [루트 비밀번호](/docs/introduction/kali-linux-default-passwords/)는 `changeme`입니다.
9. 화면 키보드를 열려면 **응용 프로그램** -> **접근성** -> **Florence 가상 키보드**로 이동하세요.
10. 무선 네트워크는 작동하지만 약간의 설정 없이는 네트워크 스캔이 건너뛰는 경우가 있습니다. **GNOME 네트워크 매니저에 무선 네트워크가 표시되지 않으면**, 간단히 "숨겨진" 네트워크로 무선 네트워크를 추가하면 평소와 같이 연결됩니다.
11. [Linux Deploy](https://play.google.com/store/apps/details?id=ru.meefik.linuxdeploy&hl=en)와 같은 안드로이드 앱을 사용하여 갤럭시 노트 내에서 쉽게 이미지를 수정, 디버그 및 탐색할 수 있습니다.
