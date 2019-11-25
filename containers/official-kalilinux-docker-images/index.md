---
title: Official Kali Linux Docker Images
description:
icon:
date: 2019-10-26
type: post
weight: 10
author: ["rhertzog",]
tags: ["",]
keywords: ["",]
og_description:
------

Kali provides official docker images that are updated once a week on
Docker Hub in the [kalilinux
organization](https://hub.docker.com/u/kalilinux/). You can thus easily
build your own Kali containers on top of those that we provide.

Here's a quick review of the various images available. First those that
you might reasonably want to use:

* [kalilinux/kali-rolling](https://hub.docker.com/r/kalilinux/kali-rolling)
  is the main image that you should likely use, tracking the
  continuously-updated `kali-rolling` package repository.
* [kalilinux/kali](https://hub.docker.com/r/kalilinux/kali) is built from
  the `kali-last-snapshot` repository, it is thus tracking the last versioned
  release (e.g. 2019.3, 2019.4, etc.) and will not get any update until
  the next release.
* [kalilinux/kali-bleeding-edge](https://hub.docker.com/r/kalilinux/kali-bleeding-edge)
  is exactly like `kalilinux/kali-rolling` with the [kali-bleeding-edge
  package repository](https://www.kali.org/news/bleeding-edge-kali-repositories/)
  enabled in APT's configuration

And those that you will likely not need except in very special cases:

* [kalilinux/kali-dev](https://hub.docker.com/r/kalilinux/kali-dev) is an
  image tracking the `kali-dev` package repository used by Kali developers
  to merge updates coming from Debian and changes maintained by Kali. It
  can be useful to rebuild (or do test rebuild of) Kali packages.
* [kalilinux/kali-experimental](https://hub.docker.com/r/kalilinux/kali-experimental)
  is exactly like `kalilinux/kali-dev` with the `kali-experimental`
  package repository enabled in APT's configuration. Might be useful to
  test some not-yet-ready updates uploaded to kali-experimental by
  Kali developers who are looking for feedback...
* [kalilinux/kali-linux-docker](https://hub.docker.com/r/kalilinux/kali-linux-docker)
  is the former official image, it's no longer updated. Don't use it.

If you want to improve our official docker images, have a look at the
[kali-docker project](https://gitlab.com/kalilinux/build-scripts/kali-docker/) in our
GitLab. We use GitLab CI to automate the build of our Docker images.
