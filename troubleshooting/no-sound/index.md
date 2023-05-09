---
title: No sound on Kali 2023.2
description:
icon:
weight: 5000
author: ["arnaudr", "gamb1t",]
---

Starting version 2023.2, Kali Linux uses [PipeWire](https://pipewire.org/) to manage its audio, for both the XFCE desktop and GNOME desktop. Before that, Kali Linux used another sound server named [PulseAudio](https://www.freedesktop.org/wiki/Software/PulseAudio/). (Note: for those who use Kali's KDE desktop: PulseAudio is still in charge)

The change should be seamless, as PipeWire provides a compatibility layer (a service named `pipewire-pulse`). Therefore legacy applications that were designed to work with PulseAudio should keep working as if nothing happened, blissfully unaware of the change.

This being said: if you encounter some audio issues after upgrading to Kali 2023.2, please reach out on [Kali's bugtracker](https://bugs.kali.org) so we can help troubleshoot the issue. **Please do NOT uninstall the package `pipewire-pulse`**, despite all the Internet pages telling you to do so! This used to be a fix in an older version of Kali Linux, and therefore this "fix" is now copy/pasted everywhere on the Internet. But now that we switched to PipeWire, uninstalling this package would be counter-productive, and would break your system even more. Please don't.

## Issues with VMWare Workstation

There have been reports of audio making cracking noises and flickering on VMWare Workstation. It seems that it can be fixed by following this short guide: <https://gitlab.freedesktop.org/pipewire/pipewire/-/wikis/Troubleshooting#stuttering-audio-in-virtual-machine>.
