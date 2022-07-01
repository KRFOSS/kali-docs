---
title: Packaging Applications with Kaboxer
description:
icon:
weight: 100
author: ["lolando", "rhertzog",]
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

```console
kali@kali:~$ sudo apt install -y kaboxer
```

## Introduction to Kaboxer

The idea of Kaboxer is to prepare ready-to-run application images,
make them available online in a Docker registry and then let users
fetch those images and start/stop containers to run the applications. All
those steps are handled with the `kaboxer` command line tool.

To stay close to the usual way of distributing applications
through Debian packages, Kaboxer makes it easy to build packages
that will transparently download the Docker image at installation
time and that will seamlessly integrate the application in the system.
This is notably achieved by providing some integration with the debhelper
tool (with `dh_kaboxer` and a specific build system).

In this tutorial, we will successively build the image and run the
application but in practice both steps usually happen in different
contexts: the build happens once on a server that uploads the resulting
image in a public registry, whereas end-users only download the
image to run it.

## Packaging a simple application with Kaboxer

### Prerequisites

The commands described in this tutorial should be run from within the
`hello-kbx` directory, which is part of the Kaboxer source code. You
can get it with Git:

```console
kali@kali:~$ git clone https://gitlab.com/kalilinux/tools/kaboxer.git
Cloning into 'kaboxer'...
[...]

kali@kali:~$ cd kaboxer/hello-kbx
```

Kaboxer needs access to the Docker daemon so you must ensure that your
user has the required rights: it needs to be part either of the `docker`
group (which has direct write access to the Docker socket) or of the
`kaboxer` group (which can get the required permissions with `sudo`).

If you installed Kali 2020.4 or newer, the initial user created during
installation should already be part of the `kaboxer` group and have the
required privileges. You can check with the command `groups`:

```console
kali@kali:~$ groups
[...] kaboxer [...]
```

If your current user is not part of the `kaboxer` group, then you should
grant the group membership with the following command:

```console
kali@kali:~$ sudo adduser $USER kaboxer
```

Note that the new group membership will only be visible after having
restarted your user session (or after having created a new sub-session
with a command like `sg`, `newgrp` or `su`).

### Creating a Docker image

Kaboxer currently only supports Docker as the isolation/container
mechanism. As such, the first step when preparing an application to
be packaged with Kaboxer ("kaboxed" for short) is to write a
**Dockerfile**. There are few options that are required by Kaboxer,
but otherwise it's a standard Dockerfile. For instance, the
`hello-cli` application uses the following Dockerfile:

```dockerfile
FROM debian:stable-slim
RUN apt update && apt install -y \
    python3 \
    python3-prompt-toolkit
COPY ./hello /usr/bin/hello
RUN mkdir /kaboxer \
 && hello version > /kaboxer/version
```

Pretty straightforward, except for the last lines that save the version
of the application in `/kaboxer/version`. This is used by Kaboxer to
track the "upstream" version of the application being packaged. Note the
starting point of the image (which is based on a slimmed-down version
of Debian), and the installation of a couple of packages that are
dependencies of `hello-cli` (namely, `python3` and
`python3-prompt-toolkit`). In this case, the application consists
of a single `hello` program that is installed inside the
container as `/usr/bin/hello`.

Now given this Dockerfile, we can build a Docker image for the app.

### Adding Kaboxer meta-information

Kaboxer is more than that though, as it allows distributing this image
in various ways, and more importantly it allows adding instructions on
how to run that image so that the kaboxed app can be seamlessly
integrated within the system and run just like any other app. This is
done via a `kaboxer.yaml` file; it will most likely be named
`hello-cli.kaboxer.yaml`, in order to distinguish it from files
for other apps in the same directory. This file uses the YAML syntax for
machine-readability, but it's also quite human-readable (and, more to
the point, human-writable). A minimal `kaboxer.yaml` file could read
like the following:

```yaml
application:
  id: hello-cli
  name: Hello World for Kaboxer (CLI)
  description: >
    hello-kbx is the hello-world application demonstrator for Kaboxer
packaging:
  revision: 1
components:
  default:
    run_mode: cli
    executable: /usr/bin/hello cli
```

The required information is split into sections and subsections, with
a clear structure. The `application` section describes the app
itself; the `packaging` section contains metadata about the
packaging of the app. The third section, `components`, describes
each component of the app (there's only one for `hello-cli`, but
we'll see a multi-component app later). The most important fields
within each subsection are `executable`, which specifies how to
start the app within the container, and `run_mode`, which describes
what kind of app this is. In our case, we picked `cli`, which is
for text-mode apps.

### Building the image and testing it

There, we have our two required files. Now let's build the Kaboxer
image:

```console
kali@kali:~$ kaboxer build hello-cli
```

As mentioned above, Kaboxer requires privileges to use Docker,
hence this command will fail if you're not part of either the
`docker` or the `kaboxer` group.

Note that if the build fails, Kaboxer will not provide much information to
figure out what's going wrong. In that case, you should try to build your
Docker image directly with `docker build` so that you can see the precise
error message. Here's how you would do that (you need membership in the
`docker` group, or root rights):

```console
kali@kali:~$ docker build -f hello-cli.Dockerfile -t kaboxer/hello-cli .
```

Once `kaboxer build` ran successfully, let's try running the app in its
container with the following command:

```console
kali@kali:~$ kaboxer run hello-cli
[...]
PermissionError: [Errno 13] Permission denied: '/var/lib/hello-kbx'
```

Ouch! If you look into the file `hello-cli.kaboxer.yaml`, you can see
that the directory `/var/lib/hello-kbx` is expected to exist in order
to be mounted in the container at `/data`. Creating this directory
requires root privileges, and it's something that would be done by the
package manager if you were to install `hello-cli-kbx` with APT.

For the sake of our example here, the most simple solution is to
use another directory, that can be created automatically without
requiring root privileges. Let's use `/tmp/hello-kbx` instead:

```console
$ sed -i 's;/var/lib/hello-kbx;/tmp/hello-kbx;' *.kaboxer.yaml
```

Now let's try to run the app again:

```console
kali@kali:~$ kaboxer run hello-cli
fetch | save <value> | delete | exit ?
```

Voilà, `hello-cli` is now running in isolation!

There's not much it can do alone, and the next step would be to
build `hello-server`, run it in a separate terminal, and then use
`hello-cli` to communicate with the server. It should be
straightforward enough so we won't cover this part, instead let's
jump to the next topic: Debian packaging files.

### Adding Debian packaging files

You can add the initial packaging files with `dh_make` like for
any other application. Besides the usual changes, we tweak
a few files to enable the integration with Kaboxer:

1. we add `kaboxer` to `Build-Depends` in `debian/control` so that
   the debhelper integration offered by Kaboxer is available at build-time
1. we ensure that we have `${misc:Depends}` in the `Depends` fields in
   `debian/control` so that `dh_kaboxer` can inject the appropriate
   dependency (mainly on Docker and Kaboxer currently)
1. we modify `debian/rules` to enable the debhelper integrations by
   changing the `dh $@` call into `dh $@ --with kaboxer
   --buildsystem=kaboxer`

```console
kali@kali:~$ cat debian/control
Source: hello-kbx
[...]
Build-Depends: debhelper-compat (= 13), kaboxer
[...]

Package: hello-cli-kbx
Architecture: all
Depends: ${misc:Depends}
[...]

kali@kali:~$ cat debian/rules
#!/usr/bin/make -f

%:
	dh $@ --with=kaboxer --buildsystem=kaboxer
```

At this point, you can build a Debian package already but it will
not work properly because it doesn't know how to retrieve the Docker
image.

You can either push the image to some Docker registry and modify
the `kaboxer.yaml` file to point to it:

```console
kali@kali:~$ cat kaboxer.yaml
[...]
container:
  type: docker
  origin:
    registry:
      url: https://registry.gitlab.com
      image: kalilinux/packages/hello-kbx/hello
```

Or you can tell the build system to build the image and store it in the
package (beware, the package will be very large!) by setting the
`DH_KABOXER_BUILD_STRATEGY` variable in `debian/rules`:

```console
kali@kali:~$ cat debian/rules
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
"mount" for the component. A "mount" makes a source directory (on the
host) available inside the container as the target directory. This
allows persisting data across runs, since even if the container is
stopped and removed, the data that is stored in the source directory
is not touched. It also allows sharing parts of the file system
across containers. For instance, assuming we want to make the
`/var/lib/hello-kbx` directory available to the container as
`/data`, we'd add the following section to our component definition:

```yaml
components:
  default:
    [...]
    mounts:
      - source: /var/lib/hello-kbx
        target: /data
```

For applications that need interaction, it will often be necessary to
also allow the app to be accessed from outside the container. In many
cases, it will just be a matter of exposing one port. For instance,
the following exposes port 8123 from the container (but no other: if
the app itself uses several ports internally, only the published ports
will be accessible from outside the container):

```yaml
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
`hello-allinone-kbx`; the `hello` application has all three
components in the same Kaboxer app, and they run in the same
container. For that, once the server part is running in its
container, the other parts need to be started within that running
container. Kaboxer automates that when the client components declare
the following:

```yaml
components:
  [...]
  gui:
    [...]
    reuse_container: true
```

In more complex scenarios, application may need to isolate the
components from one another, but still allow them to communicate
through the network. It would of course be possible to publish ports
to the outside of the server container, but that would leave them open
to the world; in that case, it's simpler to just define a private
network and plug the containers into this network. For instance, both
`hello-cli-kbx` and `hello-server-kbx` define the following
network:

```yaml
components:
  default:
    [...]
    networks:
      - hello-kbx
```

This means that even though the server part is not accessible from the
host outside the server container, it is accessible from the cli
container: the cli can connect to the `hello-server-kbx` host and
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
the `kaboxer.yaml` file (with the `categories` field below `application`).
Here's for example how you would put an application in the "Bluetooth
tools" and "Wireless Attacks" categories:

```yaml
application:
  [...]
  categories: Utility;06-02-bluetooth-tools;06-wireless-attacks
```

### Convenience command-line helpers

If you run your applications from the command-line, it can be cumbersome
to always type `kaboxer run --some-option --another-option APP`, hence Kaboxer
automatically generates command-line helpers that are named after the
application ID, the component name (if there's more than one component),
and a `-kbx` suffix.

With these helpers, you can simply start the hello server with
`hello-server-kbx start`, run the cli with `hello-cli-kbx`, and finally
stop the server with `hello-server-kbx stop`.

## Automating the build with GitLab CI

To make it super easy to maintain Kaboxer applications, we are
storing the Kaboxer files in Git repositories and we rely on
GitLab CI to rebuild the Docker image every time that we push
a change.

The Kaboxer project contains a [generic GitLab CI
file](https://gitlab.com/kalilinux/tools/kaboxer/-/blob/master/gitlab-ci/build-docker-image.yml)
that you can just remotely include in your project ([example
here](https://gitlab.com/kalilinux/packages/covenant-kbx/-/blob/kali/master/debian/kali-ci.yml)).
It will build the image and upload it to the project's Docker registry
provided by GitLab. The `kaboxer.yaml` file must be updated to reference
the URL of that Docker registry and Kaboxer will then download the image
from that location when needed.

The repository also contains the Debian packaging files so that we can
build a Kali package like for any other normal application.

## Going further

This tutorial is only about getting started with Kaboxer; all the
options are described in more details in the appropriate manual pages,
namely `kaboxer(1)` and `kaboxer.yaml(5)`. The `kaboxer`
package also provides a rather comprehensive sample `hello-kbx`
application that illustrates the settings and the operation; have a
look in `/usr/share/doc/kaboxer/examples/hello-kbx`.
