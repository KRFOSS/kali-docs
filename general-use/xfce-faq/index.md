---
title: Kali Linux XFCE FAQ
description:
icon:
date: 2019-11-22
type: post
weight: 15
author: ["re4son",]
tags: ["",]
keywords: ["",]
og_description:
---

## Kali Linux XFCE FAQ

The new Kali Linux Desktop is incredibly fast and absolutely gorgeous. Here are some tips and tricks to help you find your way around it quickly.

#### Topics

[Desktop Environments, Switching](#switch-desktop-environments)

[HiDPI](#hidpi)

[Theme](#theme)

[Feedback](#feedback)
&nbsp;  
&nbsp;  
&nbsp;  
#### Switch Desktop Environments

**Q:** I absolutely love the new theme and I desperately want it, but without having to re-install my system. How can I migrate my existing Kali Linux installation?

**A:** Run `apt update && apt install kali-desktop-xfce` in  a terminal session to install the new Kali Linux Xfce environment. If you would also like to remove the Gnome window manager, which we do not recommend until you are sure you are ready to, run `apt purge --autoremove kali-desktop-gnome`. Be sure to run this *after* installing Xfce.

When asked to select the "*Default display manager*", choose `lightdm`.  

Next time you login you can choose "XFCE" in the session selector in the top right hand corner of the login screen.  
&nbsp;  
&nbsp;  

**Q:** I installed Xfce, but it doesn't look like the preview. How can I get it to look the same?

**A:** If you are having issues, it may be that a config file is not set properly. First, backup .cache, .config, and .local. Next, running `rm -r .cache .config .local` and then rebooting will likely fix those issues.
&nbsp;  
&nbsp; 

**Q:** How can I get a Kali Linux image with GNOME instead of XFCE?  

**A:**  Just download the Kali GNOME image from https://www.kali.org/downloads/  
&nbsp;  
&nbsp;  
  
**Q:** I tried XFCE and I really like it but I still would like switch back to GNOME. How can I do that?  
  
**A:** You can run `apt update && apt install kali-desktop-gnome` in a terminal session.  
Next time you login you can choose "GNOME" in the session selector in the top right hand corner of the login screen.  
&nbsp;  
&nbsp;  
  
#### HiDPI

**Q:** I have a HiDPI screen and everything looks tiny. Is there a way to improve that?  
  
**A:** XFCE supports HiDPI monitors. You can either set the scaling factor to "*x2*" in "*Settings -> Appearance -> Settings -> Windows Scaling*" or run  
`xfconf-query -c xsettings -p /Gdk/WindowScalingFactor -s 2 && xfconf-query -c xfwm4 -p /general/theme -s Default-xhdpi`  
in a terminal window.  
To increase the scaling of the lightdm login screen, just set "*xft-dpi*" to *200* or higher in the file `/etc/lightdm/lightdm-gtk-greeter.conf`.   
&nbsp;  
&nbsp;   
  
#### Screen Captures

**Q:** How can I take screenshots?  
  
**A:** Press the `Print Screen` key on your keyboard will launch screenshooter. Pressing enter will take the screenshot. Alternatively you can click on the "*Screen-Recorder*" icon in the quick-launch panel (the far right icon in the panel next to the application menu) and choose "Screenshot".  
&nbsp;  
&nbsp;  
  
**Q:** How can I record videos of my screen activity?  
  
**A:** Press `Ctrl` & `Print Screen`on your keyboard will launch the screen recorder. Pressing enter will start recording. Alternatively you can click on the "*Screen-Recorder*" icon in the quick-launch panel (the far right icon in the panel next to the application menu).  
&nbsp;  
&nbsp;  
  
#### Theme  

**Q:** How can I switch to a lighter, brighter theme?  
  
**A:** Kali Linux provides two default themes: dark and light.  
To switch to the light theme,   
go to "*Settings -> Appearance*" and:  
  
- In the "*Style*" tab, select "*Kali-Light*"
- In the "Icons" tab, select "Flat-Remix-Blue-Light"
  
go to "*Settings -> Window Manager*" and:  
  
- In the "*Style*" tab, select "*Kali-Light*"
  
To switch from "*Light*" to "*Dark*", just select the Dark themes in these settings.  
&nbsp;  
&nbsp;  

**Q:** I love the buttons on the right hand side, but I'd love them even more on the the left. How can I switch?  

**A:** You can move the buttons from one side to the other in "*Settings -> Window Manager-> Style -> Button Layout*". Just drag and drop them to the other side of the word "Title".   
&nbsp;  
&nbsp;  

#### Feedback

**Q:** How can I get in touch to discuss some questions I have?  

**A:** Please join us in the [Kali Forums](https://forums.kali.org/). It is the home of a vibrant community and the best place to discuss everything around Kali Linux.  
&nbsp;  
&nbsp;  

**Q:** I have found a bug. Who should I talk to?  

**A:** Awesome. Not the fact that there is bug but that you found it. Please open a bug report in the [Kali Linux Bug Tracker](https://bugs.kali.org/). We really appreciate your help in making Kali Linux better.  

