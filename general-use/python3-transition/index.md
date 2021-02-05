---
title: Everything you need to know about the switch to Python 3
description:
icon:
type: post
weight: 15
author: ["rhertzog",]
---

### About the transition

Kali Linux fully switched to Python 3. This means that any tool packaged by
Kali that was using Python 2 has been either dropped or converted to use Python 3.
Any tool converted to Python 3 contains only scripts with
`/usr/bin/python3` as their shebang.

Concerning the packages that come straight from Debian, they did the same
on most packages, but there are a few exceptions where packages are
allowed to continue to rely on Python 2. However those packages
have been updated so that all their scripts use
`/usr/bin/python2` as their shebang and so that they depend on the
`python2` binary package (instead of the former `python`).

Thanks to those changes, Debian no longer need to provide
`/usr/bin/python` and recent upgrades will effectively get rid of that
symlink.

Unfortunately, when you download a Python script on the web, it will
likely have `/usr/bin/python` as its shebang. If you try to execute it
without fixing the shebang line, you will end up with an error like
this one:

```plaintext
zsh: /home/kali/test.py: bad interpreter: /usr/bin/python: no such file or directory
```

In Debian, you can reinstate the `/usr/bin/python` symlink by installing:

- `python-is-python2` if you want to have it point to `python2`
- `python-is-python3` if you want to have it point to `python3`

### Keeping backwards compatibility in Kali

Given the large number of users that would not know how to avoid the above
error, we decided that Kali would continue to ship Python 2 by default (as
long as Debian still provides it) and that `/usr/bin/python` would point
to it. We are also keeping a few common external modules (like `requests`)
so that random exploit scripts have a reasonable chance to run
successfully.

Howewer, pip for Python2 (aka python-pip) is gone, `/usr/bin/pip` is the
same as `/usr/bin/pip3` and it will install modules for Python 3. See the
FAQ below for more information.

This compatibility has been implemented by having `kali-linux-headless`
recommend `python2`, `python-is-python2` and `offsec-awae-python2` so that
they are installed by default while being removable by users that would
like to get rid of them.

The make users aware of this situation, we are displaying a promiment
message on login:
```plaintext
┏━(Message from Kali developers)
┃
┃ We have kept /usr/bin/python pointing to Python 2 for backwards
┃ compatibility. Learn how to change this and avoid this message:
┃ ⇒ https://www.kali.org/docs/general-use/python3-transition/
┃
┗━(Run “touch ~/.hushlogin” to hide this message)
```

The hope is that they will read this article and know how do deal with the
various issues that they will encounter.

### Frequently Asked Questions

#### I have downloaded a Python script, what should I do?

You need to inspect its shebang line. The shebang line is the first
line of a script and it starts with `#!` followed by the path
of the interpreter that will be used to execute the script.

If the interpreter is `/usr/bin/python`, you should read the documentation
to see whether the script can run with Python 3. If yes, then you should
update the shebang line to point to `/usr/bin/python3`.  Otherwise you
should update it to point to `/usr/bin/python2`.

Good shebang lines that can be kept:

- `#!/usr/bin/python3`
- `#!/usr/bin/python2`
- `#!/usr/bin/env python3`
- `#!/usr/bin/env python2`

Bad shebang lines that need to be updated:

- `#!/usr/bin/python`
- `#!/usr/bin/env python`

#### How can I get rid of the login message?

The message will only be displayed as long as `/usr/bin/python` points to
the deprecated Python 2. Now that you know about this situation and that
you know how to fix the shebang line of old scripts, you can safely get
rid of `/usr/bin/python`:

```console
kali@kali:~$ sudo apt remove python-is-python2
```

Or you can decide to make it point to Python 3:

```console
kali@kali:~$ sudo apt install python-is-python3
```

Either of those actions will get rid of the login message.

Alternatively, if you want to keep `/usr/bin/python` pointing to
`python2` and you still want to disable the message, you can do
this:

```console
kali@kali:~$ mkdir -p ~/.local/share/kali-motd
kali@kali:~$ touch ~/.local/share/kali-motd/disable-old-python-warning
```

#### I have a Python 2 script that doesn't run, what should I do?

If your Python 2 script uses modules that are not among those that we ship
in the `offsec-awae-python2` compatibility package (see [list
here](https://gitlab.com/kalilinux/packages/offsec-courses/-/tree/kali/master/python2-wheels)),
then you can try `pyenv` to setup a fully isolated Python 2 environment
where you can use pip to install supplementary modules. See our [Using EoL
Python versions on Kali](/docs/general-use/using-eol-python-versions/) article.

#### I want pip for Python 2, how can I get it back?

Try `pyenv`. See our [Using EoL Python versions on
Kali](/docs/general-use/using-eol-python-versions/) article.

#### I wrote a Python script, what should I do?

Be nice with end users:

- document clearly whether your code runs with Python 3 or Python 2
- use `/usr/bin/python3` or `/usr/bin/python2` as the shebang line,
  it's more expressive than `/usr/bin/python` and is more likely to have
  the desired result
- update it for Python 3 compatibility if that's not the case yet






## Python2 & PIP

@g0tmi1k, I'm sure I remember something being said. but is it now covered in the kali-docs?

```
@Gamb1t-Joe-O Looking at https://www.kali.org/docs/general-use/using-eol-python-versions/ I wonder how that interact with sudo when you want to run an old python2 script as root. It might be worth saying a word about this and in particular clearly indicate that they should never ever run "sudo pip" as that would likely not use pyenv and use the system wide script and install stuff that would conflict with packaged modules.
kali.orgkali.org
Using EoL Python Versions on Kali | Kali Linux Documentation
Official documentation of Kali Linux, an Advanced Penetration Testing Linux distribution used for Penetration Testing, Ethical Hacking and network security assessments.

BTW now that we have documented the above, we have removed python-pip (i.e. the python2 version of pip) and users calling "pip" will install stuff for Python 3.
```
