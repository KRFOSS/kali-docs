---
title: Public Packaging
description:
icon:
type: archived
weight:
author: ["gamb1t",]
---

# Getting involved in the Kali ecosystem

Kali Linux has a large number of tools that it maintains and adds on a regular basis. However, we can't always consider every request for a package to be added because of the large number of requests. Because of this, we have developed requirements that we have users follow in order to increase the chances that we see them, and then are able to properly consider them. If you have visited the [bug tracker](http://bugs.kali.org) you may have noticed the requirements posted on certain package requests. If you haven't, no worries, because they are as follows:

```plaintext
- [Name] - The name of the tool
- [Version] - What version of the tool should be added?
--- If it uses source control (such as git), please make sure there is a release to match (e.g. git tag)
- [Homepage] - Where can the tool be found online? Where to go to get more information?
- [Download] - Where to go to get the tool? Either a download page or a link to the latest version
- [Author] - Who made the tool?
- [License] - How is the software distributed? What conditions does it come with?
- [Description] - What is the tool about? What does it do?
- [Dependencies] - What is needed for the tool to work?
- [Similar tools] - What other tools are out there?
- [Activity] - When did the project start? Is is still actively being deployed?
- [How to install] - How do you compile it?
--- Note, using source code to acquire (e.g. git clone/svn checkout) can't be used - Also downloading from the head. Please use a "tag" or "release" version.
- [How to use] - What are some basic commands/functions to demonstrate it?
```

You may be asking at this point 'How does this relate to me getting involved?'. Well that's simple: users can now do most of the work on their own to get the tool added thanks to [our move to GitLab](https://gitlab.com/kalilinux/). Keep in mind that we still will not be adding all tools that are requested; perhaps there is a different tool that does the same thing but has been around longer, or maybe the tool is too new and needs time to really get more user's opinion on. There are a few things that those who want to get involved need to do first, which is what we are going to walk you through now.

Related:
- [Setting Up A System For Packaging](/docs/development/setting-up-packaging-system/)

- - -

For those of you who want to get involved and maintain your package, you're going to need to create a merge request. There are two things to know during this. The first is how to create the initial package and the other is how to continue supporting it. For easier understanding, let's take a visual look at this.

## Creating the initial folder

Before we start the "packaging" we need to get the folder prepared properly. Assuming the tool you want to package is already prepared and you are the owner, it is recommended to create a separate branch and add it directly in the "debian" directory. After this is done, skip to "Creating the Debian files" and follow along from there.

Otherwise, you need to pull the release if they have one. If they **do** have a release, skip to that header. If they **don't** have a release, clone the repo and do the following command:

```console
packaging@kali:~$ git clone https://github.com/ambionics/phpggc.git phpgcc-official
packaging@kali:~$
packaging@kali:~$ cd phpggc-official
packaging@kali:~$
packaging@kali:~/phpggc-official$ git archive --format=tar master | gzip -c > ../PACKAGE_YEARMONTHDAY.orig.tar.gz
packaging@kali:~/phpggc-official$
```
Be sure to change both package and date to the appropriate names.

We create an empty git repo and then clone it, then we can import the tool. _Be sure to create a new empty repo of your own for your own package for this step._

```console
packaging@kali:~$ git clone git@gitlab.com:PackageAllTheThings/phpggc.git
packaging@kali:~$
packaging@kali:~$ cd phpggc/
packaging@kali:~/phpggc$ gbp import-orig ../phpggc_0.20191028.orig.tar.gz
packaging@kali:~/phpggc$
packaging@kali:~/phpggc$ git checkout -b kali/master
packaging@kali:~/phpggc$
packaging@kali:~/phpggc$ git branch -D master
packaging@kali:~/phpggc$
packaging@kali:~/phpggc$ git push -u origin --all
packaging@kali:~/phpggc$
```

#### Working with a release
To get the release, we follow a similar process as to when there is no release. We first get the latest release tar.gz link and output the file to the proper Debian format.

```console
packaging@kali:~$ wget -O phpggc_1.0.1.orig.tar.gz https://gitlab.com/PackageAllTheThings/phpggc/archive/v1.0.1.tar.gz
packaging@kali:~$
packaging@kali:~$ git clone git@gitlab.com:PackageAllTheThings/phpggc.git
packaging@kali:~$ cd phpggc/
packaging@kali:~/phpggc$ gbp import-orig ../phpggc_1.0.1.orig.tar.gz
packaging@kali:~/phpggc$
packaging@kali:~/phpggc$ git checkout -b kali/master
packaging@kali:~/phpggc$
packaging@kali:~/phpggc$ git branch -D master
packaging@kali:~/phpggc$
packaging@kali:~/phpggc$ git push -u origin --all
packaging@kali:~/phpggc$
```


## Creating the Debian files

First we need to generate the base Debian files and remove some of the ones that won't be needed. When prompted we select single for the package type, and assuming everything else is correct, the default values for the rest.

```console
packaging@kali:~/phpggc$ dh_make -p phpggc_0.20191028
packaging@kali:~/phpggc$
packaging@kali:~/phpggc$ cd debian/
packaging@kali:~/phpggc/debian$ rm *.ex *.EX README.* *.docs
packaging@kali:~/phpggc/debian$
```
Next we will need to edit some of the files with the proper information.

```console
packaging@kali:~/phpggc/debian$ vim control
packaging@kali:~/phpggc/debian$
packaging@kali:~/phpggc/debian$ cat control
Source: phpggc
Section: net
Priority: optional
Maintainer: Kali Developers <devel@kali.org>
Uploaders: First Last <email@domain.com>
Build-Depends: debhelper (>= 11)
Standards-Version: 4.1.3
Homepage: https://github.com/ambionics/phpggc
Vcs-Git: https://gitlab.com/PackageAllTheThings/packages/phpggc.git
Vcs-Browser: https://gitlab.com/PackageAllTheThings/packages/phpggc

Package: phpggc
Architecture: any
Depends: ${shlibs:Depends}, ${misc:Depends}, php-cli
Description: A library of unserialize() payloads
 PHPGGC is a library of unserialize() payloads
 along with a tool to generate them, from
 command line or programmatically.
packaging@kali:~$
```

There are a number of things to take note of here. Section, priority, maintainer, uploaders, homepage, depends, and description are all changed. Going through them:

* 'section' will be what type of tool it is. Put in your best guess as to what it may be in the general area (web, net, etc) and we will change it if need be
* Priority can be set to optional
* Maintainer should always be "Kali Developers <devel@kali.org>" and Uploaders should be your name (it can be account name) and the email associated with the account
* The homepage is where the tool is originally from. Depends is whatever needs to be installed to make the tool work, which, in this case php is needed
* Vcs-* should be set to your repo that you are pushing the package to.
* Description is the combination of the short description and an extended one that explains what the package contains

```console
packaging@kali:~/phpggc/debian$ vim changelog
packaging@kali:~/phpggc/debian$
packaging@kali:~/phpggc/debian$ cat changelog
phpggc (0.20191028-0kali1) kali-dev; urgency=medium

  * Initial release

 -- First Last <email@domain.com>  Mon, 28 Oct 2019 19:29:57 -0400
packaging@kali:~$
```

Be sure to set kali-dev rather than unstable before urgency, otherwise there will be issues later from sbuild. You can remove everything after "Initial release". We also use a Debian revision of "-0kali1" instead of the default "-1" to avoid conflicts with a version that would be used by an upstream Debian package.

```console
packaging@kali:~/phpggc/debian$ vim copyright
packaging@kali:~/phpggc/debian$
packaging@kali:~/phpggc/debian$ cat copyright
Format: https://www.debian.org/doc/packaging-manuals/copyright-format/1.0/
Upstream-Name: phpggc
Upstream-Contact: contact@ambionics.io
Source: https://github.com/ambionics/phpggc

Files: *
Copyright: 2019 ambionics <contact@ambionics.io>
License: Apache License 2.0
 /usr/share/common-licenses/Apache-2.0

Files: debian/*
Copyright: 2019 First Last <email@domain.com>
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
packaging@kali:~$
```

There will be a lot of clutter when you first open copyright, most can be deleted but be sure to read what you are removing as some information may be important. The copyright and license for `Files: *` will be whatever the original package uses. In this case, the original package used Apache License 2.0, and as it has the full license already in Debain it can be linked to as above. A good command to know of is `licensecheck -r . --copyright` which will give a rough idea on if there are any licenses that were missed.

```console
packaging@kali:~/phpggc/debian$ vim watch
packaging@kali:~/phpggc/debian$
packaging@kali:~/phpggc/debian$ cat watch
version=4
opts=filenamemangle=s/.+\/v?(\d\S*)\.tar\.gz/phpggc-$1\.tar\.gz/ \
  https://github.com/ambionics/phpggc/tags .*/v?(\d\S*)\.tar\.gz
packaging@kali:~$
```

This file can look a bit intimidating, but what actually needs to be changed is very easy as demonstrated:

```console
opts=filenamemangle=s/.+\/v?(\d\S*)\.tar\.gz/PACKAGENAME-$1\.tar\.gz/ \
  ORIGINALGITLINK/tags .*/v?(\d\S*)\.tar\.gz
```

This file will _watch_ for any changes in the released version number of the upstream, which will allow updating the package on time later easier. There are more examples available on the [Debian wiki](https://wiki.debian.org/debian/watch).

## Final touches

If we built the package now, it would not be installed. To fix this, let's create an .install file and a helper script. The reason we are creating these two files is that they both will work the majority of the time. In some cases, the different ways, like using a symlink, may not work and changes will have to be made. As we can't account for every scenario now, we will go with what works the majority of the time.

```console
packaging@kali:~/phpggc/debian$ mkdir helper-script
packaging@kali:~/phpggc/debian$
packaging@kali:~/phpggc/debian$ vim phpggc.install
packaging@kali:~/phpggc/debian$
packaging@kali:~/phpggc/debian$ cat phpggc.install
lib usr/share/phpggc/
phpggc usr/share/phpggc/
templates usr/share/phpggc/
gadgetchains usr/share/phpggc/
debian/helper-script/phpggc usr/bin/
packaging@kali:~/phpggc/debian$
packaging@kali:~/phpggc/debian$ vim helper-script/phpggc
packaging@kali:~/phpggc/debian$
packaging@kali:~$ cat helper-script/phpggc
#!/bin/sh

cd /usr/share/phpggc
exec ./phpggc
packaging@kali:~$
```

Some of you may have caught something odd and are wondering what's up with the formatting of the .install file. With the way that the package builder interprets things, a `/` at the beginning of "usr/" will break things, likewise no slash at the end will as well. We include all the files that will be installed in the ".install" file. In the helper-script, we go to that directory and launch the file.

Now that all that is done, we can push everything to git and try it out!

```console
packaging@kali:~/phpggc/debian$ cd ..
packaging@kali:~/phpggc$
packaging@kali:~/phpggc$ git add .
packaging@kali:~/phpggc$
packaging@kali:~/phpggc$ git commit -m "Packaged up!"
packaging@kali:~/phpggc$
packaging@kali:~/phpggc$ git push
packaging@kali:~/phpggc$
packaging@kali:~/phpggc$ gbp buildpackage --git-builder=sbuild
packaging@kali:~$
```

This may take a little bit, and in the end a few things can occur. If lintian says "Failed" and there are errors, we recommend googling them and if no solution can be found then submit a post to the [forums](https://forums.kali.org/forumdisplay.php?8-Kali-Linux-Development) where we can assist. If lintian does not fail, then you can find your package in `/home/$USER/kali/build-area/`. Be sure to test it out by using dpkg to install the package and run it.

## Menu icon

If a `.desktop` file is needed to be created for a menu icon, then this is best done by submitting a merge request to [the kali-menu package on GitLab](https://gitlab.com/kalilinux/packages/kali-menu). Fork the package, clone it, add in the file you'd like, and then you can submit a merge request with your changes. Below is an example of how the `.desktop` file should be done. Be sure to change "Categories" to whichever most closely fits the tool, and it is possible to include more than one.

```console
packaging@kali:~$ vim desktop-files/phpggc.desktop
packaging@kali:~$
packaging@kali:~$ cat desktop-files/phpggc.desktop
[Desktop Entry]
Name=PHPGGC
Encoding=UTF-8
Exec=/usr/share/kali-menu/exec-in-shell phpggc
Icon=kali-menu
StartupNotify=false
Terminal=true
Type=Application
Categories=08-exploitation-tools;
X-Kali-Package=phpggc
packaging@kali:~$
```

# Submitting to the tracker

Just one last thing to do; submit it to us! To do this, lets head over to [Kali's issue tracker](https://bugs.kali.org). We are going to want to submit a new issue with the category "New Tool Requests". For the title we will call it "phpggc: a library of unserialize() payloads along with a tool to generate them" and for the description we will include that list from earlier.

```plaintext
- [Name] - PHPGGC
- [Version] - 0.20191028
- [Homepage] - https://github.com/ambionics/phpggc
- [Package] - https://gitlab.com/PackageAllTheThings/phpggc
- [Author] - Ambionics
- [License] - Apache License 2.0
- [Description] - PHPGGC is a library of unserialize() payloads along with a tool to generate them, from command line or programmatically
- [Dependencies] - PHP
- [Similar tools] - unknown
- [Activity] - There was a commit last month
- [How to use] - phpggc -h
```

Once done, simply submit the issue and we will review it.

## What happens after we accept

When we accept the package into Kali, we duplicate the git repository into our kalilinux/packages GitLab group (through forking and deleting the relationship). Because of this, future changes made will need to be submitted as merge requests. In order to do this, users will have to fork our git repository into their account.
