---
title: 패키징을 위한 시스템 설정하기
description:
icon:
weight: 10
author: ["gamb1t", "Funeoz",]
번역: ["xenix4845"]
---

## VM이냐 설치냐?

이 연습에서는 VM에서만 가능한 특정 사항들을 설명할 거예요. 전체 칼리 시스템을 설치하거나(이미 있다면 사용하거나) VM을 사용할지는 여러분의 선택이지만, 설치인 경우 입력하는 명령어를 염두에 두세요.

## VM 설정하기

개발 환경을 설정하는 것이 중요해요. 가장 쉬운 방법은 [최신 칼리 이미지](https://cdimage.kali.org/kali-weekly/)로 VM을 설정하고 큰 파일 시스템을 제공하는 것이에요. 80GB+는 한 번에 몇 개의 패키지에는 좋지만, [모든 패키징 저장소를 다운로드하기 위해 `mr`을 사용](https://gitlab.com/kalilinux/tools/packaging)한다면 200GB+ 권장해요. 아마도 모든 패키지를 다운로드할 필요는 없을 거예요.

## 패키지 설치하기

나중에 패키징에 사용할 도구들을 설치할 거예요.

```console
kali@kali:~$ sudo apt update
[...]
kali@kali:~$
kali@kali:~$ sudo apt install -y sbuild mmdebstrap uidmap apt-file gitk git-lfs myrepos debhelper devscripts dput lintian quilt
[...]
kali@kali:~$
```

## 사용자 계정과 키

패키징은 sudo 권한이 있는 비루트 사용자에서 수행되어야 해요. _기본 칼리 사용자가 이에 적합해요_.

(`su`를 사용하는 대신) 계정에서 로그아웃하고 새 사용자로 전환**해야** 해요. 다음 설정의 일부(설정된 변수 같은)가 해당 계정에 있어야 하므로 이렇게 하는 거고, `su`는 작동하지 않을 거예요.

다음으로 SSH와 GPG 키를 생성해야 해요. 이것들은 GitLab에서 파일에 쉽게 접근할 수 있게 해주고 작업이 우리 것임을 보장하므로 패키징에 중요해요. 이 단계가 항상 필요한 것은 아니지만, 특정 경우에 도움이 돼요. GPG 키를 설정해야 하는지 알 수 있지만, 패키징 과정을 더 빠르게 만들어주므로 SSH 키 설정을 권장해요:

```console
kali@kali:~$ ssh-keygen -t rsa
[...]
kali@kali:~$
kali@kali:~$ gpg --gen-key
gpg (GnuPG) 1.4.12; Copyright (C) 2012 Free Software Foundation, Inc.
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.

gpg: directory `/home/packaging/.gnupg' created
gpg: new configuration file `/home/packaging/.gnupg/gpg.conf' created
gpg: WARNING: options in `/home/packaging/.gnupg/gpg.conf' are not yet active during this run
gpg: keyring `/home/packaging/.gnupg/secring.gpg' created
gpg: keyring `/home/packaging/.gnupg/pubring.gpg' created
Please select what kind of key you want:
   (1) RSA and RSA (default)
   (2) DSA and Elgamal
   (3) DSA (sign only)
   (4) RSA (sign only)
 Your selection? 1
RSA keys may be between 1024 and 4096 bits long.
What keysize do you want? (2048)
Requested keysize is 2048 bits
Please specify how long the key should be valid.
         0 = key does not expire
      <n>  = key expires in n days
      <n>w = key expires in n weeks
      <n>m = key expires in n months
      <n>y = key expires in n years
Key is valid for? (0)
Key does not expire at all
Is this correct? (y/N) y

You need a user ID to identify your key; the software constructs the user ID
from the Real Name, Comment and Email Address in this form:
    "Heinrich Heine (Der Dichter) <heinrichh@duesseldorf.de>"

Real name: First Last
Email address: email@domain.com
Comment:
You selected this USER-ID:
     "First Last <email@domain.com>"

Change (N)ame, (C)omment, (E)mail or (O)kay/(Q)uit? O
You need a Passphrase to protect your secret key.

We need to generate a lot of random bytes. It is a good idea to perform
some other action (type on the keyboard, move the mouse, utilize the
disks) during the prime generation; this gives the random number
generator a better chance to gain enough entropy.

Not enough random bytes available. Please do some other work to give
the OS a chance to collect more entropy! (Need 284 more bytes)

gpg: /home/packaging/.gnupg/trustdb.gpg: trustdb created
gpg: key A123BC4D marked as ultimately trusted
public and secret key created and signed.

gpg: checking the trustdb
gpg: 3 marginal(s) needed, 1 complete(s) needed, PGP trust model
gpg: depth: 0  valid:   1  signed:   0  trust: 0-, 0q, 0n, 0m, 0f, 1u
pub   2048R/1234AB5C 2000-00-00
      Key fingerprint = 12AB 34C4 67DE F890 12G3  H45I 6789 J90K L123 MN4O
uid                  First Last <email@domain.com>
sub   2048R/12345A6B 2000-00-00
kali@kali:~$
```

**"First Last <email@domain.com>"을 여러분의 이름과 이메일로 변경하는 것을 기억해주세요**.

다음 단계는 GitLab 계정에 SSH 키를 추가하는 것이에요. 이는 [키 섹션](https://gitlab.com/-/profile/keys)에서 할 수 있어요. 아래 명령어를 실행하여 키를 복사-붙여넣기 버퍼에 넣고 GitLab의 웹 페이지에 붙여넣으세요:

```console
kali@kali:~$ sudo apt install -y xclip
[...]
kali@kali:~$
kali@kali:~$ cat ~/.ssh/id_rsa.pub | xclip
kali@kali:~$
```

## 파일 설정하기

먼저, 환경에서 `DEBFULLNAME`과 `DEBMAIL`을 설정해야 해요:

```console
kali@kali:~$ grep -q DEBFULLNAME ~/.profile \
  || echo "export DEBFULLNAME='First Last'" >> ~/.aliases
kali@kali:~$
kali@kali:~$ grep -q DEBEMAIL ~/.profile \
  || echo export DEBEMAIL=email@domain.com >> ~/.aliases
kali@kali:~$
```

**`email@domain.com`을 본인의 이메일로 변경하세요, 그리고 해당 설정이 완료된 경우 GPG 키와 동일한 키인지 확인하세요.**

이제 git-buildpackage/[`gbp buildpackage`](https://manpages.debian.org/testing/git-buildpackage/gbp-buildpackage.1.en.html)를 설정해야 해요:

```console
kali@kali:~$ cat <<EOF > ~/.gbp.conf
[DEFAULT]
pristine-tar = True
cleaner = /bin/true

[buildpackage]
export-dir = $HOME/kali/build-area/
ignore-branch = True
ignore-new = True

sign-tags = True

[dch]
ignore-branch = True
multimaint-merge = True

[import-orig]
filter-pristine-tar = True
sign-tags = True

[pq]
patch-numbers = False
EOF
kali@kali:~$
```

우리는 기본적으로 `pristine-tar`를 활성화해요. 이 도구를 사용해서 Git 저장소에 업스트림 tarball의 복사본을 (효율적으로) 저장할 거거든요. 또한 패키지 빌드가 git 체크아웃 디렉토리 외부에서 일어나도록 `export-dir`을 설정해요.

아래에서는 `devscripts` 패키지에서 제공하는 유용한 도구들을 커스터마이징하고 있어요:

```console
kali@kali:~$ gpg -k

pub   rsa2048 2019-01-01 [SC] [expires: 2021-12-21]
      ABC123DE45678F90123G4567HIJK890LM12345N6
uid           [ultimate] First Last <email@domain.com>
sub   rsa2048 2019-01-01 [E] [expires: 2021-12-21]
kali@kali:~$
kali@kali:~$ cat <<EOF > ~/.devscripts
DEBRELEASE_DEBS_DIR=$HOME/kali/build-area/
DEBRELEASE_UPLOADER=dput

DEBCHANGE_AUTO_NMU=no
DEBCHANGE_MULTIMAINT_MERGE=yes
DEBCHANGE_PRESERVE=yes
DEBCHANGE_RELEASE_HEURISTIC=changelog

DEBSIGN_KEYID=ABC123DE45678F90123G4567HIJK890LM12345N6

DEBUILD_LINTIAN_OPTS="--color always -I"

USCAN_DESTDIR=$HOME/kali/upstream/
EOF
kali@kali:~$
kali@kali:~$ mkdir -pv $HOME/kali/{build-area,upstream}
```

**`DEBSIGN_KEYID`에 여러분 자신의 키 ID를 넣어야 해요. 이 예시에서 `gpg -k`에서 우리의 키가 `ABC123DE45678F90123G4567HIJK890LM12345N6`인 것을 볼 수 있어요**

git 구성에 다음을 추가하고 싶을 수도 있어요:

```console
kali@kali:~$ gpg -k

pub   2048R/A123BC4D 2012-12-07
uid                  First Last <email@domain.com>
sub   2048R/12345A6B 2012-12-07
kali@kali:~$
kali@kali:~$ git config --global user.name "First Last"
kali@kali:~$
kali@kali:~$ git config --global user.email email@domain.com
kali@kali:~$
kali@kali:~$ git config --global user.signingkey ABC123DE45678F90123G4567HIJK890LM12345N6
kali@kali:~$
kali@kali:~$ git config --global commit.gpgsign true
kali@kali:~$
```

**`user.name`과 `user.email`은 gpg 키 세부사항(`gpg -k`)과 일치해야 해요. 그렇지 않으면 나중에 "Secret Key Not Available" 오류가 발생할 거예요**.
**`user.signingkey`에 여러분 자신의 키 ID를 넣어야 해요. 이 예시에서 `gpg -k`에서 우리의 키가 `ABC123DE45678F90123G4567HIJK890LM12345N6`인 것을 볼 수 있어요**

`debian/changelog` 파일을 위한 전용 git 병합 드라이버도 활성화하고 싶어요:

```console
kali@kali:~$ cat <<EOF >> ~/.gitconfig
[merge "dpkg-mergechangelogs"]
         name = debian/changelog merge driver
         driver = dpkg-mergechangelogs -m %O %A %B %A
EOF
kali@kali:~$
kali@kali:~$ mkdir -pv ~/.config/git/
kali@kali:~$
kali@kali:~$ grep mergechangelogs ~/.config/git/attributes \
  || echo "debian/changelog merge=dpkg-mergechangelogs" >> ~/.config/git/attributes
kali@kali:~$
```
마지막으로, 패치 관리 도구인 `quilt`를 구성할 수 있어요:

```console
kali@kali:~$ cat << EOF > ~/.quiltrc
export QUILT_PATCHES=debian/patches
QUILT_PUSH_ARGS="--color=auto"
QUILT_DIFF_ARGS="--no-timestamps --no-index -p ab --color=auto"
QUILT_REFRESH_ARGS="--no-timestamps --no-index -p ab"
QUILT_DIFF_OPTS='-p'
EOF
kali@kali:~$
```

## sbuild

`sbuild`는 격리된 빌드 환경에서 패키지를 빌드하는 데 사용되는 도구에요.

```
cat <<'EOF' > ~/.config/sbuild/config.pl

# '아키텍처: 전체' 패키지 빌드
$build_arch_all = 1;
# 소스 패키지 빌드
$build_source = 1;
# 호스트에서 clean 타겟 실행 방지
$clean_source = 0;
# litian 실행, 정보성 태그 표시
$run_lintian = 1;
$lintian_opts = ['-I'];

# 빌드 중 네트워크 접근 허용
$enable_network = 1;

# unshare 백엔드 사용
$chroot_mode = "unshare";
# chroot tarball을 일주일 동안 보관
$unshare_mmdebstrap_auto_create = 1;
$unshare_mmdebstrap_keep_tarball = 1;
$unshare_mmdebstrap_max_age = 604800;
# /var/tmp/ 디렉토리에서 빌드 수행
$unshare_tmpdir_template = "/var/tmp/sbuild.XXXXXXXXXX";

# 칼리용 chroot 조정
push @{$unshare_mmdebstrap_extra_args}, "kali-*", [
  '--mirror=http://http.kali.org/kali',
  '--components=main contrib non-free non-free-firmware',
  '--include=kali-archive-keyring'
];
```
위 설정은 필요에 따라 약간 조정할 수 있으며, 아래에 몇 가지 팁을 제공해요.

빌드 속도를 높이려면 `$unshare_tmpdir_template = ...` 줄을 주석 처리하세요. 이 경우 sbuild는 `/tmp/` 디렉토리에서 빌드를 수행하며, 이 디렉토리는 전적으로 RAM에 존재하므로 디스크를 사용하지 않아요. 빌드 시간을 단축할 수 있지만, 한 가지 심각한 주의사항이 있어요.: **대용량 패키지의 경우 RAM을 모두 차지하여 실패할 수 있으며**, 이는 SWAP 영역의 크기를 늘려 완화할 수 있어요.

빌드가 실패할 때 빌드 환경에서 쉘을 실행하는 것이 유용할 수 있어요. `~/.config/sbuild/config.pl`에 다음 코드 조각을 추가하면 자동으로 수행돼요:

```
# 빌드 실패시 쉘 획득
$external_commands = {
  "build-failed-commands" => [ [ '%SBUILD_SHELL' ] ],
};
```

## Approx (캐싱 프록시)

sbuild로 패키지를 빌드할 때 빌드 의존성을 다운로드하는 데 많은 시간(및 대역폭)이 소요돼요. 이 단계를 가속화하기 위해 `approx` 같은 캐싱 프록시를 사용할 수 있어요:

```console
kali@kali:~$ sudo apt install -y approx
```

패키지가 설치된 후, 칼리에 사용할 원격 저장소를 정의하기 위해 구성 파일 `/etc/approx/approx.conf`의 한 줄만 편집하면 돼요. 데비안에 이미 정의된 매핑 바로 아래에 추가하여 구성 파일 `/etc/approx/approx.conf`가 다음과 같이 보이도록 하세요:

```
debian          http://ftp.debian.org/debian
debian-security	http://security.debian.org/debian-security
kali            http://kali.download/kali
```

**http://http.kali.org/kali는 캐싱 프록시의 백엔드로 사용하기에 적합하지 않으며, 일시적인 오류(해시 합치 불일치)를 유발할 수 있으므로 사용하지 마세요. kali.download 또는 주변 지역에 위치한 미러를 사용하세요.**

마지막으로, 캐싱 프록시를 사용하도록 sbuild를 구성해야 해요. 이는 `~/.config/sbuild/config.pl` 파일에 다음 코드 조각을 추가하여 수행돼요:

```
# 캐싱 프록시 사용
push @{$unshare_mmdebstrap_extra_args}, "*", [
  '--aptopt=Acquire::HTTP::Proxy "http://localhost:9999";',
];
```

변경 사항을 즉시 적용하려면 빌드 환경을 제거하세요 (`rm ~/.cache/sbuild/*`), 그러면 sbuild가 이 새로운 구성 옵션으로 환경을 재구성할 수 있어요.