---
title: Introduction to packaging step-by-step example
description:
icon:
type: post
weight: 11
author: ["gamb1t",]
---

# Instaloader

[Instaloader](https://github.com/instaloader/instaloader/) is a **Python 3** application with a single dependency (Python's `requests`). This makes it a relatively simple package, however not as straightforward as only packaging up a shell script would be. Because of the learning opportunities and simplicity, this makes it a good introduction package.

## GitHub Overview

The first thing we do is look at the application's [GitHub page](https://github.com/instaloader/instaloader/). A few things stand out which we take a note of:

![](instaloader-00.png)

What we notice here is some information that will come in handy later:

- The tool contains a `setup.py` script
- It has a release
- The license is MIT based

We'll be digging into each of these more later, for now it is just information to know.

## Setting Up The Environment

We will assume that we have already followed our [documentation on setting up a packing environment](/docs/development/setting-up-packaging-system/).

Let's set up our directories now for this package:

```console
kali@kali:~$ mkdir -p ~/kali/packages/instaloader/ ~/kali/upstream/
kali@kali:~$
```

Everything that relates to us building a package will be using `~/kali/`. In there will be two sub folders:

- `packages/` will be a source code of the package we are going to create
- `upstream/` will be a compressed file of the source code of the application _(ideally from a tag version release which we saw before)_

## Downloading Tag Releases

Because we are making a new package from scratch, we'll manually download the version of the tool we want to package up. If we were updating a package (and it was packaged correctly), there is a process to help speed it up. However, this will be covered in another guide.

Going to the [GitHub's release page](https://github.com/instaloader/instaloader/releases), we can see the latest version (which at the time of writing is `4.4.4`). Here is the option to download `instaloader-v4.4.4-windows-standalone.zip`, as well as `Source Code (zip)`, and  `Source Code (tar.gz)`. We are interested in the `tar.gz` option.

We will use `wget` and make sure to format its name appropriately according to Debian's standards for source packages (take note of `.orig.tar.gz`):

```console
kali@kali:~$ wget https://github.com/instaloader/instaloader/archive/v4.4.4.tar.gz -O ~/kali/upstream/instaloader_4.4.4.orig.tar.gz
kali@kali:~$
```

If there isn't a tag release for the software _(or it hasn't had an release in some time)_, we can use the latest git commit. This is covered in another guide. However, it is preferred to use a tag release when available.

## Creating Package Source Code

We need to switch paths to the working location of the package:

```console
kali@kali:~$ cd ~/kali/packages/instaloader/
kali@kali:~/kali/packages/instaloader$
```

We are now going to create a new blank git repository:

```console
kali@kali:~/kali/packages/instaloader$ git init
Initialized empty Git repository in /home/kali/kali/packages/instaloader/.git/
kali@kali:~/kali/packages/instaloader$
```

If we wanted to, we can confirm this by looking at "status" and "log":

```console
kali@kali:~/kali/packages/instaloader$ git status
On branch master

No commits yet

nothing to commit (create/copy files and use "git add" to track)
kali@kali:~/kali/packages/instaloader$
kali@kali:~/kali/packages/instaloader$ git log
fatal: your current branch 'master' does not have any commits yet
kali@kali:~/kali/packages/instaloader$
```

Great. Everything is empty; we have a clean working area.

We can now import the upstream version into our packing source code by using the file downloaded from `wget` before. Because of the filename format, `gbp` is able to detect the values `instaloader` as the package name, and `4.4.4` as the version. We just press enter to accept the default values:

```console
kali@kali:~/kali/packages/instaloader$ gbp import-orig ~/kali/upstream/instaloader_4.4.4.orig.tar.gz
What will be the source package name? [instaloader]
What is the upstream version? [4.4.4]
gbp:info: Importing '/home/kali/kali/upstream/instaloader_4.4.4.orig.tar.gz' to branch 'upstream'...
gbp:info: Source package is instaloader
gbp:info: Upstream version is 4.4.4
gbp:info: Successfully imported version 4.4.4 of /home/kali/kali/upstream/instaloader_4.4.4.orig.tar.gz
kali@kali:~/kali/packages/instaloader$
```

If we wanted to check everything is okay, once again, we can use git to do so:

```console
kali@kali:~/kali/packages/instaloader$ git status
On branch master
nothing to commit, working tree clean
kali@kali:~/kali/packages/instaloader$
kali@kali:~/kali/packages/instaloader$ git log
commit 494f71875f10f8d1da69a8edf2fc75300e4485b9 (HEAD -> master, tag: upstream/4.4.4, upstream)
Author: Joseph O'Gorman <gamb1t@kali.org>
Date:   Thu Jul 2 18:44:38 2020 -0400

    New upstream version 4.4.4
kali@kali:~/kali/packages/instaloader$
kali@kali:~/kali/packages/instaloader$ git branch -v
* master       494f718 New upstream version 4.4.4
  pristine-tar 439fe30 pristine-tar data for instaloader_4.4.4.orig.tar.gz
  upstream     494f718 New upstream version 4.4.4
kali@kali:~/kali/packages/instaloader$
```

So there is now an automatic commit created in the master branch _(which is the current active branch, shown by the `*`)_, as well as two other branches:

- `pristine-tar` which is metadata from the import
- `upstream` which is the source code of the application, without any of our package modifications

We are creating a Kali package, and we don't use the `master` branch, but rather `kali/master`. So let's switch:

```console
kali@kali:~/kali/packages/instaloader$ git checkout -b kali/master
Switched to a new branch 'kali/master'
kali@kali:~/kali/packages/instaloader$
kali@kali:~/kali/packages/instaloader$ git branch -D master
Deleted branch master (was 494f718).
kali@kali:~/kali/packages/instaloader$
kali@kali:~/kali/packages/instaloader$ git branch -v
* kali/master  494f718 New upstream version 4.4.4
  pristine-tar 439fe30 pristine-tar data for instaloader_4.4.4.orig.tar.gz
  upstream     494f718 New upstream version 4.4.4
kali@kali:~/kali/packages/instaloader$
```

Now we can generate the necessary files required to build a Debain-based package and also remove any example files created. During the process, we will be asked if its:

- `Single binary`
- `Arch-Independent`
- `Library`
- `Python`

We are going to keep it simple, and go with "**S**ingle". Then accept whats on the screen with `Y`. If you would like more information about when to use what option, please see the [manpage for dh_make](https://manpages.debian.org/jessie/dh-make/dh_make.8.en.html).:

```console
kali@kali:~/kali/packages/instaloader$ dh_make --file ~/kali/upstream/instaloader_4.4.4.orig.tar.gz -p instaloader_4.4.4
Type of package: (single, indep, library, python)
[s/i/l/p]?
Maintainer Name     : Joseph O'Gorman
Email-Address       : gamb1t@kali.org
Date                : Thu, 02 Jul 2020 17:59:47 -0400
Package Name        : instaloader
Version             : 4.4.4
License             : blank
Package Type        : single
Are the details correct? [Y/n/q]
Currently there is not top level Makefile. This may require additional tuning
Done. Please edit the files in the debian/ subdirectory now.
kali@kali:~/kali/packages/instaloader$
kali@kali:~/kali/packages/instaloader$ rm debian/*.docs debian/README* debian/*.ex debian/*.EX
kali@kali:~/kali/packages/instaloader$
```

We use `--file` to say where the `orig.tar.gz` file is. If the file was one directory back (`../`), this would not be needed, however as we have created a separate location for the file it is.

If you would like to see what got generated when using `dh_make`, we can use `git`:

```console
kali@kali:~/kali/packages/instaloader$ git status
On branch kali/master
Untracked files:
  (use "git add <file>..." to include in what will be committed)
        debian/

nothing added to commit but untracked files present (use "git add" to track)
kali@kali:~/kali/packages/instaloader$
kali@kali:~/kali/packages/instaloader$ ls -R debian/
debian/:
changelog  control  copyright  rules  source

debian/source:
format
kali@kali:~/kali/packages/instaloader$
```

A quick overview of each of those files:

- `changelog` - tracks when the package gets an update (including why and by who). This is responsible for the package version
- `control` - is the metadata for the package (often seen with `apt`)
- `copyright` - what is under what license. The package can be under something different to the work we have put in to create the package
- `rules` - how to install the package
- `debian/source` - is the source package format

We now need to edit most of these to make sure the information is accurate. We can use what we found on GitHub to supply the correct info into the `debian/` files:

- License
- Dependencies
- Maintainers
- Description

## Collecting Information

### License/Maintainers

For this package, its straight forward. GitHub has given us a helping hand, and detected the [license](https://github.com/instaloader/instaloader/blob/master/LICENSE) as MIT. We can also see there is a license file:

```console
kali@kali:~/kali/packages/instaloader$ cat LICENSE
The MIT License (MIT)

Copyright (c) 2016-2019 Alexander Graf and André Koch-Kramer.
...
kali@kali:~/kali/packages/instaloader$
```

Reading the license, we can see there are two authors which are given credit too: `Alexander Graf` and `André Koch-Kramer`. However, we don't have a method of contact for them. We continue to explore the rest of the git repository, looking for something which may give us more authors so we can give credit to them. There isn't a fixed structure in place, however there are some things to check and look out for:

- `README*` - authors may put contact information here
    - A few examples could be: `README`, `README.txt`, `README.MD` `README.MKDOCS`, or `Readme.txt`
- `AUTHOR*` - They may have a dedicated file for author information
- `CREDIT*` - They may have a dedicated file to who they give credit to
- `LICENSE*` - Like mentioned above, the license file may give author information
- `docs/` - They may place all their documentation in a separate folder
- The "main" starting point of the application may have comments at the top of the file - in this, `instaloader.py`
- Git commits - `git --no-pager log -s --format="%ae" | sort -u`

For our package, we can see:

```console
kali@kali:~/kali/packages/instaloader$ ls
AUTHORS.md  debian  deploy  docs  instaloader  instaloader.py  LICENSE  Pipfile  Pipfile.lock  README.rst  setup.py  test
kali@kali:~/kali/packages/instaloader$
kali@kali:~/kali/packages/instaloader$ ls docs/
as-module.rst    codesnippets      contributing.rst  installation.rst  logo.svg   requirements.txt             _templates
basic-usage.rst  codesnippets.rst  favicon.ico       logo_heading.png  Makefile   sphinx_autodoc_typehints.py  troubleshooting.rst
cli-options.rst  conf.py           index.rst         logo.png          README.md  _static
kali@kali:~/kali/packages/instaloader$
```

As it turns out, there is: `AUTHORS.md`, `docs/`, `instaloader.py`, and `README.rst`, so we have a few places to look at. Starting with `AUTHORS.md`, we can see the authors name and their method of contact:

```console
kali@kali:~/kali/packages/instaloader$ cat AUTHORS.md
Authors
=======

Instaloader is written by

- Alexander Graf (@aandergr)
- André Koch-Kramer (@Thammus)
- Lars Lindqvist (@e5150)
kali@kali:~/kali/packages/instaloader$
```

So rather than an email address, it appears to be a username (could be just for GitHub, or a generic Internet handle). This is enough for us to go forward (even though its not ideal).

Another trick we could try is looking to see if they used a "legit" email address with git:

```console
kali@kali:~/kali/packages/instaloader$ git clone https://github.com/instaloader/instaloader/ /tmp/instaloader
kali@kali:~/kali/packages/instaloader$
kali@kali:~/kali/packages/instaloader$ cd /tmp/instaloader/
kali@kali:/tmp/instaloader$ git --no-pager log -s --format="%ae" | sort -u | grep -v '@users.noreply.github.com'
...
kali@kali:/tmp/instaloader$
kali@kali:/tmp/instaloader$ cd ~/kali/packages/instaloader/
kali@kali:~/kali/packages/instaloader$
```

It doesn't appear so. Was worth a try!

### Dependencies/Maintainers

We need to see what is required to be installed on the machine in order for the application to work. Either pre-installed or will be installed using the application.

Some starting places to look at for this information:

- `README*`
- `SETUP*`
- `INSTALL*`
- `docs/`

There is a README for this application, but it just says how to install the application, rather than how to build it/compile from source:

```console
kali@kali:~/kali/packages/instaloader$ grep -C 3 -i install README.rst

::

    $ pip3 install instaloader

    $ instaloader profile [profile ...]

kali@kali:~/kali/packages/instaloader$
```

Exploring the [pip option](https://pypi.org/project/instaloader/) is something we could do, but out of scope for this guide.

Next we spot `setup.py`, which contains a lot of useful information:

```console
kali@kali:~/kali/packages/instaloader$ cat setup.py
#!/usr/bin/env python3
...
if sys.version_info < (3, 5):
    sys.exit('Instaloader requires Python >= 3.5.')

requirements = ['requests>=2.4']

if platform.system() == 'Windows' and sys.version_info < (3, 6):
    requirements.append('win_unicode_console')
...
    url='https://instaloader.github.io/',
    license='MIT',
    author='Alexander Graf, André Koch-Kramer',
    author_email='mail@agraf.me, koch-kramer@web.de',
    description='Download pictures (or videos) along with their captions and other metadata '
                'from Instagram.',
    long_description=open(os.path.join(SRC, 'README.rst')).read(),
    install_requires=requirements,
    python_requires='>=3.5',
...
kali@kali:~/kali/packages/instaloader$
```

We managed to get the following information from this:

- From the shebang, we can see its Python 3 (`#!/usr/bin/env python3`).
- We can see it wants Python 3.5 or higher
- We can see it wants `requests` and for it to be `v2.4` or higher
- We can see if its on Windows, it requires another dependency, but we are Linux, so not the case
- We can see the program's home URL
- We can see the license (MIT)
- We can see the authors and their email addresses
- We can get a description of the program

Handy!

When packing, we are building a standalone package, which needs to be able to install offline. Something else which needs to be kept in mind, other systems package management systems, such as Python's pip, or ruby's gems. Any of these dependencies also need to be in the main OS package management. In our case, we need Python's `requests`.

We have two ways of searching for it. We can use either:

- [pkg.kali.org](https://pkg.kali.org/)
- `apt-cache`

But we also need to know what we are searching for. There is a naming convention, but if you are un-sure, doing multiple searches may help:

- `requests`
- `python-requests`
- `python3-requests`

We will stick with the command line option for the time being.

Doing just `requests` gives a little too many results:

```console
kali@kali:~/kali/packages/instaloader$ apt-cache search requests | wc -l
561
kali@kali:~/kali/packages/instaloader$
```

So we need to do better to shorten the list, by just searching **the short version** of the description (we will cover this more later, but its the visible part of the output):

```console
kali@kali:~/kali/packages/instaloader$ apt-cache search --names-only requests | wc -l
19
kali@kali:~/kali/packages/instaloader$
kali@kali:~/kali/packages/instaloader$ apt-cache search --names-only python-requests
python-requests-cache-doc - persistent cache for requests library (doc)
python-requests-doc - elegant and simple HTTP library for Python (Documentation)
python-requests-mock-doc - mock out responses from the requests package - doc
python-requests-oauthlib-doc - module providing OAuthlib auth support for requests (Common Documentation)
python-requests-toolbelt-doc - Utility belt for python3-requests (documentation)
kali@kali:~/kali/packages/instaloader$
kali@kali:~/kali/packages/instaloader$ apt-cache search --names-only python-requests | grep -vi 'doc'
kali@kali:~/kali/packages/instaloader$
```

After removing the documentation from the results, we don't get any results. So on with the next search!:

```console
kali@kali:~/kali/packages/instaloader$ apt-cache search --names-only python3-requests
python3-requests - elegant and simple HTTP library for Python3, built for human beings
python3-requests-cache - persistent cache for requests library (Python 3)
python3-requests-file - File transport adapter for Requests - Python 3.X
python3-requests-futures - library for asynchronous HTTP requests (Python 3)
python3-requests-kerberos - Kerberos/GSSAPI authentication handler for python-requests - Python 3.x
python3-requests-mock - mock out responses from the requests package - Python 3.x
python3-requests-ntlm - Adds support for NTLM authentication to the requests library
python3-requests-oauthlib - module providing OAuthlib auth support for requests (Python 3)
python3-requests-toolbelt - Utility belt for advanced users of python3-requests
python3-requests-unixsocket - Use requests to talk HTTP via a UNIX domain socket - Python 3.x
python3-requestsexceptions - import exceptions from bundled packages in requests. - Python 3.x
kali@kali:~/kali/packages/instaloader$
```

The first result, `python3-requests`, looks exactly right! We can look closer:

```console
kali@kali:~/kali/packages/instaloader$ apt-cache show python3-requests
Package: python3-requests
Source: requests
Version: 2.23.0+dfsg-2
...
kali@kali:~/kali/packages/instaloader$
```

And we can see its version is `2.23.0`, which is higher than than `2.4`, so we don't need to update the package. This will be covered in another guide when required.

### Maintainers

While doing the other parts, we have discovered the authors and maintainers of the software, so we don't need to do anything extra for this.

### Description

There are two descriptions that we need to supply, a long description and a short description. When we look at the GitHub page we can see an about section that we can use for the short description. For the long description, we can use the description in the README.

![](instaloader-01.png)

We also have a value from the `setup.py`.

## Editing Package Source Code

Now that we have that information copied down, we can start to populate the files in the `debian/` folder we created with `dh_make`.

More information on the subject can be found on the [Debian documentation](https://www.debian.org/doc/manuals/maint-guide/dreq.en.html).

### Changelog

If we followed the documentation on [setting up a packaging environment](/docs/development/setting-up-packaging-system/), the only values we will need to alter would be **distribution** (from `unstable` to `kali-dev`), **version** (from `4.4.4-1` to `4.4.4-0kali1`) and the **log entry**:

```console
kali@kali:~/kali/packages/instaloader$ cat debian/changelog
instaloader (4.4.4-1) unstable; urgency=medium

  * Initial release (Closes: #nnnn)  <nnnn is the bug number of your ITP>

 -- Joseph O'Gorman <gamb1t@kali.org>  Thu, 02 Jul 2020 17:59:47 -0400
kali@kali:~/kali/packages/instaloader$
kali@kali:~/kali/packages/instaloader$ vim debian/changelog
kali@kali:~/kali/packages/instaloader$
kali@kali:~/kali/packages/instaloader$ cat debian/changelog
instaloader (4.4.4-0kali1) kali-dev; urgency=medium

  * Initial release

 -- Joseph O'Gorman <gamb1t@kali.org>  Thu, 02 Jul 2020 17:59:47 -0400
kali@kali:~/kali/packages/instaloader$
```

### Control

This file is the metadata for the package, and contains a lot of information.

More information on the subject can be found on the [Debian documentation](https://www.debian.org/doc/manuals/maint-guide/dreq.en.html).

Out of the box, it will look a little like this:

```console
kali@kali:~/kali/packages/instaloader$ cat debian/control
Source: instaloader
Section: unknown
Priority: optional
Maintainer: Joseph O'Gorman <gamb1t@kali.org>
Build-Depends: debhelper-compat (= 12)
Standards-Version: 4.5.0
Homepage: <insert the upstream URL, if relevant>
#Vcs-Browser: https://salsa.debian.org/debian/instaloader
#Vcs-Git: https://salsa.debian.org/debian/instaloader.git

Package: instaloader
Architecture: any
Depends: ${shlibs:Depends}, ${misc:Depends}
Description: <insert up to 60 chars description>
 <insert long description, indented with spaces>
kali@kali:~/kali/packages/instaloader$
```

So we can see a few things that need updating:

- `Section` - we set this to be misc, or if we know for sure it should be another section based off of the sections in [Debian testing](https://packages.debian.org/testing/) we can set it to that section
- `Maintainer` - we switch to be the Kali team, rather than an individual
- `Uploaders` - this is the individual(s) who are responsible for packaging up the application
- `Build-Depends` - what packages are required to BUILD the package
- `Homepage` - where is the tool located on the Internet
- `Vcs-Browser` - package source code to view online
- `Vcs-Git` - package source code location
- `Architecture` - what machines can this work on
- `Depends` - what other packages are required for this package to work
- `Description` - short and long description

Most of this we have now figured out from before, so it should make it easier to fill in. We went ahead and created a remote empty git repository on our GitLab account. In our example, this is the end result:

```console
kali@kali:~/kali/packages/instaloader$ vim debian/control
kali@kali:~/kali/packages/instaloader$
kali@kali:~/kali/packages/instaloader$ cat debian/control
Source: instaloader
Section: misc
Priority: optional
Maintainer: Kali Developers <devel@kali.org>
Uploaders: Joseph O'Gorman <gamb1t@kali.org>
Build-Depends: debhelper-compat (= 12),
               dh-python,
               python3-all,
               python3-setuptools,
               python3-requests
Standards-Version: 4.5.0
Homepage: https://instaloader.github.io/
Vcs-Browser: https://gitlab.com/kalilinux/packages/instaloader
Vcs-Git: https://gitlab.com/kalilinux/packages/instaloader.git

Package: instaloader
Architecture: all
Depends: ${python3:Depends},
         ${misc:Depends},
         python3-requests
Description: Download media along with their metadata from Instagram
 Downloads public and private profiles, hashtags, user stories, feeds
   and saved media
 Downloads comments, geotags and captions of each post.
 Automatically detects profile name changes and renames the target
   directory accordingly
 Allows fine-grained customization of filters and where to store
   downloaded media
kali@kali:~/kali/packages/instaloader$
```

NOTE: The `Build-Depends` & `Depends` are indented with tabs (and end with commas), but the `Description` is spaces.

There is a lot going on here, so lets point out a few things

Something to keep in mind with the formatting of the **long descriptions**, at about every 70 characters in (to the nearest whole word), we would put a new line, to help keep the formatting under control.

Now onto the dependencies, of which we have: **Build** & **Package**. For the build-dependencies of Python 3 we will have to have four things:

- `debhelper-compat`
- `dh-python`
- `python3-all`
- `python3-setuptools`

In a separate guide there will be an explanation as to why these are included, however only the first two are going to be a staple of Python 3 packaging as the latter two are for more specific cases.

In our application, we have another one, `python3-requests`, which we got from `setup.py` and that is a requirement from the application. Typically, if there was not a `setup.py` file, we would not need to include `python3-requests` in our "Build-Depends". However, due to the `setup.py` file, we will need to include `python3-requests` in both the "Build-Depends" as well as the package "Depends". This ensures these packages are always on the system when we install our package (especially handy when using "sbuild").

The `debhelper-compat` level determines how the package will be built. The higher the compat level, the newer the version. Newer versions have certain menial tasks done automatically, so this should not be lowered.

The package dependencies are relatively straightforward. We get rid of the `${shlibs:Depends}` as we are packaging up a Python tool, and instead replace it with the python3 depends version `${python3:Depends}`. We also ensure that `python3-requests` is included as the tool requires this. No other dependencies are needed by this tool, so we are done.

The final thing we need to ensure we change is the architecture from **any** to **all**, as this tool can be installed on all architectures.

### Copyright

Everything that gets created has an original author. They control what happens with it and it needs to be respected. We can call out this in the copyright file.

More information on the subject can be found on the [Debian documentation](https://www.debian.org/doc/manuals/maint-guide/dreq.en.html) and [here](https://www.debian.org/doc/packaging-manuals/copyright-format/1.0/).

Below is the skeleton template output (with comments removed):

```console
kali@kali:~/kali/packages/instaloader$ grep -v '#' debian/copyright
Format: https://www.debian.org/doc/packaging-manuals/copyright-format/1.0/
Upstream-Name: instaloader
Upstream-Contact: <preferred name and address to reach the upstream project>
Source: <url://example.com>

Files: *
Copyright: <years> <put author's name and email here>
           <years> <likewise for another author>
License: <special license>
 <Put the license of the package here indented by 1 space>
 <This follows the format of Description: lines in control file>
 .
 <Including paragraphs>

Files: debian/*
Copyright: 2020 Joseph O'Gorman <gamb1t@kali.org>
License: GPL-2+
 This package is free software; you can redistribute it and/or modify
 it under the terms of the GNU General Public License as published by
 the Free Software Foundation; either version 2 of the License, or
 (at your option) any later version.
 .
 This package is distributed in the hope that it will be useful,
 but WITHOUT ANY WARRANTY; without even the implied warranty of
 MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
 GNU General Public License for more details.
 .
 You should have received a copy of the GNU General Public License
 along with this program. If not, see <https://www.gnu.org/licenses/>
 .
 On Debian systems, the complete text of the GNU General
 Public License version 2 can be found in "/usr/share/common-licenses/GPL-2".

kali@kali:~/kali/packages/instaloader$
```

The original tool's author has ownership on their work, and the work we have put into creating the package belongs to us.
After updating it, it looks like the following:

```console
kali@kali:~/kali/packages/instaloader$ vim debian/copyright
kali@kali:~/kali/packages/instaloader$
kali@kali:~/kali/packages/instaloader$ cat debian/copyright
Format: https://www.debian.org/doc/packaging-manuals/copyright-format/1.0/
Upstream-Name: instaloader
Source: https://github.com/instaloader/instaloader

Files: *
Copyright: 2016-2020 Alexander Graf <mail@agraf.me>
           2016-2020 André Koch-Kramer <koch-kramer@web.de>
License: MIT

Files: debian/*
Copyright: 2020 Joseph O'Gorman <gamb1t@kali.org>
License: MIT

License: MIT
 The MIT License
 Permission is hereby granted, free of charge, to any person obtaining a copy
 of this software and associated documentation files (the "Software"), to deal
 in the Software without restriction, including without limitation the rights to
 use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies
 of the Software, and to permit persons to whom the Software is furnished to do
 so, subject to the following conditions:
 .
 The above copyright notice and this permission notice shall be included in all
 copies or substantial portions of the Software.
 .
 THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
 IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
 FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
 AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
 LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
 OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
 SOFTWARE.

kali@kali:~/kali/packages/instaloader$
```

We altered the following:

- We removed an optional parameter (`Upstream-Contact`), as that is touched on in the copyright file.
- We put in the homepage of the application to `Source`
- Put the two authors name and addresses from `setup.py`. The dates came from the `LICENSE` file
- Rather than putting the whole block of MIT license text directly after, we placed it towards the end of the file, and gave a header to it.
- We replaced the `GPL-2+` used as default for the packaging section with the **same** `MIT` license, which is used in the application. This is the standard for Debian packages (packaging work should match the application's license).

### Rules

This file is a Makefile, for building the Debian package.

More information on the subject can be found on the [Debian documentation](https://www.debian.org/doc/manuals/maint-guide/dreq.en.html).

The output of the template looks like the following:

```console
kali@kali:~/kali/packages/instaloader$ cat debian/rules
#!/usr/bin/make -f
# See debhelper(7) (uncomment to enable)
# output every command that modifies files on the build system.
#export DH_VERBOSE = 1


# see FEATURE AREAS in dpkg-buildflags(1)
#export DEB_BUILD_MAINT_OPTIONS = hardening=+all

# see ENVIRONMENT in dpkg-buildflags(1)
# package maintainers to append CFLAGS
#export DEB_CFLAGS_MAINT_APPEND  = -Wall -pedantic
# package maintainers to append LDFLAGS
#export DEB_LDFLAGS_MAINT_APPEND = -Wl,--as-needed


%:
  dh $@


# dh_make generated override targets
# This is example for Cmake (See https://bugs.debian.org/641051 )
#override_dh_auto_configure:
# dh_auto_configure -- #  -DCMAKE_LIBRARY_PATH=$(DEB_HOST_MULTIARCH)

kali@kali:~/kali/packages/instaloader$
```

So there are a lot of items which are pre-commented out, that may be handy for debugging & troubleshooting.
Other than the shebang (`#!/usr/bin/make -f`), there is only two other lines which are currently in use:

```plaintext
%:
  dh $@
```

Which is a wildcard (`%`), and feed in all the arguments into `dh`.

What needs to go here now starts to depend on the program and how complex it is. As our program is a python application we are going to have to tell it to build with `python3`. We also need to tell it to use `pybuild` to build, as we have a `setup.py` file included in the source of the application. If there was not a `setup.py` file, we would not add this flag. We also need to tell PyBuild the name of the application. This looks like:

```console
kali@kali:~/kali/packages/instaloader$ vim debian/rules
kali@kali:~/kali/packages/instaloader$
kali@kali:~/kali/packages/instaloader$ cat debian/rules
#!/usr/bin/make -f

#export DH_VERBOSE = 1
export PYBUILD_NAME=instaloader

%:
        dh $@ --with python3 --buildsystem=pybuild
kali@kali:~/kali/packages/instaloader$
```

NOTE: It uses TAB for indentation, as its a Makefile.

### Watch

An additional file which we highly recommend to include is a `watch` file. This points to upstream, and is then used to detect if there is a more recent version of the application than what is packaged. This is useful when doing updates to packages.

For more information, and example formats, please see the [Debian wiki](https://wiki.debian.org/debian/watch).
Using this wiki, we can see there is an example for GitHub which is where our project is stored - [github.com/instaloader/instaloader/](https://github.com/instaloader/instaloader/):

```plaintext
version=4
opts=filenamemangle=s/.+\/v?(\d\S+)\.tar\.gz/<project>-$1\.tar\.gz/ \
  https://github.com/<user>/<project>/tags .*/v?(\d\S+)\.tar\.gz
```

So lets now alter it to fit our needs:

```console
kali@kali:~/kali/packages/instaloader$ vim debian/watch
kali@kali:~/kali/packages/instaloader$
kali@kali:~/kali/packages/instaloader$ cat debian/watch
version=4
opts=filenamemangle=s/.+\/v?(\d\S+)\.tar\.gz/instaloader-$1\.tar\.gz/ \
  https://github.com/instaloader/instaloader/tags .*/v?(\d\S+)\.tar\.gz
kali@kali:~/kali/packages/instaloader$
```

NOTE: This has two spaces for any indentation

So let's do a quick check to see if its working right:

```console
kali@kali:~/kali/packages/instaloader$ uscan -vv --no-download
...
uscan info: Found the following matching hrefs on the web page (newest first):
   /instaloader/instaloader/archive/v4.4.4rc3.tar.gz (4.4.4rc3) index=4.4.4rc3-1
   /instaloader/instaloader/archive/v4.4.4rc2.tar.gz (4.4.4rc2) index=4.4.4rc2-1
   /instaloader/instaloader/archive/v4.4.4rc1.tar.gz (4.4.4rc1) index=4.4.4rc1-1
   /instaloader/instaloader/archive/v4.4.4.tar.gz (4.4.4) index=4.4.4-1
   /instaloader/instaloader/archive/v4.4.3.tar.gz (4.4.3) index=4.4.3-1
...
    $newversion  = 4.4.4rc3
    $lastversion = 4.4.4
...
uscan: Newest version of instaloader on remote site is 4.4.4rc3, local version is 4.4.4
uscan:    => Newer package available from
      https://github.com/instaloader/instaloader/archive/v4.4.4rc3.tar.gz
uscan info: Scan finished
kali@kali:~/kali/packages/instaloader$
```

Looks like its not! Its correctly detected all the versions, but its not sorted the order correctly (due to the release candidate). We know this by going to the [release page](https://github.com/instaloader/instaloader/tags):

![](instaloader-02.png)

Looking back at the [Debian wiki](https://wiki.debian.org/debian/watch), there is a section called [Common mistakes](https://wiki.debian.org/debian/watch#Common_mistakes):

> Not mangling upstream versions that are alphas, betas or release candidates to make them sort before the final release. The solution is to use "uversionmangle" like this:

```plaintext
opts=uversionmangle=s/(\d)[_\.\-\+]?((RC|rc|pre|dev|beta|alpha)\d*)$/$1~$2/
```

However, we need to edit it a bit to fit Instaloader. This can be figured out through trial and error using the above uversionmangle as the base.

Let's see how it works:

```console
kali@kali:~/kali/packages/instaloader$ vim debian/watch
kali@kali:~/kali/packages/instaloader$
kali@kali:~/kali/packages/instaloader$ cat debian/watch
version=4
opts=uversionmangle=s/(\d)[_\.\-\+]?((RC|rc|pre|dev|beta|alpha|a)\d*)$// \
  https://github.com/instaloader/instaloader/tags .*/v?(\d\S+)\.tar\.gz
kali@kali:~/kali/packages/instaloader$
kali@kali:~/kali/packages/instaloader$ uscan -vv --no-download
...
uscan info: Newest version of instaloader on remote site is 4.4.4, local version is 4.4.4
uscan info:    => Package is up to date for from
      https://github.com/instaloader/instaloader/archive/v4.4.4.tar.gz
uscan info: Scan finished
kali@kali:~/kali/packages/instaloader$
```

Success!

## .Install & Helper-Scripts

Everything we have done so far would just be for building the package, but we haven't said how to install the application.

```console
kali@kali:~/kali/packages/instaloader$ vim debian/instaloader.install
kali@kali:~/kali/packages/instaloader$
kali@kali:~/kali/packages/instaloader$ cat debian/instaloader.install
instaloader.py usr/share/instaloader/
instaloader usr/share/instaloader/
kali@kali:~/kali/packages/instaloader$
```

NOTE: There is no leading slash in the target directory

We can go forward with this, but it may not behave like we were expecting. This is because we don't have anything in `$PATH`, so if we went to the command line and tried typing in `instaloader.py` its not going to work (also it has the file extension, `.py`). The solution is to create a **helper-script**, which is placed into `$PATH` (and we include in the `.install` file):

```console
kali@kali:~/kali/packages/instaloader$ mkdir -p debian/helper-script/
kali@kali:~/kali/packages/instaloader$
kali@kali:~/kali/packages/instaloader$ vim debian/helper-script/instaloader
kali@kali:~/kali/packages/instaloader$
kali@kali:~/kali/packages/instaloader$ cat debian/helper-script/instaloader
#!/bin/sh
exec python3 /usr/share/instaloader/instaloader.py "$@"
kali@kali:~/kali/packages/instaloader$
kali@kali:~/kali/packages/instaloader$ vim debian/instaloader.install
kali@kali:~/kali/packages/instaloader$
kali@kali:~/kali/packages/instaloader$ cat debian/instaloader.install
instaloader.py usr/share/instaloader/
instaloader usr/share/instaloader/
debian/helper-script/instaloader usr/bin/
kali@kali:~/kali/packages/instaloader$
```

With that, all the necessary `debian/` files are added. Time to build!

## Packing Up

Time to bundle everything into a file. We are going to use **sbuild** to create the package. This has its pros and cons. One of the pros is that if it builds here, it will also build elsewhere as its meant for build daemons. The down side is, it will require access to a network repository as it will try and handle detecting and installing any dependencies missing in the chroot, making it slower to build.

If you don't want to use **sbuild**, just drop it from the arguments (e.g. `gbp buildpackage`), However, you will be required then to install whats in `debian/control` in the `Build-Depends` section (e..g `sudo apt install -y dh-python python3-all python3-setuptools python3-requests`).

So lets give sbuild a try:

```console
kali@kali:~/kali/packages/instaloader$ gbp buildpackage --git-builder=sbuild
gbp:error: Can't determine package type: Failed to read changelog: can't get HEAD:debian/changelog: fatal: path 'debian/changelog' exists on disk, but not in 'HEAD'
kali@kali:~/kali/packages/instaloader$
```

Oops! We haven't committed our changes to git. Shame on us.

If we wanted to, we could bypass this by doing `gbp buildpackage --git-builder=sbuild --git-export=WC`, which would allow us to test out our values in `debian/` before commiting to it, rather than cluttering up the git history with various debugging/troubleshooting commits. Then when we have our package in a working state, we can then commit to git, and try again, like so:

```console
kali@kali:~/kali/packages/instaloader$ git status
On branch kali/master
Untracked files:
  (use "git add <file>..." to include in what will be committed)
        debian/

nothing added to commit but untracked files present (use "git add" to track)
kali@kali:~/kali/packages/instaloader$
kali@kali:~/kali/packages/instaloader$ git add debian/
kali@kali:~/kali/packages/instaloader$
kali@kali:~/kali/packages/instaloader$ git commit -m "Initial release"
[kali/master 10a9e96] Add debian/ files
 8 files changed, 94 insertions(+)
 create mode 100644 debian/changelog
 create mode 100644 debian/control
 create mode 100644 debian/copyright
 create mode 100755 debian/helper-script/instaloader
 create mode 100644 debian/instaloader.install
 create mode 100755 debian/rules
 create mode 100644 debian/source/format
 create mode 100644 debian/watch
kali@kali:~/kali/packages/instaloader$
```

Let's try and build again:

NOTE: You may not get the following error (as it depends on how "clean" your OS is):

```console
kali@kali:~/kali/packages/instaloader$ gbp buildpackage --git-builder=sbuild
gbp:info: Exporting 'HEAD' to '/home/kali/kali/build-area/instaloader-tmp'
gbp:info: Moving '/home/kali/kali/build-area/instaloader-tmp' to '/home/kali/kali/build-area/instaloader-4.4.4'
gbp:info: Performing the build
dh clean --with python3 --buildsystem=pybuild
dh: error: unable to load addon python3: Can't locate Debian/Debhelper/Sequence/python3.pm in @INC (you may need to install the Debian::Debhelper::Sequence::python3 module) (@INC contains: /etc/perl /usr/local/lib/x86_64-linux-gnu/perl/5.30.3 /usr/local/share/perl/5.30.3 /usr/lib/x86_64-linux-gnu/perl5/5.30 /usr/share/perl5 /usr/lib/x86_64-linux-gnu/perl-base /usr/lib/x86_64-linux-gnu/perl/5.30 /usr/share/perl/5.30 /usr/local/lib/site_perl) at (eval 25) line 1.
BEGIN failed--compilation aborted at (eval 25) line 1.
make: *** [debian/rules:7: clean] Error 255
E: Failed to clean source directory /home/kali/kali/build-area/instaloader-4.4.4 (/home/kali/kali/build-area/instaloader_4.4.4-0kali1.dsc)
gbp:error: 'sbuild' failed: it exited with 1
kali@kali:~/kali/packages/instaloader$
```

If you see the above error, this is because `dh-python` is missing from our OS. We can quickly fix this by doing:

```console
kali@kali:~/kali/packages/instaloader$ sudo apt install -y dh-python
kali@kali:~/kali/packages/instaloader$
```

So, one more try and building:

```console
kali@kali:~/kali/packages/instaloader$ gbp buildpackage --git-builder=sbuild
gbp:info: Exporting 'HEAD' to '/home/kali/kali/build-area/instaloader-tmp'
gbp:info: Moving '/home/kali/kali/build-area/instaloader-tmp' to '/home/kali/kali/build-area/instaloader-4.4.4'
gbp:info: Performing the build
dh clean --with python3 --buildsystem=pybuild
...

+------------------------------------------------------------------------------+
| Package contents                                                             |
+------------------------------------------------------------------------------+

...

Install lintian build dependencies (apt-based resolver)
-------------------------------------------------------

...

E: instaloader source: source-is-missing docs/_static/bootstrap-4.1.3.bundle.min.js
W: instaloader: binary-without-manpage usr/bin/instaloader
I: instaloader source: testsuite-autopkgtest-missing

I: Lintian run was successful.
...

+------------------------------------------------------------------------------+
| Summary                                                                      |
+------------------------------------------------------------------------------+

Build Architecture: amd64
Build Type: full
Build-Space: 2460
Build-Time: 5
Distribution: kali-dev
Host Architecture: amd64
Install-Time: 37
Job: /home/kali/kali/build-area/instaloader_4.4.4-0kali1.dsc
Lintian: warn
Machine Architecture: amd64
Package: instaloader
Package-Time: 45
Source-Version: 4.4.4-0kali1
Space: 2460
Status: successful
Version: 4.4.4-0kali1
--------------------------------------------------------------------------------
Finished at 2020-07-03T15:51:09Z
Build needed 00:00:45, 2460k disk space

kali@kali:~/kali/packages/instaloader$
```

The output here is very long, so we have truncated it, but we can see its being built successfully. Even with an error, warning, and information from `lintian`!

Let's double check to see what got created:

```console
kali@kali:~/kali/packages/instaloader$ ls ~/kali/build-area/instaloader*
/home/kali/kali/build-area/instaloader_4.4.4-0kali1_all.deb          /home/kali/kali/build-area/instaloader_4.4.4-0kali1.debian.tar.xz
/home/kali/kali/build-area/instaloader_4.4.4-0kali1_amd64.build      /home/kali/kali/build-area/instaloader_4.4.4-0kali1.dsc
/home/kali/kali/build-area/instaloader_4.4.4-0kali1_amd64.buildinfo  /home/kali/kali/build-area/instaloader_4.4.4.orig.tar.gz
/home/kali/kali/build-area/instaloader_4.4.4-0kali1_amd64.changes
kali@kali:~/kali/packages/instaloader$
```

We have output!

## Making Lintian Happy

For more information, see [Debian's documentation](https://www.debian.org/doc/manuals/maint-guide/checkit.en.html).

To fix this error `E: instaloader source: source-is-missing docs/_static/bootstrap-4.1.3.bundle.min.js`, which will make sure the package is a higher standard, we can resolve this by expanding the minified JavaScript (Beautifier/Unminify) file and put it into a new directory in `debian/` called `missing-sources`:

```console
kali@kali:~/kali/packages/instaloader$ apt-cache search beautifier js
...
jsbeautifier - JavaScript unobfuscator and beautifier
...
kali@kali:~/kali/packages/instaloader$
kali@kali:~/kali/packages/instaloader$ sudo apt install -y jsbeautifier
kali@kali:~/kali/packages/instaloader$
kali@kali:~/kali/packages/instaloader$ mkdir -p debian/missing-sources/
kali@kali:~/kali/packages/instaloader$
kali@kali:~/kali/packages/instaloader$ js-beautify docs/_static/bootstrap-4.1.3.bundle.min.js > debian/missing-sources/bootstrap-4.1.3.bundle.js
kali@kali:~/kali/packages/instaloader$
kali@kali:~/kali/packages/instaloader$ git add debian/missing-sources/
kali@kali:~/kali/packages/instaloader$
kali@kali:~/kali/packages/instaloader$ git commit -m "Unminify JS to make lintian happy"
kali@kali:~/kali/packages/instaloader$
```

We can now rebuild the package with the same command and see that it was successful with no error

```console
kali@kali:~/kali/packages/instaloader$ gbp buildpackage --git-builder=sbuild
...

+------------------------------------------------------------------------------+
| Package contents                                                             |
+------------------------------------------------------------------------------+

...

Install lintian build dependencies (apt-based resolver)
-------------------------------------------------------

...

W: instaloader: binary-without-manpage usr/bin/instaloader
I: instaloader source: testsuite-autopkgtest-missing

I: Lintian run was successful.
...
kali@kali:~/kali/packages/instaloader$
```

A way we can improve this package is to automate this stage by unminify the JavaScript file, but this will be covered in another guide.

## Manual Install

Let's now give our package a test drive:

```console
kali@kali:~/kali/packages/instaloader$ sudo apt install ~/kali/build-area/instaloader_4.4.4-0kali1_all.deb
Selecting previously unselected package instaloader.
(Reading database ... 154513 files and directories currently installed.)
Preparing to unpack .../instaloader_4.4.4-0kali1_all.deb ...
Unpacking instaloader (4.4.4-0kali1) ...
Setting up instaloader (4.4.4-0kali1) ...
Processing triggers for kali-menu (2020.3.0) ...
kali@kali:~/kali/packages/instaloader$
kali@kali:~/kali/packages/instaloader$ instaloader
usage:
instaloader.py [--comments] [--geotags]
               [--stories] [--highlights] [--tagged] [--igtv]
               [--login YOUR-USERNAME] [--fast-update]
               profile | "#hashtag" | %%location_id | :stories | :feed | :saved
instaloader.py --help
kali@kali:~/kali/packages/instaloader$
```

Success!

This is looking good (not perfect), and eagle eye spotters may be able to spot why (in the output) - there is the file extension in the output (`instaloader.py`), yet the command used to call it doesn't have it (`instaloader`).
We are going to need to either patch the application or find a different way to call the application to address this. But this will be covered in another guide.

## Save & Share

Let's now make sure everything is in git locally, before we push it out to the remote repository (the one we defined in `debian/control`):

```console
kali@kali:~/kali/packages/instaloader$ git status
On branch kali/master
nothing to commit, working tree clean
kali@kali:~/kali/packages/instaloader$
kali@kali:~/kali/packages/instaloader$ git branch -v
* kali/master  10a9e96 Unminify JS to make lintian happy
  pristine-tar 439fe30 pristine-tar data for instaloader_4.4.4.orig.tar.gz
  upstream     494f718 New upstream version 4.4.4
kali@kali:~/kali/packages/instaloader$
kali@kali:~/kali/packages/instaloader$ git remote -v
kali@kali:~/kali/packages/instaloader$
```

We don't (yet) have a remote repository setup, so we go to GitLab and [create a new project](https://gitlab.com/projects/new) before continuing.

Afterwards:

```console
kali@kali:~/kali/packages/instaloader$ git remote add origin git@gitlab.com:kalilinux/packages/instaloader.git
kali@kali:~/kali/packages/instaloader$
kali@kali:~/kali/packages/instaloader$ git remote -v
origin  git@gitlab.com:kalilinux/packages/instaloader.git (fetch)
origin  git@gitlab.com:kalilinux/packages/instaloader.git (push)
kali@kali:~/kali/packages/instaloader$
```

Now we need to send our local work to the new remote repository (and don't forget the tags):

```console
kali@kali:~/kali/packages/instaloader$ git push --all
Enumerating objects: 95, done.
Counting objects: 100% (95/95), done.
Delta compression using up to 2 threads
Compressing objects: 100% (88/88), done.
Writing objects: 100% (95/95), 291.16 KiB | 7.46 MiB/s, done.
Total 95 (delta 1), reused 0 (delta 0), pack-reused 0
remote:
remote: To create a merge request for pristine-tar, visit:
remote:   https://gitlab.com/kalilinux/packages/instaloader/-/merge_requests/new?merge_request%5Bsource_branch%5D=pristine-tar
remote:
remote: To create a merge request for upstream, visit:
remote:   https://gitlab.com/kalilinux/packages/instaloader/-/merge_requests/new?merge_request%5Bsource_branch%5D=upstream
remote:
To gitlab.com:kalilinux/packages/instaloader.git
 * [new branch]      kali/master -> kali/master
 * [new branch]      pristine-tar -> pristine-tar
 * [new branch]      upstream -> upstream
kali@kali:~/kali/packages/instaloader$
kali@kali:~/kali/packages/instaloader$ git push --tags
Enumerating objects: 1, done.
Counting objects: 100% (1/1), done.
Writing objects: 100% (1/1), 172 bytes | 172.00 KiB/s, done.
Total 1 (delta 0), reused 0 (delta 0), pack-reused 0
To gitlab.com:kalilinux/packages/instaloader.git
 * [new tag]         upstream/4.4.4 -> upstream/4.4.4
kali@kali:~/kali/packages/instaloader$
```

At this point now, a ticket can be opened up on the [Kali Linux bug tracker](https://bugs.kali.org/), with a suggestion of the tool & package which you have created, and our tool team will handle it from there.
