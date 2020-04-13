---
title: Public Packaging
description:
icon:
date: 2020-02-24
type: post
weight: 100
author: ["gamb1t",]
tags: ["",]
keywords: ["",]
og_description:
---

# Getting involved in the Kali ecosystem

Kali Linux has a large number of tools that it maintains and adds on a regular basis. However, we can't always consider every request for a package to be added because of the large number of requests. Because of this, we have developed requirements that we have users follow in order to increase the chances that we see them, and then are able to properly consider them. If you have visited the [bug tracker](http://bugs.kali.org) you may have noticed the requirements posted on certain package requests. If you haven't, no worries, because they are as follows:

```markdown
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

You may be asking at this point 'How does this relate to me getting involved?'. Well that's simple: users can now do most of the work on their own to get the tool added thanks to [our move to GitLab](https://gitlab.com/kalilinux). Keep in mind that we still will not be adding all tools that are requested; perhaps there is a different tool that does the same thing but has been around longer, or maybe the tool is too new and needs time to really get more user's opinion on. There are a few things that those who want to get involved need to do first, which is what we are going to walk you through now.

Related: [MyRepos MR](../git-clone-my-repos), [Getting Started with Kali Development](../getting-started-with-kali-development), [Building packages with sbuild](../building-package-with-sbuild),

- - -

# Setting up the system

#### VM or install?

In this walkthrough we will be explaining certain things that are only on a VM. It is your choice if you want to install a full Kali system (or if you already have one, if you want to use it) or if you want to use a VM, however keep in mind what commands you're entering if it is an install.

#### Setting up the VM

It's important to set up a development environment. The easiest way to go about this is to set up a VM with the [latest Kali image](https://cdimage.kali.org/kali-weekly/) and give it a large filesystem. 80GB+ is good for a few packages at a time, however 150GB+ is recommended if [you are using `mr`](https://gitlab.com/kalilinux/tools/packaging) to download all packaging repositories. Likely, you will not need all of the packages to be downloaded.

#### User accounts and keys

Packaging needs to be done on a user with sudo privileges. The default Kali user is suitable for this. The name of the user can be whatever the user prefers, in this example we will be using the name "packaging".

```html
kali@kali:~# sudo adduser packaging
Adding user `packaging' ...
Adding new group `packaging' (1000) ...
Adding new user `packaging' (1000) with group `packaging' ...
Creating home directory `/home/packaging' ...
Copying files from `/etc/skel' ...
Enter new UNIX password: [PASSWORD]
Retype new UNIX password: [PASSWORD]
passwd: password updated successfully
Changing the user information for packaging
Enter the new value, or press ENTER for the default
 Full Name []:
 Room Number []:
 Work Phone []:
 Home Phone []:
 Other []:
Is the information correct? [Y/n] Y
```

Be sure to change `[PASSWORD]` to your own password. Keep in mind you won't see your password or any sort of sign it is being typed out even though it is still being registered.

```markdown
kali@kali:~# sudo usermod -G sudo packaging
```

Next you **must** log out of your account and switch to the new user. This is done as some pieces of the following setup require you to be on that account, `su` will not work.

Next, we should generate SSH and GPG keys. These are important for packaging as they will allow us to access our files on GitLab easily and ensure the work is ours. This step is not always necessary, however it is helpful in certain cases. You will know if you need to do this.

```html
packaging@kali:~$ ssh-keygen -t rsa
packaging@kali:~$ gpg --gen-key
gpg (GnuPG) 1.4.12; Copyright (C) 2012 Free Software Foundation, Inc.
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.

gpg: directory `/home/packaging/.gnupg' created
gpg: new configuration file `/home/packaging/.gnupg/gpg.conf' created
gpg: WARNING: options in `/home/packaging/.gnupg/gpg.conf' are not yet active during this run
gpg: keyring `/home/packaging/.gnupg/secring.gpg' created
gpg: keyring `/home/packaging/.gnupg/pubring.gpg' created
Please select what kind of key you want:
   (1) RSA and RSA (default)
   (2) DSA and Elgamal
   (3) DSA (sign only)
   (4) RSA (sign only)
 Your selection? 1
RSA keys may be between 1024 and 4096 bits long.
What keysize do you want? (2048)
Requested keysize is 2048 bits
Please specify how long the key should be valid.
         0 = key does not expire
      <n>  = key expires in n days
      <n>w = key expires in n weeks
      <n>m = key expires in n months
      <n>y = key expires in n years
Key is valid for? (0)
Key does not expire at all
Is this correct? (y/N) y

You need a user ID to identify your key; the software constructs the user ID
from the Real Name, Comment and Email Address in this form:
    "Heinrich Heine (Der Dichter) <heinrichh@duesseldorf.de>"

Real name: First Last
Email address: email@domain.com
Comment:
You selected this USER-ID:
     "First Last <email@domain.com>"

Change (N)ame, (C)omment, (E)mail or (O)kay/(Q)uit? O
You need a Passphrase to protect your secret key.

We need to generate a lot of random bytes. It is a good idea to perform
some other action (type on the keyboard, move the mouse, utilize the
disks) during the prime generation; this gives the random number
generator a better chance to gain enough entropy.

Not enough random bytes available.  Please do some other work to give
the OS a chance to collect more entropy! (Need 284 more bytes)

gpg: /home/packaging/.gnupg/trustdb.gpg: trustdb created
gpg: key A123BC4D marked as ultimately trusted
public and secret key created and signed.

gpg: checking the trustdb
gpg: 3 marginal(s) needed, 1 complete(s) needed, PGP trust model
gpg: depth: 0  valid:   1  signed:   0  trust: 0-, 0q, 0n, 0m, 0f, 1u
pub   2048R/1234AB5C 2000-00-00
      Key fingerprint = 12AB 34C4 67DE F890 12G3  H45I 6789 J90K L123 MN4O
uid                  First Last <email@domain.com>
sub   2048R/12345A6B 2000-00-00
```

The next step is to add the SSH key to your GitLab account. This can be done in the [keys section](https://gitlab.com/profile/keys). Run the command below to put the key in the copy-paste buffer and paste it on GitLab's web page.

```markdown
packaging@kali:~$ cat ~/.ssh/id_rsa.pub | xclip
```

#### Setting up files

We now need to set up git-buildpackage/`gbp buildpackage`. Remember to copy from the first line down to the second 'EOF'.

```markdown
packaging@kali:~$ cat <<EOF> ~/.gbp.conf
[DEFAULT]
pristine-tar = True
cleaner = /bin/true

[buildpackage]
sign-tags = True
export-dir = $HOME/kali/build-area/
ignore-branch = True
ignore-new = True

[import-orig]
filter-pristine-tar = True

[pq]
patch-numbers = False

[dch]
multimaint-merge = True
ignore-branch = True
EOF

packaging@kali:~$ grep -q DEBEMAIL ~/.bashrc \
  || echo export DEBEMAIL=email@domain.com >> ~/.bashrc
```
**Be sure to replace `email@domain.com` with your email, and ensure it is the same one used with your GPG key, if that was setup.**

We enable `pristine-tar` by default as we will use this tool to (efficiently) store a copy of the upstream tarball in the Git repository. We also set `export-dir` so that package builds happen outside of the git checkout directory.

Below, we're customizing some useful tools provided by the `devscripts` package:

```html
packaging@kali:~$ cat <<EOF> ~/.devscripts
DEBRELEASE_UPLOADER=dput
DEBRELEASE_DEBS_DIR=$HOME/kali/build-area/
DEBCHANGE_RELEASE_HEURISTIC=changelog
DEBCHANGE_MULTIMAINT_MERGE=yes
DEBCHANGE_PRESERVE=yes
DEBUILD_LINTIAN_OPTS="--color always -I"
DEBCHANGE_AUTO_NMU=no
DEBSIGN_KEYID=ABC123DE45678F90123G4567HIJK890LM12345N6
EOF

packaging@kali:~$ gpg -k

pub   rsa2048 2019-01-01 [SC] [expires: 2021-12-21]
      ABC123DE45678F90123G4567HIJK890LM12345N6
uid           [ultimate] First Last <email@domain.com>
sub   rsa2048 2019-01-01 [E] [expires: 2021-12-21]
```

**Be sure to put your own key id in `DEBSIGN_KEYID`. In this example we can see from `gpg - k` that our key is `ABC123DE45678F90123G4567HIJK890LM12345N6`**

You may also want to add the following to your git config:

```markdown
packaging@kali:~$ git config --global user.name "First Last"

packaging@kali:~$ git config --global user.email email@domain.com
```

**The `user.name` and `user.email` must match your gpg key details (`gpg -k`) or you will get a "Secret Key Not Available" error later on:**

```markdown
packaging@kali:~$ gpg -k

pub   2048R/A123BC4D 2012-12-07
uid                  First Last <email@domain.com>
sub   2048R/12345A6B 2012-12-07
```

We also want to enable a new git merge driver:

```html
packaging@kali:~$ cat <<EOF >> ~/.gitconfig
[merge "dpkg-mergechangelogs"]
         name = debian/changelog merge driver
         driver = dpkg-mergechangelogs -m %O %A %B %A
EOF
```

#### sbuild

We also will need to set up sbuild. Although this isn't too difficult, it does require some extra setup.

```markdown
packaging@kali:~$ sudo mkdir -p /srv/chroots/ && cd /srv/chroots

packaging@kali:~$ sudo sbuild-createchroot --keyring=/usr/share/keyrings/kali-archive-keyring.gpg --arch=amd64 --components=main,contrib,non-free --include=kali-archive-keyring kali-dev kali-dev-amd64-sbuild http://http.kali.org/kali
```

Once that is done, we need to edit `/etc/schroot/chroot.d/kali-dev-amd64-sbuild*`, note that "\*" is used as it will generate the last bit randomly. Alternatively, use TAB auto-completion.

```markdown
packaging@kali:~$ echo source-root-groups=root,sbuild >> /etc/schroot/chroot.d/kali-dev-amd64-sbuild*

packaging@kali:~$ cat /etc/schroot/chroot.d/kali-dev-amd64-sbuild*
[kali-dev-amd64-sbuild]
description=Debian kali-dev/amd64 autobuilder
groups=root,sbuild
root-groups=root,sbuild
profile=sbuild
type=directory
directory=/srv/chroots/kali-dev-amd64-sbuild
union-type=overlay
source-root-groups=root,sbuild
```

Finally, we just need to add our user to the group and do one last change.

```html
packaging@kali:~$ sudo sbuild-adduser $USER

packaging@kali:~$ touch ~/.sbuildrc

packaging@kali:~$ nano ~/.sbuildrc

packaging@kali:~$ cat ~/.sbuildrc
$build_arch_all = 1;
$build_source = 1;
$run_lintian = 1;
$lintian_opts = ['-I'];
```

Before we begin to do any real packaging, we need to install the following:

```markdown
packaging@kali:~$ sudo apt install -y packaging-dev apt-file gitk mr
...SNIP...
packaging@kali:~$
```

# Creating a package

For those of you who want to get involved and maintain your package, you're going to need to create a merge request. There are two things to know during this. The first is how to create the initial package and the other is how to continue supporting it. For easier understanding, let's take a visual look at this.

## Creating the initial folder

Before we start the "packaging" we need to get the folder prepared properly. Assuming the tool you want to package is already prepared and you are the owner, it is recommended to create a separate branch and add it directly in the "debian" directory. After this is done, skip to "Creating the Debian files" and follow along from there. Otherwise, you need to pull the release if they have one and run a few commands. If they don't have a release, clone the repo and do the following command `git archive --format=tar master | gzip -c > ../$PACKAGE_$VERSION.orig.tar.gz` ensuring to change both package and version to the appropriate names.

We create an empty git repo and then clone it, then we can import the tool.

```markdown
packaging@kali:~$ git clone https://gitlab.com/PackageAllTheThings/phpggc
packaging@kali:~$ cd phpggc
packaging@kali:~$ git remote add upstream https://gitlab.com/PackageAllTheThings/phpggc
packaging@kali:~$ git fetch upstream
packaging@kali:~$ git checkout master
packaging@kali:~$ git rebase upstream/master
packaging@kali:~$ gbp import-orig ../phpggc_0.20191028.orig.tar.gz
```

## Creating the Debian files

First we need to generate the base Debian files and remove some of the ones that won't be needed.

```html
packaging@kali:~$ dh_make -p phpggc_0.20191028
packaging@kali:~$ cd debian
packaging@kali:~$ rm *.ex *.EX README.* *.doc
```
Next we will need to edit some of the files with the proper information.

```html
packaging@kali:~$ nano control
packaging@kali:~$ nano changelog
packaging@kali:~$ nano copyright
packaging@kali:~$ nano watch
packaging@kali:~$ cat control
Source: phpggc
Section: net
Priority: optional
Maintainer: Kali Developers <devel@kali.org>
Uploaders: Package Things <packageallthethings@gmail.com>
Build-Depends: debhelper (>= 11)
Standards-Version: 4.1.3
Homepage: https://github.com/ambionics/phpggc
#Vcs-Browser: https://salsa.debian.org/debian/phpggc
#Vcs-Git: https://salsa.debian.org/debian/phpggc.git

Package: phpggc
Architecture: any
Depends: ${shlibs:Depends}, ${misc:Depends}, php-cli
Description: A library of unserialize() payloads
 PHPGGC is a library of unserialize() payloads
 along with a tool to generate them, from
 command line or programmatically.
```

There are a number of things to take note of here. Section, priority, maintainer, uploaders, homepage, depends, and description are all changed. Going through them, 'section' will be what type of tool it is. Put in your best guess as to what it may be in the general area (web, net, etc) and we will change it if need be. Priority can be set to optional. Maintainer should always be "Kali Developers <devel@kali.org>" and Uploaders should be your name (it can be account name) and the email associated with the account. The homepage is where the tool is originally from. Depends is whatever needs to be installed to make the tool work, which, in this case php is needed. Description is the combination of the short description and an extended one that explains what the package contains.

```markdown
packaging@kali:~$ cat changelog
phpggc (0.20191028-0kali1) kali-dev; urgency=medium

  * Initial release

 -- Package Things <packageallthethings@gmail.com>  Mon, 28 Oct 2019 19:29:57 -0400
```

Be sure to set kali-dev rather than unstable before urgency, otherwise there will be issues later from sbuild. You can remove everything after "Initial release". We also use a Debian revision of "-0kali1" instead of the default "-1" to avoid conflicts with a version that would be used by a Debian official package.

```markdown
packaging@kali:~$ cat copyright
Format: https://www.debian.org/doc/packaging-manuals/copyright-format/1.0/
Upstream-Name: phpggc
Upstream-Contact: contact@ambionics.io
Source: https://github.com/ambionics/phpggc

Files: *
Copyright: 2019 ambionics <contact@ambionics.io>
License: Apache License 2.0
 /usr/share/common-licenses/Apache-2.0

Files: debian/*
Copyright: 2019 Package Things <packageallthethings@gmail.com>
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
```

There will be a lot of clutter when you first open copyright, most can be deleted but be sure to read what you are removing as some information may be important. The copyright and license for `Files: *` will be whatever the original package uses. In this case, the original package used Apache License 2.0, and as it has the full license already in Debain it can be linked to as above. A good command to know of is `licensecheck -r . --copyright` which will give a rough idea on if there are any licenses that were missed.

```markdown
packaging@kali:~$ cat watch
version=4
opts=filenamemangle=s/.+\/v?(\d\S*)\.tar\.gz/phpggc-$1\.tar\.gz/ \
  https://github.com/ambionics/phpggc/tags .*/v?(\d\S*)\.tar\.gz
```

This file can look a bit intimidating, but what actually needs to be changed is very easy as demonstrated:

```markdown
opts=filenamemangle=s/.+\/v?(\d\S*)\.tar\.gz/PACKAGENAME-$1\.tar\.gz/ \
  ORIGINALGITLINK/tags .*/v?(\d\S*)\.tar\.gz
```

This file will _watch_ for any changes in released version.

## Final touches

If we built the package now, it would not be installed. To fix this, let's create an .install file and a helper script. The reason we are creating these two files is that they both will work the majority of the time. In some cases, the different ways, like using a symlink, may not work and changes will have to be made. As we can't account for every scenario now, we will go with what works the majority of the time.

```html
packaging@kali:~$ mkdir helper-script
packaging@kali:~$ nano phpggc.install
packaging@kali:~$ nano helper-script/phpggc
packaging@kali:~$ cat phpggc.install
lib usr/share/phpggc/
phpggc usr/share/phpggc/
templates usr/share/phpggc/
gadgetchains usr/share/phpggc/
debian/helper-script/phpggc usr/bin/
packaging@kali:~$ cat helper-script/phpggc
#!/bin/sh

cd /usr/share/phpggc
exec ./phpggc
```

Some of you may have caught something odd and are wondering what's up with the formatting of the .install file. With the way that the package builder interprets things, a `/` at the beginning of "usr/" will break things, likewise no slash at the end will as well. We include all the files that will be installed in the ".install" file. In the helper-script, we go to that directory and launch the file.

Now that all that is done, we can push everything to git and try it out!

```markdown
packaging@kali:~$ cd ..
packaging@kali:~$ pwd
/home/packaging/phpggc
packaging@kali:~$ git add .
packaging@kali:~$ git commit -m "Packaged up!"
packaging@kali:~$ git push
packaging@kali:~$ gbp buildpackage --git-builder=sbuild
```

This may take a little bit, and in the end a few things can occur. If lintian says "Failed" and there are errors, we recommend googling them and if no solution can be found then submit a post to the [forums](https://forums.kali.org/forumdisplay.php?8-Kali-Linux-Development) where we can assist. If lintian does not fail, then you can find your package in `/home/$USER/kali/build-area/`. Be sure to test it out by using dpkg to install the package and run it.

# Submitting to the tracker

Just one last thing to do; submit it to us! To do this, lets head over to [Kali's issue tracker](https://bugs.kali.org). We are going to want to submit a new issue with the category "New Tool Requests". For the title we will call it "phpggc: a library of unserialize() payloads along with a tool to generate them" and for the description we will include that list from earlier.

```markdown
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

## Menu icon

If a `.desktop` file is needed to be created for a menu icon, then this is best done by submitting a merge request to [the kali-menu package on GitLab](https://gitlab.com/kalilinux/packages/kali-menu). Fork the package, clone it, add in the file you'd like, and then you can submit a merge request with your changes. Below is an example of how the `.desktop`file should be done. Be sure to change "Categories" to whichever most closely fits the tool, and it is possible to include more than one.

```
packaging@kali:~$ nano desktop-files/phpggc.desktop
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
```

## What happens after we accept

When we accept the package into Kali, we duplicate the git repository into our kalilinux/packages GitLab group (through forking and deleting the relationship). Because of this, future changes made will need to be submitted as merge requests. In order to do this, users will have to fork our git repository into their account.
