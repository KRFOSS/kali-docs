---
title: Installing old i386 images
description:
icon:
weight:
author: ["arnaudr", "gamb1t",]
---

## Foreword

Since October 2024, there is no longer a i386 kernel, nor i386 images for Kali Linux. As a reminder, i386 is the name used in Kali (and Debian) to refer to the 32-bit x86 CPU architecture.

In all practicality, it means that it's no longer possible to run Kali on machines that have such a (rather old) CPU. However, it's still possible to run programs that were compiled for i386 on a 64-bit machine. In fact, at the time of writing, most of the i386 packages are still available in the repository, and i386 Kali containers are also available.

This change was announced in a [blog post](/blog/end-of-i386-kernel-and-images/), it contains much more details for those interested.

## Using old i386 images

The last Kali Linux release that supported the i386 architecture was `2024.3`. At the moment, those images are still available for download at <https://kali.download/base-images/kali-2024.3/>.

After they get removed from the mirrors, users can still use [old.kali.org](https://old.kali.org/kali-images/kali-2024.3/) and download the last i386 images from there.
