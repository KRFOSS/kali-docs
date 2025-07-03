---
title: 칼리의 기본 자격 증명
description: 칼리의 비밀번호는 무엇인가요
icon:
weight: 60
author: ["g0tmi1k",]
번역: ["xenix4845"]
---

칼리는 [**2020.1** 버전 출시](/blog/kali-default-non-root-user/) 이후로 기본적으로 [비루트 사용자 정책](/docs/policy/kali-linux-user-policy/)으로 변경되었습니다.

이는 다음을 의미합니다:

- [**amd64** 이미지](/docs/installation/) 설치 중에는 표준 사용자 계정 생성을 요청하는 프롬프트가 표시됩니다.

- **라이브 부팅**(Live Boot) 또는 **사전 생성된 이미지**(**[가상 머신](/docs/virtualization/)** 및 **[ARM](/docs/arm/)** 같은)에서 사용되는 기본 운영 체제 자격 증명은 다음과 같습니다:
    - 사용자: `kali`
    - 비밀번호: `kali`

- **Vagrant** 이미지 _(그들의 [정책](https://www.vagrantup.com/docs/boxes/base.html)에 기반함)_:
    - 사용자 이름: `vagrant`
    - 비밀번호: `vagrant`

- [Amazon **EC2**](/docs/cloud/aws/):
    - 사용자: `kali`
    - 비밀번호: `<ssh key>`

## 도구 기본 자격 증명

칼리와 함께 제공되는 일부 도구는 자체 하드코딩된 기본 자격 증명을 사용합니다(다른 일부는 처음 사용할 때 새 비밀번호를 생성합니다). 다음 도구들은 기본값을 가지고 있습니다:

- [BeEF-XSS](/tools/beef-xss/)
    - 사용자 이름: `beef`
    - 비밀번호: `beef`
    - 설정 파일: `/etc/beef-xss/config.yaml`

- MySQL
    - 사용자: `root`
    - 비밀번호: ` ` _(공백)_
    - 설정 프로그램: `mysql_secure_installation`

- [OpenVAS](/tools/gvm/)
    - 사용자 이름: `admin`
    - 비밀번호: `<설정 중 생성됨>`
    - 설정 프로그램: `openvas-setup`

- [Metasploit-Framework](/tools/metasploit-framework/)
    - 사용자 이름: `postgres`
    - 비밀번호: `postgres`
    - 설정 파일: `/usr/share/metasploit-framework/config/database.yml`

- PowerShell-Empire/Starkiller
    - 사용자 이름: `empireadmin`
    - 비밀번호: `password123`

- - -

[2020.1](https://kali.org/blog/kali-linux-2020-1-release/)보다 이전 버전의 칼리 리눅스에 대해서는 [이전 자격 증명 정보](/docs/introduction/kali-linux-default-passwords/) 및 [루트 정책](/docs/policy/kali-linux-root-user-policy/) 정보를 참조하세요.
