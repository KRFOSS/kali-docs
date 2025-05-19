---
title: 맞춤형 칼리 ISO 빌드하기
description:
icon:
weight: 52
author: ["g0tmi1k",]
번역: ["xenix4845"]
---

칼리 리눅스의 가장 강력한 기능 중 하나는 맞춤형 도구, 데스크톱 매니저, 서비스를 포함한 나만의 배포판을 만들 수 있다는 거예요. 이 워크숍에서는 live-build 유틸리티를 사용해 거의 모든 측면을 맞춤화하고 칼리에서 제공하는 다양한 [메타패키지](/docs/general-use/metapackages/)를 효율적으로 활용하여 나만의 칼리 리눅스 ISO를 만드는 방법을 알려드릴게요.

## Live Build의 놀라운 기능

**0x00 - 저장소 업데이트, 필수 패키지 설치,** 그리고 칼리 Git 저장소에서 최신 버전의 `live-build-config`를 체크아웃하는 것부터 시작하세요:

```console
kali@kali:~$ sudo apt update
kali@kali:~$ sudo apt install -y git live-build cdebootstrap devscripts
kali@kali:~$ git clone https://gitlab.com/kalilinux/build-scripts/live-build-config.git
kali@kali:~$ cd live-build-config/
```

**0x01 - 기본 칼리 패키지 목록을 원하는 패키지만 포함하도록 덮어쓰세요**. 영상에서는 목록을 편집하고 몇 가지 패키지 이름만 변경했어요:

```console
kali@kali:~$ cat <<EOF > kali-config/variant-default/package-lists/kali.list.chroot
kali-root-login
kali-defaults
kali-menu
kali-debtags
kali-archive-keyring
debian-installer-launcher
alsa-tools
locales-all
dconf-tools
openssh-server
EOF
```

**0x02 - 맞춤형 syslinux 부팅 항목 추가하기** 여기에는 맞춤 preseed 파일(자동 설치 구성 파일)을 위한 부팅 매개변수가 포함돼요:

```console
kali@kali:~$ cat <<EOF > kali-config/common/includes.binary/isolinux/install.cfg
label install
    menu label ^Install Automated
    linux /install/vmlinuz
    initrd /install/initrd.gz
    append vga=788 -- quiet file=/cdrom/install/preseed.cfg locale=en_US keymap=us hostname=kali domain=local.lan
EOF
```

**0x03 - ISO 빌드 맞춤화하기**. 이 예제에서는 SSH 서비스가 기본적으로 시작되도록 설정할게요. 이를 위해 "hooks" 디렉토리에 chroot 훅 스크립트를 사용할 수 있어요:

```console
kali@kali:~$ echo 'systemctl enable ssh' >>  kali-config/common/hooks/01-start-ssh.chroot
kali@kali:~$ chmod +x kali-config/common/hooks/01-start-ssh.chroot
```

**0x04 - 다음으로, 배경화면을 다운로드하고** 오버레이해요. chroot 오버레이 파일이 _includes.chroot_ 디렉토리에 배치되는 방식을 확인하세요:

```console
kali@kali:~$ mkdir -pv kali-config/common/includes.chroot/usr/share/wallpapers/kali/contents/images/
kali@kali:~$ wget https://www.kali.org/dojo/blackhat-2015/wp-blue.png
kali@kali:~$ mv wp-blue.png kali-config/common/includes.chroot/usr/share/wallpapers/kali/contents/images
```

**0x05 - preseed 파일 추가하기** 이 파일은 입력 없이(무인 설치) 기본 칼리 설치를 진행해요. 미리 만들어진 preseed 구성을 포함시키고 필요에 따라 수정할 수 있어요:

```console
kali@kali:~$ mkdir -pv kali-config/common/debian-installer/
kali@kali:~$ wget https://gitlab.com/kalilinux/recipes/kali-preseed-examples/-/raw/main/kali-linux-full-unattended.preseed -O kali-config/common/debian-installer/preseed.cfg
```

**0x06 - 최종 빌드에 Nessus 데비안 패키지를 포함시켜 봅시다** _packages_ 디렉토리에 넣으면 돼요. 64비트 빌드를 사용했으므로 64비트 Nessus 데비안 패키지를 포함시킬게요. Nessus .deb 파일을 [다운로드](https://www.tenable.com/products/nessus/select-your-operating-system)하고 packages.chroot 디렉토리에 넣으세요:

```console
kali@kali:~$ mkdir -p kali-config/common/packages.chroot/
kali@kali:~$ mv Nessus-*amd64.deb kali-config/common/packages.chroot/
```

**0x07 - 이제 ISO 빌드를 진행할 수 있어요**. 이 과정은 하드웨어와 인터넷 속도에 따라 시간이 좀 걸릴 수 있어요. 완료되면 ISO가 live-build 루트 디렉토리에 생성돼요:

```console
kali@kali:~$ ./build.sh -v
```

더 많은 live-build 구현 사례는 다음을 참조하세요:

- [offsec.com/blog/kali-linux-recipes/](https://www.offsec.com/blog/kali-linux-recipes/?utm_source=kali&utm_medium=web&utm_campaign=docs)
- [gitlab.com/kalilinux/recipes/live-build-config-examples](https://gitlab.com/kalilinux/recipes/live-build-config-examples)
