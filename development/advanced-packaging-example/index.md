---
title: Advanced Packaging Step-By-Step Example (FinalRecon & Python-icmplib)
description:
icon:
weight: 13
author: ["gamb1t", "g0tmi1k",]
---

_This guide is accurate at the time of writing. As it references a lot of external resources out of our control, items may be different over time (as software gets updated)._

**[FinalRecon](https://github.com/thewhiteh4t/FinalRecon)** is a **Python 3** application with multiple Python dependencies. At the time of writing, one of the dependencies (**[python3-icmplib](https://github.com/ValentinBELYN/icmplib)**) is not in the Kali Linux repository. In this guide we will have to learn how to follow dependency chains, and fix anything required to ensure that the end package can be included. We will also create a patch, helper-script, as well as a [runtime test](/docs/development/contributing-runtime-tests/) for the package.

We will assume we have already followed our [documentation on setting up a packaging environment](/docs/development/setting-up-packaging-system/) as well as our previous other packaging guides [#1 (Instaloader)](/docs/development/intro-to-packaging-example/) & [#2 (Photon)](/docs/development/intermediate-packaging-example/) as this will explain their contents.

## FinalRecon Code Overview

The first action we will take, will be to look at [FinalRecon's source code](https://github.com/thewhiteh4t/FinalRecon) to see what information we can acquire. Using this, we notice the following:

- It has [no tag release](https://github.com/thewhiteh4t/FinalRecon/releases)
- The [MIT license file](https://github.com/thewhiteh4t/FinalRecon/blob/0d41eb61a023c1ae467ede8653d37cc847695f01/LICENSE)
- There is no `setup.py` file (which is used for [setuptools](https://packaging.python.org/tutorials/packaging-projects/))
- There is a `requirements.txt` [file](https://github.com/thewhiteh4t/FinalRecon/blob/0d41eb61a023c1ae467ede8653d37cc847695f01/requirements.txt) (which is used for [pip](https://pip.pypa.io/en/stable/user_guide/#requirements-files))
- Various descriptions about the tool & usage guides
- Various external links _(if any additional research is required)_

### Missing Tag Releases

As FinalRecon does [not have a tag release](https://github.com/thewhiteh4t/FinalRecon/releases)) we will have to create our own upstream tar file. Looking to see what branches there are, we discover there is just one (there isn't a stable/production one, or is there a beta/deployment/staging one). As a result, we will use whatever is the latest commit on the main branch until the author does a tag release. _We can auto open up an issue request and/or email them seeing if they will response to such an act_.

### License

This package has been detected as having a **[MIT](https://opensource.org/licenses/MIT)** license by **[GitHub](https://github.com/thewhiteh4t/FinalRecon)**. If we look at the specific [license file](https://github.com/thewhiteh4t/FinalRecon/blob/0d41eb61a023c1ae467ede8653d37cc847695f01/LICENSE) we can see that there is not a lot to copy, so we will be copying this exactly as-is. Unfortunately, though we have found a maintainer we have not found any contact information yet. We will have to continue searching for contact information.

### Dependencies

As there is a `requirements.txt` [file](https://github.com/thewhiteh4t/FinalRecon/blob/0d41eb61a023c1ae467ede8653d37cc847695f01/requirements.txt) (which is used for Python's [pip](https://pip.pypa.io/en/stable/user_guide/#requirements-files) to install any **Python dependencies** that are required for this tool to work), we will need check to see what's needed.

### Description(s)

We will once again pull our description from [FinalRecon's GitHub](https://github.com/thewhiteh4t/FinalRecon).

For the **short description** we will use a modified version of the first line in the [README](https://github.com/thewhiteh4t/FinalRecon/blob/0d41eb61a023c1ae467ede8653d37cc847695f01/README.md), "A fast and simple python script for web reconnaissance."

For the **long description** we will also use a modified version of the first line in the [README](https://github.com/thewhiteh4t/FinalRecon/blob/0d41eb61a023c1ae467ede8653d37cc847695f01/README.md), however we will expand this time, "A fast and simple python script for web reconnaissance that follows a modular structure and provides detailed information on various areas."

### Maintainer(s)

If we were to look all over the GitHub we would not find an email address. We could look in `git log` and view the email addresses associated, however these do not seem to be solid as there are multiple for "thewhiteh4t" _(at least 3)_. Instead, we do more digging. We notice that there is a YouTube video demo linked in the `README.md`, if we go to the YouTube channel's [about page](https://www.youtube.com/c/thewhiteh4t/about) we can **view an email address** for business inquiries (which does not match to any in the git log). This will be a good choice to use as the contact information.
With that said all said, not having a contact information is not an essential part , so if we were unable to find one we could still continue to package.

## Tag Releases

Having a "tagged" release, is preferred when doing Debian packaging. When handling anything "upstream", having a newer tag release, will feed back into the packaging eco-system to signal there is a newer version available (thus the package should be updated).

It is possible to do it without tag releases. Such as using version-control systems (e.g. git), but it is not preferred. There are various reasons why:

- Different coding styles
  - Some people will use a single branch, and develop/test code here
- End users often want something that is "stable", and which has been fully tested signed off
  - Frequent minor updates
- Based on the filename, only the date is used (`YYYYMMDD`). Time is not used (`HHMMSS`)
  - If there are multiple commits in a single day, may get inconsistencies

### Creating a Compressed Tar File

As FinalRecon does [not have a tag release](https://github.com/thewhiteh4t/FinalRecon/releases) we will have to create our own upstream tar file. We will use whatever is the latest on the main branch and this will be imported into our package. We have two ways of doing it, either by:

- **Creating a [Git archive](https://manpages.debian.org/experimental/git-man/git-archive.1.en.html)**
- **wget** to download a snapshot of the latest commit

As we are manually creating the upstream file, we need to make sure to give it the right filename to use _(to match Debian standards)_, as it will help `gbp` later on. We need to make sure to name the file using the following format `<packagename>_0~<date-YYYYMMDD>.orig.tar.gz`:

- `<packagename>` is hopefully what will be detected in `gbp` for the package name. This should be the name of the tool
- `_` is the separator between name and version.
- `0~` is handy to have for if/when upstream does a "tagged release", making their version greater than ours, making it an easy update path (as the date format is a really large number)
- `<date-YYYYMMDD>` is the date format stamp: "YearYearYearYearMonthMonthDayDay"
- `.orig` is to show its untouched original upstream source code
- `.tar.gz` is a compressed file

We will do the long way by doing using Git to pull down the latest source code from [FinalRecon's GitHub's page](https://github.com/thewhiteh4t/FinalRecon), and create a compressed archive afterwards.

```console
kali@kali:~$ git clone https://github.com/thewhiteh4t/FinalRecon ~/kali/upstream/finalrecon/
...
kali@kali:~$
kali@kali:~$ cd ~/kali/upstream/finalrecon/
kali@kali:~/kali/upstream/finalrecon$
kali@kali:~/kali/upstream/finalrecon$ git archive --format=tar master \
  | gzip -c \
  > ~/kali/upstream/finalrecon_0~$( date '+%Y%m%d' ).orig.tar.gz
kali@kali:~/kali/upstream/finalrecon$
kali@kali:~/kali/upstream/finalrecon$ ls -l ~/kali/upstream/finalrecon_*.orig.tar.gz
-rw-r--r-- 1 kali kali 101K Nov  8 07:07 /home/kali/kali/upstream/finalrecon_0~20201108.orig.tar.gz
kali@kali:~/kali/upstream/finalrecon$
kali@kali:~/kali/upstream/finalrecon$ file ~/kali/upstream/finalrecon_*.orig.tar.gz
/home/kali/kali/upstream/finalrecon_0~20201108.orig.tar.gz: gzip compressed data, from Unix, original size modulo 2^32 317440
kali@kali:~/kali/upstream/finalrecon$
```

{{% notice info %}}
The `wget` method would be to get the URL from GitHub and then run the following command.

```console
kali@kali:~$ mkdir -p ~/kali/upstream/
kali@kali:~$
kali@kali:~$ wget \
  -O ~/kali/upstream/finalrecon_0~$( date '+%Y%m%d' ).orig.tar.gz \
  https://github.com/thewhiteh4t/FinalRecon/archive/master.tar.gz
...
kali@kali:~$
```

GitHub UI changes over time. Currently you can find the URL by click in the green "Code" button, "HTTPS", and using "Download ZIP". It is a undocumented feature, but you can download as a tar.gz instead, by replacing the requested filename.
{{% /notice %}}

## Creating Package Source Code

Now that the prerequisites are done, we can go to the working directory of where we will be making this package and create an **empty Git repository**. This is where we will house our packaging work, and save anything we make.

```console
kali@kali:~/kali/upstream/finalrecon$ mkdir -p ~/kali/packages/finalrecon/
kali@kali:~/kali/upstream/finalrecon$
kali@kali:~/kali/upstream/finalrecon$ cd ~/kali/packages/finalrecon/
kali@kali:~/kali/packages/finalrecon$
kali@kali:~/kali/packages/finalrecon$ git init
Initialized empty Git repository in /home/kali/packages/finalrecon/.git/
kali@kali:~/kali/packages/finalrecon$
kali@kali:~/kali/packages/finalrecon$ git status
On branch master

No commits yet

nothing to commit (create/copy files and use "git add" to track)
kali@kali:~/kali/packages/finalrecon$
```

- - -

### Importing Upstream Package

We can now **import the upstream `.tar.gz`** file we previously created into the empty Git repository we just made.

When prompted, we accept any default values _(or use the flag `--no-interactive`)_.

```console
kali@kali:~/kali/packages/finalrecon$ gbp import-orig ~/kali/upstream/finalrecon_*.orig.tar.gz --debian-branch=kali/master
What will be the source package name? [finalrecon]
What is the upstream version? [0~20201108]
gbp:info: Importing '../finalrecon_0~20201108.orig.tar.gz' to branch 'upstream'...
gbp:info: Source package is finalrecon
gbp:info: Upstream version is 0~20201108
gbp:info: Successfully imported version 0~20201108 of ../finalrecon_0~20201108.orig.tar.gz
kali@kali:~/kali/packages/finalrecon$
kali@kali:~/kali/packages/finalrecon$ git status
On branch kali/master
nothing to commit, working tree clean
kali@kali:~/kali/packages/finalrecon$
kali@kali:~/kali/packages/finalrecon$ git branch -v
* kali/master  f1c4c9f New upstream version 0~20201108
  pristine-tar 69f689f pristine-tar data for finalrecon_0~20201108.orig.tar.gz
  upstream     f1c4c9f New upstream version 0~20201108
kali@kali:~/kali/packages/finalrecon$
```

- - -

We can now **generate the `debian/` folder** with its related files. We will manually specify the upstream `.tar.gz` file _(as it is not located in `../`, but instead `~/kali/upstream/`)_. We will also set the package name to use in the same naming convention as before _(`<packagename>_<date-YYYYMMDD>` as is Debian standards)_.

Afterwards we will remove any example files that get automatically generated, as they are not used.

```console
kali@kali:~/kali/packages/finalrecon$ dh_make --file ~/kali/upstream/finalrecon_*.orig.tar.gz -p finalrecon_0~$( date '+%Y%m%d' ) --single -y
Maintainer Name     : Joseph O'Gorman
Email-Address       : gamb1t@kali.org
Date                : Sun, 08 Nov 2020 07:09:41 +0000
Package Name        : finalrecon
Version             : 0~20201108
License             : blank
Package Type        : single
Currently there is not top level Makefile. This may require additional tuning
Done. Please edit the files in the debian/ subdirectory now.

kali@kali:~/kali/packages/finalrecon$
kali@kali:~/kali/packages/finalrecon$ rm -f debian/*.docs debian/README* debian/*.ex debian/*.EX
kali@kali:~/kali/packages/finalrecon$
kali@kali:~/kali/packages/finalrecon$ git status
On branch kali/master
Untracked files:
  (use "git add <file>..." to include in what will be committed)
        debian/

nothing added to commit but untracked files present (use "git add" to track)
kali@kali:~/kali/packages/finalrecon$
kali@kali:~/kali/packages/finalrecon$ ls debian/
changelog  control  copyright  rules  source
kali@kali:~/kali/packages/finalrecon$
```

- - -

We can now start to populate the files in the `debian/` folder to make sure the information is accurate. We can use what we found from before on [FinalRecon's GitHub](https://github.com/thewhiteh4t/FinalRecon) to supply the correct information. To recap, we need to make sure we got the following bits of information to locate:

- Dependencies
- Description
- License
- Maintainers

## FinalRecon (Pip) Dependencies

As there is a `requirements.txt` [file](https://github.com/thewhiteh4t/FinalRecon/blob/0d41eb61a023c1ae467ede8653d37cc847695f01/requirements.txt) (which is used for Python's [pip](https://pip.pypa.io/en/stable/user_guide/#requirements-files) to install any **Python dependencies** that are required for this tool to work), we will need check to see what's needed.

For this tool to work, it requires additional software to be installed, aka dependencies. Depending on how the tool is coded, will depend on what is required (or only recommended) to be installed. FinalRecon is using various Python libraries and does not call any system commands.

In Python's eco-system, there is [pip](https://pip.pypa.io/en/stable/user_guide/#requirements-files). This is Python's package manager, which can be used to download and install any Python libraries. However, we are trying to build a package for Debian package management instead. As a result, any Python libraries need to be ported over to Debian format, in order for our package to use them (so the OS can track any files, allowing for cleaner upgrades and un-installs of packages). Lets start out by looking to see what is needed outside of the standard values, for this tool to work.

```console
kali@kali:~/kali/packages/finalrecon$ cat requirements.txt
requests
ipwhois
bs4
lxml
dnslib
aiohttp
aiodns
psycopg2
tldextract
icmplib
kali@kali:~/kali/packages/finalrecon$
```

- - -

We then try to search for each dependency from `requirements.txt` in `apt-cache`, to make sure that we have everything in Kali Linux' repository.

```console
kali@kali:~/kali/packages/finalrecon$ sudo apt update
kali@kali:~/kali/packages/finalrecon$
kali@kali:~/kali/packages/finalrecon$ apt-cache search ipwhois | grep -i python3
python3-ipwhois - Retrieve and parse whois data for IP addresses (Python 3)
kali@kali:~/kali/packages/finalrecon$
```

- - -

We could search each one manually by repeating the above process for all items in `requirements.txt`, or we can make a quick loop to automate it.

During this process, we will notice **one dependency which does not have an entry (`icmplib`)**.

```console
kali@kali:~/kali/packages/finalrecon$ cat requirements.txt | while read x; do
  apt-cache search $x | grep -i "python3-$x -" \
    || echo --MISSING $x--;
done
python3-requests - elegant and simple HTTP library for Python3, built for human beings
python3-ipwhois - Retrieve and parse whois data for IP addresses (Python 3)
python3-bs4 - error-tolerant HTML parser for Python 3
python3-lxml - pythonic binding for the libxml2 and libxslt libraries
python3-dnslib - Module to encode/decode DNS wire-format packets (Python 3)
python3-aiohttp - http client/server for asyncio
python3-aiodns - Asynchronous DNS resolver library for Python 3
python3-psycopg2 - Python 3 module for PostgreSQL
python3-tldextract - Python library for separating TLDs
--MISSING icmplib--
kali@kali:~/kali/packages/finalrecon$
```

- - -

We can try and **broaden our search** for `icmplib`, as we were limiting output last time _(by using grep)_.

```console
kali@kali:~/kali/packages/finalrecon$ apt-cache search icmplib | grep -i python3
kali@kali:~/kali/packages/finalrecon$
kali@kali:~/kali/packages/finalrecon$ apt-cache search icmplib
kali@kali:~/kali/packages/finalrecon$
```

- - -

Unfortunately it appears that Kali Linux does not have this dependency (Python's icmplib) in the repository at this point in time. This means we will need to **extend our packaging process** to accommodate for packaging up icmplib as well, to allow us to completely package up FinalRecon.

We will first look for `icmplib` in the **[pypi.org](https://pypi.org/) repository**. We can easily find [icmplib on PyPI](https://pypi.org/project/icmplib/) along with the link to its [GitHub page](https://github.com/ValentinBELYN/icmplib). If we do the same process with icmplib looking over the GitHub page as we did for FinalRecon, we can see that **icmplib will not need additional dependencies** (no `requirements.txt` file and `setup.py` [does not list anything for](https://packaging.python.org/discussions/install-requires-vs-requirements/) `install_requires`) and therefore will be a relatively straightforward Python package.

We can now either:

- Continue to package FinalRecon, before moving onto icmplib. We have to remember that we cannot successfully build a complete working package until we are done with `icmplib`.
- Pause FinalRecon packaging, and switch our focus to icmplib. We have to make sure we took detailed notes with the work we have done so far and information gathered.

We will go with the former option, and **continue as far as we can with FinalRecon**.

## Editing FinalRecon Package Source Code

We can now start to edit the files in the `debian/` folder.

### Changelog

We will now perform what are our standard changes ([#1 (Instaloader)](/docs/development/intro-to-packaging-example/) & [#2 (Photon)](/docs/development/intermediate-packaging-example/)) to the **version**, **distribution** and **description**. The resulting file should be similar to the following.

```console
kali@kali:~/kali/packages/finalrecon$ vim debian/changelog
kali@kali:~/kali/packages/finalrecon$
kali@kali:~/kali/packages/finalrecon$ cat debian/changelog
finalrecon (0~20201108-0kali1) kali-dev; urgency=medium

  * Initial release

 -- Joseph O'Gorman <gamb1t@kali.org>  Sun, 08 Nov 2020 07:09:41 +0000
kali@kali:~/kali/packages/finalrecon$
```

{{% notice info %}}
You could also use `dch -r` or `gbp dch` to edit the file, rather than `vim`.

You will need to update the version to make the same date as used previously
{{% /notice %}}

### Control

Using what we know from the information we have already gathered from GitHub and the source code, it is once again similar to our previous packaging guides ([#1 (Instaloader)](/docs/development/intro-to-packaging-example/) & [#2 (Photon)](/docs/development/intermediate-packaging-example/)). We should have a good understanding of what needs to be altered now.

As there is no code that needs to be compiled, we can set `Architecture: all`. This is true for most Python scripts, as they are not providing Python "extensions". If they are, they would generate a compiled `.so` files (e.g. [psycopg2](https://pkg.kali.org/pkg/psycopg2)).

We make sure to include the **Python dependencies** for building the package as well as the tool dependencies to run (the values from pip).

There is one thing to note, and that is `python3-icmplib`. **This package does not exist yet**. We are adding this in for the time being as we will be creating it soon, to prevent going back and adding it we will add it now. This does **mean that we will be unable to build our package until** we finish with `icmplib`.

```console
kali@kali:~/kali/packages/finalrecon$ vim debian/control
kali@kali:~/kali/packages/finalrecon$
kali@kali:~/kali/packages/finalrecon$ cat debian/control
Source: finalrecon
Section: misc
Priority: optional
Maintainer: Kali Developers <devel@kali.org>
Uploaders: Joseph O'Gorman <gamb1t@kali.org>
Build-Depends: debhelper-compat (= 12),
               dh-python,
               python3-aiodns,
               python3-aiohttp,
               python3-all,
               python3-bs4,
               python3-dnslib,
               python3-icmplib,
               python3-ipwhois,
               python3-lxml,
               python3-psycopg2,
               python3-requests,
               python3-tldextract,
Standards-Version: 4.5.0
Homepage: https://github.com/thewhiteh4t/FinalRecon
Vcs-Browser: https://gitlab.com/kalilinux/packages/finalrecon
Vcs-Git: https://gitlab.com/kalilinux/packages/finalrecon

Package: finalrecon
Architecture: all
Depends: ${misc:Depends},
         ${python3:Depends},
         python3-aiodns,
         python3-aiohttp,
         python3-bs4,
         python3-dnslib,
         python3-icmplib,
         python3-ipwhois,
         python3-lxml,
         python3-psycopg2,
         python3-requests,
         python3-tldextract,
Description: A fast and simple python script for web reconnaissance
 A fast and simple python script for web reconnaissance that follows
 a modular structure and provides detailed information on various areas.
kali@kali:~/kali/packages/finalrecon$
```

### Copyright

As we have already finished getting the copyright information (license, name, contact, year and source), we now just need to add it.

```console
kali@kali:~/kali/packages/finalrecon$ vim debian/copyright
kali@kali:~/kali/packages/finalrecon$
kali@kali:~/kali/packages/finalrecon$ cat debian/copyright
Format: https://www.debian.org/doc/packaging-manuals/copyright-format/1.0/
Upstream-Name: finalrecon
Upstream-Contact: thewhiteh4t <thewhiteh4t@protonmail.com>
Source: https://github.com/thewhiteh4t/FinalRecon

Files: *
Copyright: 2020 thewhiteh4t <thewhiteh4t@protonmail.com>
License: MIT

Files: debian/*
Copyright: 2020 Joseph O'Gorman <gamb1t@kali.org>
License: MIT

License: MIT
 Copyright (c) 2020 thewhiteh4t
 .
 Permission is hereby granted, free of charge, to any person obtaining a copy
 of this software and associated documentation files (the "Software"), to deal
 in the Software without restriction, including without limitation the rights
 to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
 copies of the Software, and to permit persons to whom the Software is
 furnished to do so, subject to the following conditions:
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
kali@kali:~/kali/packages/finalrecon$
```

### Rules

The start of the rules file will look very similar to [#2 (Photon)](/docs/development/intermediate-packaging-example/), however there is a new lower section.
This part is to set the permissions on `finalrecon.py`, so when we call it using the symlinks (by `debian/links`), it will be executable.

```console
kali@kali:~/kali/packages/finalrecon$ vim debian/rules
kali@kali:~/kali/packages/finalrecon$
kali@kali:~/kali/packages/finalrecon$ cat debian/rules
#!/usr/bin/make -f
#export DH_VERBOSE = 1
export PYBUILD_NAME=finalrecon

%:
	dh $@ --with python3

override_dh_install:
	dh_install
	chmod 0755 debian/finalrecon/usr/share/finalrecon/finalrecon.py
kali@kali:~/kali/packages/finalrecon$
```

{{% notice info %}}
Beware that the "dh" line needs to be indented by a single tabulation character, rather than spaces.
{{% /notice %}}

### Watch

This watch file should be in theory be easy. However as there are no tag releases at the moment we cannot account for potential future issues.

We will go with the **[Debian standard watch file for GitHub](https://wiki.debian.org/debian/watch)**.

```console
kali@kali:~/kali/packages/finalrecon$ vim debian/watch
kali@kali:~/kali/packages/finalrecon$
kali@kali:~/kali/packages/finalrecon$ cat debian/watch
version=4
opts=filenamemangle=s/.+\/v?(\d\S+)\.tar\.gz/finalrecon-$1\.tar\.gz/ \
  https://github.com/thewhiteh4t/FinalRecon/tags .*/v?(\d\S+)\.tar\.gz
kali@kali:~/kali/packages/finalrecon$
```

### Links

Whereas last time ([#1 (Instaloader)](/docs/development/intro-to-packaging-example/) & [#2 (Photon)](/docs/development/intermediate-packaging-example/)), we are not going to use a "helper-script" but instead create a symlink pointing to the main Python file, which will still be in `$PATH`.

```console
kali@kali:~/kali/packages/finalrecon$ vim debian/links
kali@kali:~/kali/packages/finalrecon$
kali@kali:~/kali/packages/finalrecon$ cat debian/links
usr/share/finalrecon/finalrecon.py usr/bin/finalrecon
kali@kali:~/kali/packages/finalrecon$
```

{{% notice info %}}
One thing we notice during the testing process of the script, we have to `cd` into the directory before calling the Python script. This is because the tool attempts to drop files into the `dumps` folder, and if it is not in path FinalRecon will fail.

This could be discovered by auditing the source code, or trial and error when testing the package after its built.
{{% /notice %}}

### .Install

We can now create the install file, which is required to say **what files go where on the system during the unpacking of the package**. We need to make sure to include everything from the root of the package directory.

```console
kali@kali:~/kali/packages/finalrecon$ vim debian/finalrecon.install
kali@kali:~/kali/packages/finalrecon$
kali@kali:~/kali/packages/finalrecon$ cat debian/finalrecon.install
conf usr/share/finalrecon/
finalrecon.py usr/share/finalrecon/
modules usr/share/finalrecon/
wordlists usr/share/finalrecon/
kali@kali:~/kali/packages/finalrecon$
```

{{% notice info %}}
There is not a leading slash on the destination directory
{{% /notice %}}

### Patches

For this tool we will need to also implement a **patch to disable the update and dependency checker**. If the program self updates, the system will not be aware of any additional files outside of the package, so things then start to get messy. The dependency is also being handled by our package now instead. Knowing you need to do this, comes with either knowing the tool, or auditing the source code.

The patch process looks like the following (for more information see [our previous guide, #2 (Photon)](/docs/development/intermediate-packaging-example/)).

```console
kali@kali:~/kali/packages/finalrecon$ gbp pq import
gbp:info: Trying to apply patches at 'f1c4c9f8d25224186749ce69a9f403f207feda03'
gbp:info: 0 patches listed in 'debian/patches/series' imported on 'patch-queue/kali/master'
kali@kali:~/kali/packages/finalrecon$
kali@kali:~/kali/packages/finalrecon$ vim finalrecon.py
kali@kali:~/kali/packages/finalrecon$
kali@kali:~/kali/packages/finalrecon$ git add finalrecon.py
kali@kali:~/kali/packages/finalrecon$
kali@kali:~/kali/packages/finalrecon$ git commit -m "disable requirements check"
...
kali@kali:~/kali/packages/finalrecon$
kali@kali:~/kali/packages/finalrecon$ vim finalrecon.py
kali@kali:~/kali/packages/finalrecon$
kali@kali:~/kali/packages/finalrecon$ git add finalrecon.py
kali@kali:~/kali/packages/finalrecon$
kali@kali:~/kali/packages/finalrecon$ git commit -m "disable ver_check"
...
kali@kali:~/kali/packages/finalrecon$
kali@kali:~/kali/packages/finalrecon$ gbp pq export
gbp:info: On 'patch-queue/kali/master', switching to 'kali/master'
gbp:info: Generating patches from git (kali/master..patch-queue/kali/master)
kali@kali:~/kali/packages/finalrecon$
kali@kali:~/kali/packages/finalrecon$ git branch -v
* kali/master             f1c4c9f New upstream version 0~20201108
  patch-queue/kali/master 2935f22 disable ver_check
  pristine-tar            69f689f pristine-tar data for finalrecon_0~20201108.orig.tar.gz
  upstream                f1c4c9f New upstream version 0~20201108
kali@kali:~/kali/packages/finalrecon$
kali@kali:~/kali/packages/finalrecon$ ls debian/patches/
disable-requirements-check.patch  disable-ver_check.patch  series
kali@kali:~/kali/packages/finalrecon$
kali@kali:~/kali/packages/finalrecon$ cat debian/patches/disable-requirements-check.patch
From: Joseph O'Gorman <gamb1t@kali.org>
Date: Sun, 8 Nov 2020 07:26:07 +0000
Subject: disable requirements check

---
 finalrecon.py | 32 ++++++++++++++++----------------
 1 file changed, 16 insertions(+), 16 deletions(-)

diff --git a/finalrecon.py b/finalrecon.py
index 735f40b..95e99f1 100644
--- a/finalrecon.py
+++ b/finalrecon.py
@@ -26,22 +26,22 @@ else:

 path_to_script = os.path.dirname(os.path.realpath(__file__))

-with open(path_to_script + '/requirements.txt', 'r') as rqr:
-       pkg_list = rqr.read().strip().split('\n')
-
-print('\n' + G + '[+]' + C + ' Checking Dependencies...' + W + '\n')
-
-for pkg in pkg_list:
-       spec = importlib.util.find_spec(pkg)
-       if spec is None:
-               print(R + '[-]' + W + ' {}'.format(pkg) + C + ' is not Installed!' + W)
-               fail = True
-       else:
-               pass
-if fail == True:
-       print('\n' + R + '[-]' + C + ' Please Execute ' + W + 'pip3 install -r requirements.txt' + C + ' to Install Missing Packages' + W + '\n')
-       os.remove(pid_path)
-       sys.exit()
+#with open(path_to_script + '/requirements.txt', 'r') as rqr:
+#      pkg_list = rqr.read().strip().split('\n')
+
+#print('\n' + G + '[+]' + C + ' Checking Dependencies...' + W + '\n')
+
+#for pkg in pkg_list:
+#      spec = importlib.util.find_spec(pkg)
+#      if spec is None:
+#              print(R + '[-]' + W + ' {}'.format(pkg) + C + ' is not Installed!' + W)
+#              fail = True
+#      else:
+#              pass
+#if fail == True:
+#      print('\n' + R + '[-]' + C + ' Please Execute ' + W + 'pip3 install -r requirements.txt' + C + ' to Install Missing Packages' + W + '\n')
+#      os.remove(pid_path)
+#      sys.exit()

 import argparse

kali@kali:~/kali/packages/finalrecon$
kali@kali:~/kali/packages/finalrecon$ cat debian/patches/disable-requirements-check.patch
From: Joseph O'Gorman <gamb1t@kali.org>
Date: Sun, 8 Nov 2020 07:26:53 +0000
Subject: disable ver_check

---
 finalrecon.py | 2 +-
 1 file changed, 1 insertion(+), 1 deletion(-)

diff --git a/finalrecon.py b/finalrecon.py
index 95e99f1..d21877c 100644
--- a/finalrecon.py
+++ b/finalrecon.py
@@ -207,7 +207,7 @@ def full_recon():
 try:
        fetch_meta()
        banner()
-       ver_check()
+       #ver_check()

        if target.startswith(('http', 'https')) == False:
                print(R + '[-]' + C + ' Protocol Missing, Include ' + W + 'http://' + C + ' or ' + W + 'https://' + '\n')
kali@kali:~/kali/packages/finalrecon$
```

## Runtime Test

The [runtime test](/docs/development/contributing-runtime-tests/) process looks like the following (for more information see [our previous guide, #2 (Photon)](/docs/development/intermediate-packaging-example/)). Just like last time, we will just create a **minimal test to look for the help screen**.

```console
kali@kali:~/kali/packages/finalrecon$ mkdir -p debian/tests/
kali@kali:~/kali/packages/finalrecon$
kali@kali:~/kali/packages/finalrecon$ vim debian/tests/control
kali@kali:~/kali/packages/finalrecon$
kali@kali:~/kali/packages/finalrecon$ cat debian/tests/control
Test-Command: finalrecon -h
Restrictions: superficial
kali@kali:~/kali/packages/finalrecon$
```

## Completing dependencies

In theory, we should have a complete working package now with the exception of the missing `icmplib` dependency. So we now need to package up `icmplib`, before trying to finally build FinalRecong.

## icmplib

### Naming Packages

Unlike our previous guides ([#1 (Instaloader)](/docs/development/intro-to-packaging-example/) & [#2 (Photon)](/docs/development/intermediate-packaging-example/)) where we use the same name for both **source package** and **binary package**, this time we will differ them.

The naming convention for a **binary package is `python3-<package>`**, which is important to follow as it has a impact at a technical level. However a **source package can be `python-<package>`** (or even just `<package>`). It does not matter if this is not followed as it will not break anything if its not followed. However, from a Kali team point of view we prefer and will use `python-<package>`. See this [Debian resource](https://www.debian.org/doc/packaging-manuals/python-policy/ch-module_packages.html) for more information.

### Cheat Sheet Packaging

This package is straightforward using `python3-setuptools` (like in our [first guide (Instaloader)](/docs/development/intro-to-packaging-example/)), so to prevent this guide from getting too long, we will not be going step by step for `icmplib`.

For more information on building Python libraries, see the [Debian resource](https://wiki.debian.org/Python/LibraryStyleGuide)

Here is a quick overview of the **commands needed to build the package**.

```plaintext
mkdir -p ~/kali/upstream/ ~/kali/build-area/ ~/kali/packages/python-icmplib/ 
wget https://github.com/ValentinBELYN/icmplib/archive/v1.2.2.tar.gz -O ~/kali/upstream/python-icmplib_1.2.2.orig.tar.gz
cd ~/kali/packages/python-icmplib/
git init
gbp import-orig ~/kali/upstream/python-icmplib_1.2.2.orig.tar.gz --no-interactive --debian-branch=kali/master
dh_make --file ~/kali/upstream/python-icmplib_1.2.2.orig.tar.gz -p python-icmplib_1.2.2 --python -y
rm -f debian/{*.docs,README*,*.ex,*.EX}
vim debian/changelog
vim debian/control
vim debian/copyright
vim debian/rules
vim debian/watch
gbp buildpackage --git-builder=sbuild --git-export=WC
sudo dpkg -i ~/kali/build-area/python3-icmplib_1.2.2-0kali1_all.deb
pip search icmplib
git add debian/
git commit -m "Initial release"
```

- - -

Previewing the contents of the key filesin `debian/`:

#### Changelog

Straight forward, like all the other guides, [#1 (Instaloader)](/docs/development/intro-to-packaging-example/) & [#2 (Photon)](/docs/development/intermediate-packaging-example/), edit **version**, **distribution** and **description**.

Note, `python-icmplib` needs to match the source name in `debian/control`.

```console
kali@kali:~/kali/packages/python-icmplib$ cat debian/changelog
python-icmplib (1.2.2-0kali1) kali-dev; urgency=medium

  * Initial release

 -- Joseph O'Gorman <gamb1t@kali.org>  Mon, 12 Oct 2020 18:10:27 -0400
kali@kali:~/kali/packages/python-icmplib$
```

#### Control

This is a bit different to what we have seen previously with `Section: python`. This is because its a **Python library**. For more information see [Debian's write up](https://www.debian.org/doc/debian-policy/ch-archive.html#s-subsections) as well as the [different options](https://packages.debian.org/testing/).

We also need to name the package differently. The source package part of `debian/control` is the top part, which gets named with the  `Source:` field, whereas the binary part the lower half and uses `Package:` to name. _Were possible Kali Linux will always try and do both a source and binary package (See the [Debian resource](https://wiki.debian.org/Packaging/SourcePackage) for more information)._

Note, the source name `python-icmplib` needs to match in `debian/changelog`.

```console
kali@kali:~/kali/packages/python-icmplib$ cat debian/control
Source: python-icmplib
Section: python
Priority: optional
Maintainer: Kali Developers <devel@kali.org>
Uploaders: Joseph O'Gorman <gamb1t@kali.org>
Build-Depends: debhelper-compat (= 12),
                                dh-python,
                                python3-all,
                                python3-setuptools
Standards-Version: 4.5.0
Homepage: https://github.com/ValentinBELYN/icmplib
Vcs-Browser: https://gitlab.com/kalilinux/packages/python-icmplib
Vcs-Git: https://gitlab.com/kalilinux/packages/python-icmplib.git

Package: python3-icmplib
Architecture: all
Depends: ${python3:Depends}, ${misc:Depends}
Description: Python tool to forge ICMP packages
 icmplib is a brand new and modern implementation of the ICMP protocol in Python
 Able to forge ICMP packages to make your own ping, multiping, traceroute etc
kali@kali:~/kali/packages/python-icmplib$
```

#### Copyright

As we renamed the `orig.tar.gz`, upstream name is incorrect, as it normally would not have a leading `python3-`. We can get this from the **source URL**.

```console
kali@kali:~/kali/packages/python-icmplib$ cat debian/copyright
Format: https://www.debian.org/doc/packaging-manuals/copyright-format/1.0/
Upstream-Name: icmplib
Upstream-Contact: Valentin BELYN <valentin-hello@gmx.com>
Source: https://github.com/ValentinBELYN/icmplib

Files: *
Copyright: 2020 Valentin BELYN <valentin-hello@gmx.com>
License: LGPL-3+

Files: debian/*
Copyright: 2020 Joseph O'Gorman <gamb1t@kali.org>
License: LGPL-3+

License: LGPL-3+
 This program is free software; you can redistribute it and/or modify it
 under the terms of the GNU Lesser General Public License as
 published by the Free Software Foundation; either version 3 of
 the License, or (at your option) any later version.
 .
 This program is distributed in the hope that it will be useful, but
 WITHOUT ANY WARRANTY; without even the implied warranty of
 MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the GNU
 Lesser General Public License for more details.
 .
 You should have received a copy of the GNU Lesser General Public
 License along with this program; if not, see <https://www.gnu.org/licenses/>.
 .
 On Debian systems, the full text of the GNU Lesser General Public
 License version 3 can be found in the file
 `/usr/share/common-licenses/LGPL-3'.
kali@kali:~/kali/packages/python-icmplib$
```

#### Rules

We need to make sure to drop any leading `python-` when being defined in `PYBUILD_NAME`, even though the binary package which gets produced (as defined in `debian/control`) will be `python3-icmplib`. This is because of [PyBuild](https://wiki.debian.org/Python/Pybuild), **only wanting the Python module name**.

```console
kali@kali:~/kali/packages/python-icmplib$ cat debian/rules
#!/usr/bin/make -f
#export DH_VERBOSE = 1
export PYBUILD_NAME=icmplib

%:
	dh $@ --with python3 --buildsystem=pybuild
kali@kali:~/kali/packages/python-icmplib$
```

#### Watch

Straight forward, like all the other guides, [#1 (Instaloader)](/docs/development/intro-to-packaging-example/) & [#2 (Photon)](/docs/development/intermediate-packaging-example/), using the **[Debian standard watch file for GitHub](https://wiki.debian.org/debian/watch)**.

```console
kali@kali:~/kali/packages/python-icmplib$ cat debian/watch
version=4
opts=filenamemangle=s/.+\/v?(\d\S+)\.tar\.gz/icmplib-$1\.tar\.gz/ \
  https://github.com/ValentinBELYN/icmplib/tags .*/v?(\d\S+)\.tar\.gz
kali@kali:~/kali/packages/python-icmplib$
```

- - -

We have successfully managed to build a Python 3 library file, icmplib!

#### Final FinalRecon Build

As we may not have pushed out, had `python3-icmplib` being accepted yet into Kali Linux, or you may want to submit both at the same time, we can **include the recently generated package in the chroot** for `sbuild` to use, it is a listed requirement for FinalRecon.

We are also unsure about the status of the package, we may not want to commit the latest edits to Git. So we will add `--git-export=WC` when **building the package**.

```console
kali@kali:~/kali/packages/python-icmplib$ cd ~/kali/packages/finalrecon/
kali@kali:~/kali/packages/finalrecon$
kali@kali:~/kali/packages/finalrecon$ gbp buildpackage \
  --git-builder=sbuild --git-export=WC \
  --extra-package=$HOME/kali/build-area/python3-icmplib_1.2.2-0kali1_all.deb
...
kali@kali:~/kali/packages/finalrecon$
kali@kali:~/kali/packages/finalrecon$ ls -lah ~/kali/build-area/finalrecon_*.deb
-rw-rw-r-- 1 kali kali 83K Nov  8 07:44 /home/kali/kali/build-area/finalrecon_0~20201108-0kali1_all.deb
kali@kali:~/kali/packages/finalrecon$
```

- - -

Before we try to test our newly generated package, we remember that in `debian/control` we listed a few dependencies (not only to build the package but to run the package). Using `
dpkg`, it will not satisfy these requirements, so we need to manually install them first. We can **check what is missing from our operating system**, by doing.

```console
kali@kali:~/kali/packages/finalrecon$ dpkg-checkbuilddeps
dpkg-checkbuilddeps: error: Unmet build dependencies: python3-ipwhois python3-dnslib python3-aiohttp python3-aiodns python3-psycopg2 python3-tldextract
kali@kali:~/kali/packages/finalrecon$
kali@kali:~/kali/packages/finalrecon$ sudo apt -y build-dep .
...
kali@kali:~/kali/packages/finalrecon$
```

{{% notice info %}}
If you fail to do install the package, you may end up with the following mess.

```console
kali@kali:~/kali/packages/finalrecon$ sudo dpkg -i ~/kali/build-area/finalrecon_*.deb
(Reading database ... 167589 files and directories currently installed.)
Preparing to unpack .../finalrecon_0~20201108-0kali1_all.deb ...
Unpacking finalrecon (0~20201108-0kali1) over (0~20201108-0kali1) ...
dpkg: dependency problems prevent configuration of finalrecon:
 finalrecon depends on python3-ipwhois; however:
  Package python3-ipwhois is not installed.
 finalrecon depends on python3-dnslib; however:
  Package python3-dnslib is not installed.
 finalrecon depends on python3-aiohttp; however:
  Package python3-aiohttp is not installed.
 finalrecon depends on python3-aiodns; however:
  Package python3-aiodns is not installed.
 finalrecon depends on python3-psycopg2; however:
  Package python3-psycopg2 is not installed.
 finalrecon depends on python3-tldextract; however:
  Package python3-tldextract is not installed.

dpkg: error processing package finalrecon (--install):
 dependency problems - leaving unconfigured
Processing triggers for kali-menu (2020.3.2) ...
Errors were encountered while processing:
 finalrecon
kali@kali:~/kali/packages/finalrecon$
```

- - -

You will then hit the issue of the next time you try and install or update a package, it will fail.

```console
kali@kali:~/kali/packages/finalrecon$ sudo apt upgrade
Reading package lists... Done
Building dependency tree
Reading state information... Done
You might want to run 'apt --fix-broken install' to correct these.
The following packages have unmet dependencies:
 finalrecon : Depends: python3-ipwhois but it is not installed
              Depends: python3-dnslib but it is not installed
              Depends: python3-aiohttp but it is not installed
              Depends: python3-aiodns but it is not installed
              Depends: python3-psycopg2 but it is not installed
              Depends: python3-tldextract but it is not installed
E: Unmet dependencies. Try 'apt --fix-broken install' with no packages (or specify a solution).
kali@kali:~/kali/packages/finalrecon$
```

- - -

Following what it says, running `sudo apt --fix-broken install` often fixes the issue.
{{% /notice %}}

- - -

Our package has been built and dependencies have been installed. Its now time to **finally install FinalRecon**.

```console
kali@kali:~/kali/packages/finalrecon$ sudo dpkg -i ~/kali/build-area/finalrecon_*.deb
...
kali@kali:~/kali/packages/finalrecon$
kali@kali:~/kali/packages/finalrecon$ dpkg -l | grep final
ii  finalrecon                           0~20201108-0kali1               all          A fast and simple python script for web reconnaissance
kali@kali:~/kali/packages/finalrecon$
```

{{% notice info %}}
You can also install by doing.

```console
kali@kali:~/kali/packages/finalrecon$ sudo debi --debs-dir ~/kali/build-area/
```

{{% /notice %}}

- - -

We have **successfully managed to build FinalRecon as a package**!

Let's test to **make sure it works**.

```console
kali@kali:~/kali/packages/finalrecon$ finalrecon
usage: finalrecon [-h] [--headers] [--sslinfo] [--whois] [--crawl] [--dns] [--sub] [--trace] [--dir] [--ps] [--full] [-t T] [-T T] [-w W] [-r] [-s]
                  [-sp SP] [-d D] [-e E] [-m M] [-p P] [-tt TT] [-o O]
                  url
finalrecon: error: the following arguments are required: url
kali@kali:~/kali/packages/finalrecon$
```

## Saving Our Work

At this point, we can **save the work** we have put in.

```console
kali@kali:~/kali/packages/finalrecon$ git add debian/
kali@kali:~/kali/packages/finalrecon$
kali@kali:~/kali/packages/finalrecon$ git commit -m "Initial release"
[kali/master d1c9f75] Initial release
 12 files changed, 169 insertions(+)
 create mode 100644 debian/changelog
 create mode 100644 debian/control
 create mode 100644 debian/copyright
 create mode 100644 debian/finalrecon.install
 create mode 100644 debian/links
 create mode 100644 debian/patches/disable-requirements-check.patch
 create mode 100644 debian/patches/disable-ver_check.patch
 create mode 100644 debian/patches/series
 create mode 100755 debian/rules
 create mode 100644 debian/source/format
 create mode 100644 debian/tests/control
 create mode 100644 debian/watch
kali@kali:~/kali/packages/finalrecon$
kali@kali:~/kali/packages/finalrecon$ git status
On branch kali/master
nothing to commit, working tree clean
kali@kali:~/kali/packages/finalrecon$
```

- - -

We can now finish up the packaging by **putting in a request on the [Kali Linux bug tracker](https://bugs.kali.org/)** for these packages to be added!

## Message From Kali Team

During the packaging process, we worked with the tool author (upstream), so the program would be [better fit with FHS](https://tldp.org/LDP/Linux-Filesystem-Hierarchy/html/). An example of this is, not to use `/usr/share/finalrecon/dumps` as writing to `/usr/share` requires root privileges and we also do not want user files to be saved here.
