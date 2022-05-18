---
title: Handling common APT problems
description:
icon:
weight:
author: ["gamb1t","rhertzog",]
draft: true
---

This page is due for more content as issues come up that need addressing. If you have an issue with APT that is not addressed in this page, please create a [merge request](https://gitlab.com/kalilinux/documentation/kali-docs/-/merge_requests) with the steps to solve the problem or address it, or an [issue](https://gitlab.com/kalilinux/documentation/kali-docs/-/issues) if you are unaware of how to solve the problem but would like to see it added to this page.

##### Package is to be installed, however it is not going to be

```
┌──(kali㉿kali)-[~]
└─$ sudo apt upgrade                                                                                   100 ⨯
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

This is a common "issue" that an APT user may experience. This however is not a true error. If you run into an issue with running `apt upgrade` where a package is not going to be installed, there are a couple ways to fix it.

The first, and proper, way to fix this issue is to install the package manually and then run the command again.
The second, more risky, way to fix this is to run `apt full-upgrade` instead, which will install and remove any packages it sees fit. This may cause some issues for the user depending on what it removes. Use with caution, and know what you are doing when you run it.

##### A package needs a newer version, but there is no new version available

This will likely happen more often when using kali-dev or kali-experimental, and may happen for two reasons. The first is that the package is still being tested, and this will make its way into the repo soon. The second is that there are conflicts with some dependencies, and these will need to be resolved first.

To figure out which may be the case in your situation, you can look up the package's page on Debian's side or if it belongs to Kali see if there are any bug reports on [bugs.kali.org](https://bugs.kali.org). Either way, the issue will be sorted out and will likely be resolved within a couple of weeks in most cases.

##### A package is trying to overwrite a file and causing an error

This can occur when files move between packages and don't include the proper packaging field set to tell it that it is a replacing something else. In most cases this can be solved by retrying the installation or upgrade. In other cases, the APT command can have `-o dpkg::options::="--force-overwrite"` added in order to tell dpkg to ignore the overwrite error.

For extra information and a more detailed understanding of this error, you can read about it [here](https://raphaelhertzog.com/2011/08/01/understanding-dpkgs-file-overwrite-error/)
