---
title: Using EoL Python Versions on Kali
description:
icon:
date: 2020-09-02
type: post
weight:
author: ["gamb1t",]
tags: ["",]
keywords: ["",]
og_description:
---

In December of 2019 we released a [blog post](https://www.kali.org/news/python-2-end-of-life/) talking about how we will deal with Python 2's End-of-Life. Since then there has been quite a lot of tools that users use that have not been ported to Python 3, causing issues when they try to use them. This page will cover a way of using the depreciated version in a safe way.

## pyenv

Python 2 is no longer being maintained in the Debian repositories. This means that we must find a way to work around this issue. `pyenv` solves this problem by allowing us to install multiple versions of Python that do not conflict with each other. Currently it is not in the Debian or Kali repository, so we will need to install it from source. Thankfully, there is a handy [installation script](https://github.com/pyenv/pyenv-installer) that the authors have released. Let's go through the installation and setup together.

```
kali@kali:~$ sudo apt-get install -y build-essential libssl-dev zlib1g-dev libbz2-dev libreadline-dev libsqlite3-dev wget curl llvm libncurses5-dev libncursesw5-dev xz-utils tk-dev libffi-dev liblzma-dev python3-openssl git
...
SNIP
...
kali@kali:~$
```

We will next be quickly running the bash install script. If `ZSH` is the default shell we will have to edit the `.zshrc` file after this.

```
kali@kali:~$ curl https://pyenv.run | bash
...
SNIP
...
kali@kali:~$
```

If we are using `ZSH` then we will now add the proper lines to our `.zshrc`.

```
echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.zshrc
echo 'export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.zshrc
echo -e 'if command -v pyenv 1>/dev/null 2>&1; then\n  eval "$(pyenv init -)"\nfi' >> ~/.zshrc
```

Lets continue with the setup

```
kali@kali:~$ exec $SHELL
kali@kali:~$
kali@kali:~$ pyenv
pyenv 1.2.20
Usage: pyenv <command> [<args>]

Some useful pyenv commands are:
   activate    Activate virtual environment
   commands    List all available pyenv commands
   deactivate   Deactivate virtual environment
   doctor      Verify pyenv installation and development tools to build pythons.
   exec        Run an executable with the selected Python version
   global      Set or show the global Python version(s)
   help        Display help for a command
   hooks       List hook scripts for a given pyenv command
   init        Configure the shell environment for pyenv
   install     Install a Python version using python-build
   local       Set or show the local application-specific Python version(s)
   prefix      Display prefix for a Python version
   rehash      Rehash pyenv shims (run this after installing executables)
   root        Display the root directory where versions and shims are kept
   shell       Set or show the shell-specific Python version
   shims       List existing pyenv shims
   uninstall   Uninstall a specific Python version
   --version   Display the version of pyenv
   version     Show the current Python version(s) and its origin
   version-file   Detect the file that sets the current pyenv version
   version-name   Show the current Python version
   version-origin   Explain how the current Python version is set
   versions    List all Python versions available to pyenv
   virtualenv   Create a Python virtualenv using the pyenv-virtualenv plugin
   virtualenv-delete   Uninstall a specific Python virtualenv
   virtualenv-init   Configure the shell environment for pyenv-virtualenv
   virtualenv-prefix   Display real_prefix for a Python virtualenv version
   virtualenvs   List all Python virtualenvs found in `$PYENV_ROOT/versions/*'.
   whence      List all Python versions that contain the given executable
   which       Display the full path to an executable

See `pyenv help <command>' for information on a specific command.
For full documentation, see: https://github.com/pyenv/pyenv#readme
kali@kali:~$
```

We can now install Python 2 and set it as our default Python version:

```
kali@kali:~$ pyenv install 2.7.18
Downloading Python-2.7.18.tar.xz...
-> https://www.python.org/ftp/python/2.7.18/Python-2.7.18.tar.xz
Installing Python-2.7.18...
Installed Python-2.7.18 to /home/kali/.pyenv/versions/2.7.18

kali@kali:~$
kali@kali:~$ pyenv global 2.7.18
kali@kali:~$
kali@kali:~$ pyenv versions
  system
* 2.7.18 (set by /home/kali/.pyenv/version)
kali@kali:~$
kali@kali:~$ python
Python 2.7.18 (default, Apr 20 2020, 20:30:41)
[GCC 9.3.0] on linux2
Type "help", "copyright", "credits" or "license" for more information.
>>>
kali@kali:~$
```

We can now install dependencies as needed for whatever tools we are using. When we want to swap back to Python 3, we just need to set the global to be system.

One thing to keep in mind is to stick with installing the dependencies via `pip`. `apt` will not be very kind if you are trying to install Python 2 dependencies through it and through pip, so just stick with pip in this case.