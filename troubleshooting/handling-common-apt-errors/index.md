---
title: Handling common APT problems
description:
icon:
weight:
author: ["gamb1t", "rhertzog",]
---

This page is due for more content as issues come up that need addressing. If you have an issue with APT that is not addressed in this page, please create a [merge request](https://gitlab.com/kalilinux/documentation/kali-docs/-/merge_requests) with the steps to solve the problem or address it, or an [issue](https://gitlab.com/kalilinux/documentation/kali-docs/-/issues) if you are unaware of how to solve the problem but would like to see it added to this page.

## A foreword: please run 'apt update'

Did you run `apt update`? You should. Before installing a package (`apt install ...`) or upgrading your system (`apt full-upgrade`), you should **always** run `apt update`. This is how apt works, there's no way around it, and this will fix the most bizarre issues you might encounter.

```console
kali@kali:~$ sudo apt update
Get:1 http://http.kali.org/kali kali-rolling InRelease [41.2 kB]
[...]
```

## Package is to be installed, however it is not going to be: please use 'apt full-upgrade'

```console
kali@kali:~$ sudo apt upgrade
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
Calculating upgrade... Done
Some packages could not be installed. This may mean that you have
requested an impossible situation or if you are using the unstable
distribution that some required packages have not yet been created
or been moved out of Incoming.
The following information may help to resolve the situation:

The following packages have unmet dependencies:
 libwacom9 : Depends: libwacom-common (= 2.2.0-1) but 1.8-2 is to be installed
E: Broken packages
```

This is a common "issue" that an APT user may experience. This however is not a true error.

Let's step back and take some time to learn about APT basics. There are two different commands that can be used to upgrade your system: `apt ugrade` and `apt full-upgrade`. The first one, "upgrade", is used to install available upgrades of all packages currently installed on the system. New packages will be installed if required to satisfy dependencies, but existing packages will never be removed. In short, it can be seen as a conservative upgrade. The second command, "full-upgrade", performs the function of upgrade but will remove currently installed packages if this is needed to upgrade the system as a whole.

In practice, in a rolling distribution like Kali Linux, packages and their dependencies are updated all the time, and it's often necessary to remove some packages in order to make room for new packages. _Therefore the command "apt upgrade" isn't really useful, and can even be counter-productive. We recommend to always use "apt full-upgrade" to keep your system updated_.

It's also true that "full-upgrade" is a bit more risky than "upgrade", and APT can sometimes (rarely) try to uninstall important packages, leaving you with an unusable system. Therefore you should always pay attention to the list of packages that will be removed. If this list is big (say, compared to the list of packages to upgrade, or new packages to install), then you'd better pause for a moment. Experienced users will be able to know what to do, but for others? If you're really unsure, maybe it's best not to update your system now. Try again in a few days: packages will have changed again, and APT might come up with a better proposition.

You're also free to open a bug at <https://bugs.kali.org>. Please paste the part of your terminal with the APT command(s) you're running, along with the output. Kali Linux developers will be able to assess the situation. It could be that a bug in package dependencies leads to APT doing the wrong thing, and this is something we can fix.

To sum up: always upgrade your system with `apt full-upgrade`.

## The following package has been kept back

```console
kali@kali:~$ sudo apt full-upgrade
[...]
The following packages have been kept back:
  kali-desktop-xfce
[...]
```

Sometimes, APT doesn't dare to upgrade a package, usually because it will remove other packages. In those cases, you have to be explicit and install the package manually:

```
kali@kali:~$ sudo apt install kali-desktop-xfce
[...]
The following packages will be REMOVED:
  pipewire-media-session
The following packages will be upgraded:
  [...] kali-desktop-xfce [...]
Do you want to continue? [Y/n] n
```

In the example above, we can see that `pipewire-media-session` was blocking apt from upgrading `kali-desktop-xfce`. But after we asked to upgrade it explicitly (note that running "apt install" on a package already installed will in fact upgrade it), we now see what was the issue, and we can confirm that we're Ok with removing `pipewire-media-session`. That's enough to unblock the situation.

## A package needs a newer version, but there is no new version available

This will likely happen more often when using `kali-dev` or `kali-experimental`, and may happen for two reasons. The first is that the package is still being tested, and this will make its way into the repo soon. The second is that there are conflicts with some dependencies, and these will need to be resolved first.

To figure out which may be the case in your situation, you can look up the package's page on Debian's side or if it belongs to Kali see if there are any bug reports on [bugs.kali.org](https://bugs.kali.org/). Either way, the issue will be sorted out and will likely be resolved within a couple of weeks in most cases.

## A package is trying to overwrite a file and causing an error

This can occur when files move between packages and don't include the proper packaging field set to tell it that it is a replacing something else. In most cases this can be solved by retrying the installation or upgrade. In other cases, the APT command can have `-o dpkg::options::="--force-overwrite"` added in order to tell dpkg to ignore the overwrite error.

For extra information and a more detailed understanding of this error, you can read about it [here](https://raphaelhertzog.com/2011/08/01/understanding-dpkgs-file-overwrite-error/)
