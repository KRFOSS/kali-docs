---
title: Installing Python Applications via pipx
description:
icon:
weight: 16
author: ["arnaudr",]
---

## Introduction: say good-bye to `pip install`

Starting Kali Linux 2024.4, using `pip` to install external Python packages _is strongly discouraged_. Instead, we recommend using `pipx`. On the surface, it provides a similar user experience, but under the hood it overcomes the one outstanding issue with pip: the lack of environment isolation.

If you try to use `pip` to perform system-wide installs (`sudo pip install`) or user home directory installs (`pip install --user`), you'll get this message:

```plaintext
┌──(kali㉿kali)-[~]
└─$ sudo pip install xyz
error: externally-managed-environment

? This environment is externally managed
╰─> To install Python packages system-wide, try apt install
    python3-xyz, where xyz is the package you are trying to
    install.
    
    If you wish to install a non-Kali-packaged Python package,
    create a virtual environment using python3 -m venv path/to/venv.
    Then use path/to/venv/bin/python and path/to/venv/bin/pip. Make
    sure you have pypy3-venv installed.
    
    If you wish to install a non-Kali-packaged Python application,
    it may be easiest to use pipx install xyz, which will manage a
    virtual environment for you. Make sure you have pipx installed.
    
    For more information, refer to the following:
    * https://www.kali.org/docs/general-use/python3-external-packages/
    * /usr/share/doc/python3.12/README.venv

note: If you believe this is a mistake, please contact your Python
installation or OS distribution provider.  You can override this,
at the risk of breaking your Python installation or OS, by passing
--break-system-packages.
hint: See PEP 668 for the detailed specification.
```

The reason for this change, in short, is that using both `apt` (Kali's package manager) and `pip` to install Python packages on a Kali system has never really been supported. Given that both `apt` and `pip` installs Python packages in the same environment, they basically step on each others toes, and it can quickly become a broken mess. [pipx](https://pipx.pypa.io/) is the solution to this problem: please use it.

For longer (and more formal) explanations, you can refer to the [PEP 668 – Marking Python base environments as externally managed](https://peps.python.org/pep-0668/), or for something shorter, our blog post [Pip install and Python's externally managed](/blog/python-externally-managed/).

Below, we'll follow the suggestions from the error message above, and give concrete examples.

## Prefer installing packages and programs via APT

Always check if the Python program that you're looking for is already packaged in Kali Linux, and if so, install it with APT.

For an example, let's check [Faraday's README](https://github.com/infobyte/faraday). The page mentions several installation methods, among which Docker images, a PyPi package (installed via `pip`), or installing distro packages published by Faraday itself.

Before doing any of that, we can check if Faraday is already packaged in Kali Linux with:

```plaintext
┌──(kali㉿kali)-[~]
└─$ apt search faraday
[...]
```

The output is a bit too long, so let's match only package names that start with `faraday`:

```plaintext
┌──(kali㉿kali)-[~]
└─$ apt search faraday | grep ^faraday
faraday/kali-rolling 5.7.0-0kali1 all
faraday-agent-dispatcher/kali-rolling 3.2.1-0kali2 all
faraday-cli/kali-rolling 2.1.8-0kali1 all
```

We're getting there: there's a package named `faraday`, let's check if it's indeed what we're after:

```plaintext
┌──(kali㉿kali)-[~]
└─$ apt show faraday
Package: faraday
[...]
Homepage: https://faradaysec.com
[...]
Description: Collaborative Penetration Test IDE
[...]
```

Indeed, it's the right one! We can install it simply with:

```plaintext
┌──(kali㉿kali)-[~]
└─$ sudo apt install faraday
```

Done!

## Not packaged in Kali? Too old in Kali? Install it with pipx

In this example, we're going to install [XSStrike](https://github.com/s0md3v/XSStrike). This time, APT returns nothing:

```plaintext
┌──(kali㉿kali)-[~]
└─$ apt search xsstrike
```

So we're going to install it with `pipx.` This assumes that the project is published on the Python Package Index, and [indeed it is](https://pypi.org/project/xsstrike/).

Installation is pretty straightforward:

```plaintext
┌──(kali㉿kali)-[~]
└─$ pipx install xsstrike
  installed package xsstrike 3.2.2, installed using Python 3.12.6
  These apps are now globally available
    - xsstrike
done!
```

That's all, now we can run it:

```plaintext
┌──(kali㉿kali)-[~]
└─$ xsstrike -h

usage: xsstrike [-h] [-u target] [--data paramdata] [-e encode] [--fuzzer]
                [--update] [--timeout timeout] [--proxy] [--crawl] [--json]
                [--path] [--seeds args_seeds] [-f args_file] [-l level]
                [--headers [add_headers]] [-t threadcount] [-d delay]
                [--skip] [--skip-dom] [--blind]
                [--console-log-level {debug,info,run,good,warning,error,critical,vuln}]
                [--file-log-level {debug,info,run,good,warning,error,critical,vuln}]
                [--log-file log_file] [-n payload_count]
[...]
```

And it works already!

## Pipx troubleshooting

### Install pipx

From Kali Linux 2024.4, `pipx` should be pre-installed. If ever it's not the case, you can install it via `apt` as usual:

```plaintext
┌──(kali㉿kali)-[~]
└─$ sudo apt install -y pipx
```

### Add `~/.local/sbin` to the path

`~/.local/bin` is the directory where `pipx` installs Python applications. It needs to be in the `PATH` environment variable, so that when you install, for example, an application `xyz` via pipx, you can then run it simply by typing `xyz` in the terminal.

From Kali Linux 2024.4, `~/.local/bin` should already be in the `PATH`. You can check that by opening a terminal and running the command:

```plaintext
┌──(kali㉿kali)-[~]
└─$ echo $PATH
/home/kali/.local/bin:[...]
```

If you see `/home/kali/.local/bin` somewhere in the output, it's all good.

If for some reason it's not there, you might get this message after installing a program with `pipx`:

```plaintext
┌──(kali㉿kali)-[~]
└─$ pipx install xyz
  installed package xyz 1.0, installed using Python 3.12.6
  These apps are now globally available
    - xyz
   Note: '/home/kali/.local/bin' is not on your PATH environment variable.
    These apps will not be globally accessible until your PATH is updated.
    Run `pipx ensurepath` to automatically add it, or manually modify your
    PATH in your shell's config file (e.g. ~/.bashrc).
done!
```

Contrary to what the messagae says, there should be no need to run `pipx ensurepath`. Instead, just log out, then log back in. Then open a terminal, run `echo $PATH`, and you should see `/home/kali/.local/bin` somewhere in the output.

If for some very mysterious reason it's not there, then maybe it's time to run `pipx ensurepath` and follows the instructions.
