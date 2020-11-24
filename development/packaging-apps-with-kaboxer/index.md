---
title: Packaging Applications with Kaboxer
description:
icon:
date: 2020-10-09
type: post
weight: 100
author: ["lolando","rhertzog"]
tags: ["",]
keywords: ["",]
og_description:
draft: true
---

Some applications can't be packaged properly, for example when they have
dependencies on obsolete libraries that are no longer available in Kali.
Others need to run in isolation because their behaviour would break other
applications on the system.

To overcome those problems, we have created Kaboxer to manage
such applications within containers and make them available in Kali Linux
(and other Debian-based) systems like any other application. They can be
launched from the menu and can be installed with `apt install`.

Looks interesting? Let's see how you can package an application
with this new framework. First install the tool itself:

```
$ sudo apt install kaboxer
```


## Introduction to Kaboxer

The idea of Kaboxer is to prepare ready-to-run application images,
make them available online in a docker registry and then let users
fetch those images and start/stop containers to run the applications. All
those steps are handled with the `kaboxer` command line tool.

To stay close to the usual way of distributing applications
through Debian packages, Kaboxer makes it easy to build packages
that will transparently download the docker image at installation
time and that will seamlessly integrate the application in the system.
This is notably achieved by providing some integration with the debhelper
tool (with `dh_kaboxer` and a specific build system).

In this tutorial, we will successively build the image and run the
application but in practice both steps usually happen in different
contexts: the build happens once on a server that uploads the resulting
image in a public registry, whereas end-users only download the
image to run it.

## Packaging a simple application with Kaboxer

### Creating a Docker image

Kaboxer currently only supports Docker as the isolation/container
mechanism.  As such, the first step when preparing an application to
be packaged with Kaboxer ("kaboxed" for short) is to write a
**Dockerfile**.  There are few options that are required by Kaboxer,
but otherwise it's a standard Dockerfile.  For instance, the
`kbx-hello-cli` application uses the following Dockerfile:

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
`$KBX_APP_VERSION`.  These are used by Kaboxer to track the
"upstream" version of the application being packaged.  Note the
starting point of the image (which is based on a slimmed-down version
of Debian), and the installation of a couple of packages that are
dependencies of `kbx-hello-cli` (namely, `python3` and
`python3-prompt-toolkit`).  In this case, the application consists
of a single `kbx-hello` program that is installed inside the
container as `/kbx-hello`.

Now given this Dockerfile, we can build a Docker image for the app.

### Adding Kaboxer meta-information

Kaboxer is more than that though, as it allows distributing this image
in various ways, and more importantly it allows adding instructions on
how to run that image so that the kaboxed app can be seamlessly
integrated within the system and run just like any other app.  This is
done via a `kaboxer.yaml` file; it will most likely be named
`kbx-hello-cli.kaboxer.yaml`, in order to distinguish it from files
for other apps.  This file uses the YAML syntax for
machine-readability, but it's also quite human-readable (and, more to
the point, human-writable).  A minimal `kaboxer.yaml` file could read
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
a clear structure.  The `application` section describes the app
itself; the `packaging` section contains metadata about the
packaging of the app.  The third section, `components`, describes
each component of the app (there's only one for `kbx-hello-cli`, but
we'll see a multi-component app later).  The most important fields
within each subsection are `executable`, which specifies how to
start the app within the container, and `run_mode`, which describes
what kind of app this is.  In our case, we picked `cli`, which is
for text-mode apps.

### Building the image and testing it

There, we have our two required files.  Now let's build the Kaboxer
image:

```
$ kaboxer build kbx-hello-cli
```

This command will run Docker stuff, so it requires some
privileges: at least membership in the `docker` or `kaboxer`
groups.

Note that if the build fails, kaboxer will not provide much information to
figure out what's going wrong. In that case, you should try to build your
docker image directly with `docker build` so that you can see the precise
error message. Here's how you would do that (you need membership in the
`docker` group, or root rights):

```
$ docker build -f Dockerfile . -t kaboxer/kbx-hello-cli
```

Once `kaboxer build` ran successfully, run the app in its container with
the following command:

```
$ kaboxer run kbx-hello-cli
```

Voilà, `kbx-hello-cli` is now running in isolation!

### Adding Debian packaging files

You can add the initial packaging files with `dh_make` like for
any other application. Besides the usual changes, we tweak
a few files to enable the integration with Kaboxer:

1. we add `kaboxer` to `Build-Depends` in `debian/control` so that
   the debhelper integration offered by Kaboxer is available at build-time
1. we ensure that we have `${misc:Depends}` in the `Depends` fields in `debian/control`
   so that `dh_kaboxer` can inject the appropriate dependency
   (mainly on docker and kaboxer currently)
1. we modify `debian/rules` to enable the debhelper integrations by
   changing the `dh $@` call into `dh $@ --with kaboxer
   --buildsystem=kaboxer`

```
$ cat debian/control
Source: kbx-hello
[...]
Build-Depends: debhelper-compat (= 13), kaboxer (>= 0.3)
[...]

Package: kbx-hello-cli
Architecture: all
Depends: ${misc:Depends}
[...]
$ cat debian/rules
#!/usr/bin/make -f

%:
	dh $@ --with=kaboxer --buildsystem=kaboxer
```

At this point, you can build a Debian package already but it will
not work properly because it doesn't know how to retrieve the docker
image.

You can either push the image to some docker registry and modify
the `kaboxer.yaml` file to point to it:

```
$ cat
[...]
container:
  type: docker
  origin:
    registry:
      url: https://registry.gitlab.com/kalilinux/packages/hello-kbx
      image: kaboxer/kbx-hello-cli
```

Or you can tell the build system to build the image and store it in the
package (beware, the package will be very large!) by setting the
`DH_KABOXER_BUILD_STRATEGY` variable in `debian/rules`:

```
$ cat debian/rules
#!/usr/bin/make -f

export DH_KABOXER_BUILD_STRATEGY=tarball

%:
	dh $@ --with=kaboxer --buildsystem=kaboxer
```

## More Kaboxer features

### Sharing resources (network or file system)

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
`/var/lib/kbx-hello` directory available to the container as
`/data`, we'd add the following section to our component definition:

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

### Multi-component applications

Kaboxer also allows packaging applications that have different
components, for instance a server part and a client part; they can
either be run in isolation or in a shared container, depending on the
needs.

The simple, "shared container" scenario is illustrated by
`kbx-hello-allinone`; the `kbx-hello` application has all three
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

### Integrating the application in the Kali menu

When applications are packaged for Kali, they are integrated in the Kali
menu for easy discovery. Applications packaged with the help of Kaboxer
should make no exception. Kaboxer is already generating a `.desktop` file,
so the only thing that is left to do is to ensure that the `Categories` entry
is populated with values used by the Kali menu. You can find a list of the
categories [in this
file](https://gitlab.com/kalilinux/packages/kali-menu/-/blob/kali/master/menus/kali-applications.menu).

Adding a category to the desktop file can be easily done through
the kaboxer.yaml file (with the `categories` field below `application`).
Here's for example how you would put an application in the "Bluetooth
tools" and "Wireless Attacks" categories:

```
application:
    [...]
    categories: Utility;06-02-bluetooth-tools;06-wireless-attacks
```

## Automating the build with GitLab CI

To make it super easy to maintain Kaboxer applications, we are
storing the Kaboxer files in git repositories and we rely on
GitLab CI to rebuild the docker image every time that we push
a change.

The kaboxer project contains a [generic GitLab CI
file](https://gitlab.com/kalilinux/tools/kaboxer/-/blob/master/gitlab-ci/build-docker-image.yml)
that you can just remotely include in your project ([example
here](https://gitlab.com/kalilinux/packages/covenant-kbx/-/blob/kali/master/debian/kali-ci.yml)).
It will build the image and upload it to the project's docker registry
provided by GitLab. The `kaboxer.yaml` file must be updated to reference
the URL of that docker registry and kaboxer will then download the image
from that location when needed.

The repository also contains the Debian packaging files so that we can
build a Kali package like for any other normal application.

## Going further

This tutorial is only about getting started with Kaboxer; all the
options are described in more details in the appropriate manual pages,
namely `kaboxer(1)` and `kaboxer.yaml(5)`.  The `kaboxer`
package also provides a rather comprehensive sample `kbx-hello`
application that illustrates the settings and the operation; have a
look in `/usr/share/doc/kaboxer/examples/kbx-hello`.
