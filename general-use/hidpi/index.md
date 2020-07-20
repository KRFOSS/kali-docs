---
title: HiDPI (High Dots Per Inch) Display
description:
icon:
date: 2020-02-18
type: post
weight: 10
author: ["g0tmi1k",]
tags: ["",]
keywords: ["",]
og_description:
---

Upon starting Kali Linux back up, certain things may appear smaller or larger than expected. This could be because of **HiDPI** (aka **High DPI**). It all depends on the software in question, with how it was made, (e.g. GTK2, GTK3, Qt5 etc).

This could be happening for various reasons, such as the graphic card drivers and/or the monitor profile.

This guide will cover single screen setups. **We do not have the hardware in order to test multiple display outputs to write up the guide. So we are looking for [community contribution](https://www.kali.org/docs/community/contribute/) to help out. If you have the hardware, and expertise, please [edit this guide](https://gitlab.com/kalilinux/documentation/kali-docs/edit/master/general-use/hidpi/index.md)!**

## Xfce

Xfce does support HiDPI monitors. Though you may need to alter a few places, depending on your hardware, versions and issues to get it working.

In case you need a more general script to enable HiDPI in your desktop, here you have one that applies the configurations explained after ("**Scaling Factor**" & "**Cursor size**").
**Remember to log out and in again after running it**.

```
xfconf-query -c xfwm4 -p /general/theme -s Kali-Dark-xHiDPI
xfconf-query -c xsettings -p /Gdk/WindowScalingFactor -n -t 'int' -s 2
cat <<EOF >> ~/.xsessionrc
  export QT_SCALE_FACTOR=2
  export XCURSOR_SIZE=48
  export GDK_SCALE=2
EOF
```

Below is more of an explanation of the actions.

### Scaling Factor

#### GTK

After logging into Kali, the wallpaper may look "normal", but everything else might be "a little small to read".

![](scaling-factor.png)

Increasing the "Scaling Factor" from "x1" to "x2" should address this problem.
You have two ways of altering this, either graphical or through the command line. To alter the scaling factor to "*x2*":

- In a terminal window, run the following commands:

```
kali@kali:~$ echo export GDK_SCALE=2 >> ~/.xsessionrc
kali@kali:~$
kali@kali:~$ xfconf-query -c xfwm4 -p /general/theme -s Kali-Dark-xHiDPI
kali@kali:~$
kali@kali:~$ xfconf-query -c xsettings -p /Gdk/WindowScalingFactor -n -t 'int' -s 2
kali@kali:~$
```

- Graphical:
  - Kali -> Settings -> Appearance -> **Settings** -> **Windows Scaling**
  - Kali -> Settings -> Appearance -> **Window Manager** -> **Theme: `Kali-Dark-xHiDPI`**

![](kali-menu-setting-manager.png)
![](appearance-settings.png)
![](window-manager.png)

**The quickest way to clean up any left over artifacts is to now log out and in again**.

#### Qt

Some apps, such as [qTerminal](https://packages.debian.org/testing/qterminal), don't use the scale factor explained before, so they need to be configure separately. To do so, you need to set the following environmental variables in the `~/.xsessionrc` file:

```
kali@kali:~$ echo export QT_SCALE_FACTOR=2 >> ~/.xsessionrc
kali@kali:~$
```

### Cursor size

Enabling HiDPI settings can cause some issues with the mouse size, and you might see how its size varies depending on the application you place it over. To solve this, you can force the cursor size with the following command:

```
kali@kali:~$ echo export XCURSOR_SIZE=48 >> ~/.xsessionrc
kali@kali:~$
```

{{% notice info %}
You may need to try increasing the value from `48`.
{{% /notice %}}

- - -

## Small/Large Font

Upon opening some applications, the font may appear smaller/larger than expected.

In the example below, you can see two different terminal software, one on the left is using Qt (`QTerminal`) and the one on the right is using GTK (`xfce4-terminal`).

![](large-font.png)

You have two ways of altering this, either graphical or through the command line. To alter the DPI:

- In a terminal window, run the following commands:

```
kali@kali:~$ vim ~/.xsessionrc
kali@kali:~$
kali@kali:~$ cat ~/.xsessionrc
xrandr --dpi 180
kali@kali:~$
```

- Graphical:
  - Kali -> Settings -> Appearance -> Fonts -> DPI
    - Enable: `Custom DPI Settings`
    - Value: `180`

![](kali-menu-setting-manager.png)
![](appearance-fonts.png)

{{% notice info %}
Due to a bug, you will need to either toggle `Custom DPI Settings` or increase/decrease the value then restore it back to the value previously.
{{% /notice %}}

{{% notice info %}
You may need to try increasing the value from `180`.
{{% /notice %}}

The next time you open a programmatic program back up, the font should now be "normal".

## Login Screen

Are you experiencing an issue with the login screen (`lightdm`), with the login box being smaller than "normal"?

![](login.png)

A possible solution would be to set "`xft-dpi`" to "`180` (**or higher**):

```
kali@kali:~$ grep xft-dpi /etc/lightdm/lightdm-gtk-greeter.conf
xft-dpi = 96
kali@kali:~$
kali@kali:~$ sudo vim /etc/lightdm/lightdm-gtk-greeter.conf
kali@kali:~$
kali@kali:~$ cat /etc/lightdm/lightdm-gtk-greeter.conf
[greeter]
...SNIP...
xft-dpi = 180
...SNIP...
kali@kali:~$
```

{{% notice info %}
You may need to try increasing the value from `180`.
{{% /notice %}}
