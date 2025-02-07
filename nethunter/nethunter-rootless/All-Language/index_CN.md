---
title: NetHunter Rootless
description:
icon:
weight:
author: ["re4son",]
---

<!-- Based on https://gitlab.com/kalilinux/nethunter/build-scripts/kali-nethunter-rootless -->

本文（翻译版）是本人机翻加上本人理解的注释，仅供参考，翻译错误欢迎指正

## 适用于未取得root权限的NetHunter程序组
##### *最大限度的灵活性，无需承诺*
##### 本文（翻译版）是本人（GoldleafOS）机翻加上本人理解的注释，仅供参考，翻译错误欢迎指正 ：）

在任何没有root权限和解锁bootloader的Android设备上安装Kali NetHunter，而不会使保修失效（部分Android设备厂商会使解锁BL的手机失去保修，或者像部分手机厂商并不开放BL解锁）。

![](010-NH-Rootless-Installation_Start_s.png)

![](020-NH-Rootless-KeX_s.png)

准备（先决条件）:
--------------

Android 设备
(未经修改BL的设备，无需root或自定义recovery镜像)

下载并安装:
--------------

- 安装NetHunter-Store from [store.nethunter.com](https://store.nethunter.com/)
- 从NetHunter Store中下载并安装：“ __Termux__ 、__Nethunter-Kex client__ 和 __Hacker's keyboard__ ”（但是也可以从其他源和渠道下载，但nethunter应用商店审查过它们，没有恶意代码和脚本，其他源可能未经审查，藏有恶意代码）
  _注意:
      _安装后，商店客户端中的“安装”按钮可能不会更改为“已安装”，只需忽略即可(也可能无法安装，请到高级选项处关闭“特权插件”)_
      _在某些设备上显示“正在安装”时，首次启动termux似乎会卡住——只需按enter键即可_

- 在Termux中运行以下命令:

```console
kali@kali:~$ termux-setup-storage
kali@kali:~$ pkg install wget
kali@kali:~$ wget -O install-nethunter-termux https://offs.ec/2MceZWr
kali@kali:~$ chmod +x install-nethunter-termux
kali@kali:~$ ./install-nethunter-termux
```

如何使用:


下列命令可以在Termux中使用:

| Command                   | To                                                      |
| ------------------------- | ------------------------------------------------------- |
| `nethunter`               | 启动Nethunter命令行        |
| `nethunter kex passwd`    | 配置或修改KEX密码（仅在首次使用前或丢失密码后需要） |
| `nethunter kex &`         | 启动Kali NetHunter桌面体验用户会话   |
| `nethunter kex stop`      | 停止Kali NetHunter桌面体验                  |
| `nethunter <command>`     | 在NetHunter环境中运行<自定义命令>                  |
| `nethunter -r`            | 以root身份启动Kali NetHunter CLI                        |
| `nethunter -r kex passwd` | 为root配置KEX密码                     |
| `nethunter -r kex &`      | 以root身份启动Kali NetHunter桌面体验         |
| `nethunter -r kex stop`   | 停止以root身份启动的Kali NetHunter桌面    |
| `nethunter -r kex kill`   | 杀死（关闭）所有KEX服务端                              |
| `nethunter -r <command>`  | 以root身份在NetHunter环境中运行<自定义命令>        |

注意：命令 `nethunter` 可以缩写为`nh`。
_小贴士：如果您在后台运行kex（`&`）而没有设置密码，请在提示输入密码时首先将其带回前台，即通过`fg<job id>`-稍后您可以通过`Ctrl+z`和`bg<作业id>`再次将其发送到后台`_

要使用KeX，请启动KeX客户端，输入密码并单击连接
_小贴士：为了获得更好的观看体验，请在KeX客户端的“高级设置”下输入自定义分辨率_



## NetHunter 版本:

请参阅 [this table](/docs/nethunter/#1-0-nethunter-editions) ，了解不同nethunter版本的比较。

## 提示:

1. 安装后首先运行 sudo apt update && sudo apt full-upgrade -y 以 更新 Kali。如果您有足够的存储空间，您可能还想运行 sudo apt install -y kali-linux-default。
2. 所有的渗透测试工具应该都能工作，但有些可能有限制，例如，Metasploit 可以工作，但没有数据库支持。如果您发现任何工具不能工作，请在我们的 论坛 上发布。
3. 一些工具，如 "top"，在未 root 的手机上无法运行。
4. 非 root 用户在 chroot 中仍然有 root 访问权限。这是 proot 的特性。请注意这一点。
5. Galaxy 手机可能会阻止非 root 用户使用 sudo。请改用 "su -c"。
6. 通过停止所有 nethunter 会话并在 termux 会话中输入以下命令来定期备份您的 rootfs： tar -cJf kali-arm64.tar.xz kali-arm64 && mv kali-arm64.tar.xz storage/downloads 这将把备份放在您的 Android 下载文件夹中。 注意：在较旧的设备上，将 "arm64" 改为 "armhf"
7. 请加入我们的 论坛，交流技巧和想法，成为努力使 NetHunter 变得更好的社区的一部分。


##### 感谢 百度翻译、MARSCODE AI 提供的翻译服务！本人增加了个人见解！感谢Kali社区！
