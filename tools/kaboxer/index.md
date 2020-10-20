---
title: Kaboxer
description:
icon:
date: 2020-10-09
type: post
weight: 100
author: ["lolando",]
tags: ["",]
keywords: ["",]
og_description:
---

## Kaboxer
Kaboxer is a framework to manage applications in containers on Kali Linux (and other Debian-based) systems. It allows shipping applications that are hard to package properly or that need to run in isolation from the rest of the system.

The framework has two parts; the ``kaboxer`` tool is the main UI for starting, stopping, creating and managing containers and the relevant images. There's also ``dh_kaboxer``, a helper that can be used in source packages and that partially automates the process of creating a package for an app that uses Kaboxer.

## Packaging a simple application with Kaboxer

Kaboxer currently only supports Docker as the isolation/container
mechanism.  As such, the first step when preparing an application to
be packaged with Kaboxer ("kaboxed" for short) is to write a
**Dockerfile**.  There are few options that are required by Kaboxer,
but otherwise it's a standard Dockerfile.  For instance, the
``kbx-hello-cli`` application uses the following Dockerfile:

```
FROM debian:stable-slim
RUN apt-get update && apt-get install -y \
    python3 \
    python3-prompt-toolkit
ARG KBX_APP_VERSION=0.5
RUN mkdir /kaboxer ; echo $KBX_APP_VERSION > /kaboxer/version
COPY ./kbx-hello /kbx-hello
```

Pretty straightforward, except for the two lines about
``$KBX_APP_VERSION``.  These are used by Kaboxer to track the
"upstream" version of the application being packaged.  Note the
starting point of the image (which is based on a slimmed-down version
of Debian), and the installation of a couple of packages that are
dependencies of ``kbx-hello-cli`` (namely, ``python3`` and
``python3-prompt-toolkit``).  In this case, the application consists
of a single ``kbx-hello`` program that is installed inside the
container as ``/kbx-hello``.

Now given this Dockerfile, we can build a Docker image for the app.
Kaboxer is more than that though, as it allows distributing this image
in various ways, and more importantly it allows adding instructions on
how to run that image so that the kaboxed app can be seamlessly
integrated within the system and run just like any other app.  This is
done via a ``kaboxer.yaml`` file; it will most likely be named
``kbx-hello-cli.kaboxer.yaml``, in order to distinguish it from files
for other apps.  This file uses the YAML syntax for
machine-readability, but it's also quite human-readable (and, more to
the point, human-writable).  A minimal ``kaboxer.yaml`` file could read
like the following:

```
application:
    id: kbx-hello-cli
    name: Hello World for Kaboxer (CLI)
    description: >
        kbx-hello is the hello-world application demonstrator for Kaboxer
packaging:
  revision: 1
components:
  default:
    run_mode: cli
    executable: /kbx-hello cli
```

The required information is split into sections and subsections, with
a clear structure.  The ``application`` section describes the app
itself; the ``packaging`` section contains metadata about the
packaging of the app.  The third section, ``components``, describes
each component of the app (there's only one for ``kbx-hello-cli``, but
we'll see a multi-component app later).  The most important fields
within each subsection are ``executable``, which specifies how to
start the app within the container, and ``run_mode``, which describes
what kind of app this is.  In our case, we picked ``cli``, which is
for text-mode apps.

There, we have our two required files.  Now let's build the Kaboxer
image:

```
kaboxer build kbx-hello-cli
```

This command will run Docker stuff, so it requires some
privileges: at least membership in the ``docker`` or ``kaboxer``
groups.

Once it is completed, run the app in its container with the following
command:

```
kaboxer run kbx-hello-cli
```

Voilà, ``kbx-hello-cli`` is now running in isolation!

## Sharing resources (network or file system)

Many times, even though the app runs in its isolated container, you'll
want to let it interact with the outside world in some way, either by
sharing some part of the file system or by letting it access the
network.

Sharing a part of the file system is a simple matter of defining a
"mount" for the component.  A "mount" makes a source directory (on the
host) available inside the container as the target directory.  This
allows persisting data across runs, since even if the container is
stopped and removed, the data that is stored in the source directory
is not touched.  It also allows sharing parts of the file system
across containers.  For instance, assuming we want to make the
``/var/lib/kbx-hello`` directory available to the container as
``/data``, we'd add the following section to our component definition:

```
components:
  default:
    [...]
    mounts:
      - source: /var/lib/kbx-hello
        target: /data
```

For applications that need interaction, it will often be necessary to
also allow the app to be accessed from outside the container.  In many
cases, it will just be a matter of exposing one port.  For instance,
the following exposes port 8123 from the container (but no other: if
the app itself uses several ports internally, only the published ports
will be accessible from outside the container):

```
components:
  default:
    [...]
    publish_ports:
      - 8123
```

## Multi-component applications

Kaboxer also allows packaging applications that have different
components, for instance a server part and a client part; they can
either be run in isolation or in a shared container, depending on the
needs.

The simple, "shared container" scenario is illustrated by
``kbx-hello-allinone``; the ``kbx-hello`` application has all three
components in the same Kaboxer app, and they run in the same
container.  For that, once the server part is running in its
container, the other parts need to be started within that running
container.  Kaboxer automates that when the client components declare
the following:

```
components:
  [...]
  gui:
    [...]
    reuse_container: true
```

In more complex scenarios, application may need to isolate the
components from one another, but still allow them to communicate
through the network.  It would of course be possible to publish ports
to the outside of the server container, but that would leave them open
to the world; in that case, it's simpler to just define a private
network and plug the containers into this network.  For instance, both
``kbx-hello-cli`` and ``kbx-hello-server`` define the following
network:

```
components:
  default:
    [...]
    networks:
      - kbx-hello
```

This means that even though the server part is not accessible from the
host outside the server container, it is accessible from the cli
container: the cli can connect to the ``kbx-hello-server`` host and
the connection will be automatically routed through the private
network to the other container.

## Going further

This tutorial is only about getting started with Kaboxer; all the
options are described in more details in the appropriate manual pages,
namely ``kaboxer(1)`` and ``kaboxer.yaml(5)``.  The ``kaboxer``
package also provides a rather comprehensive sample ``kbx-hello``
application that illustrates the settings and the operation; have a
look in ``/usr/share/doc/kaboxer/examples/kbx-hello``.
