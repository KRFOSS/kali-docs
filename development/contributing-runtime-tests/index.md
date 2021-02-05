---
title: Contributing run-time tests with autopkgtest
description:
icon:
type: post
weight:
author: ["gamb1t",]
---

## Why Kali could benefit from your help

With Kali Linux being a [rolling distribution](/docs/general-use/kali-branches/) it will occasionally have packages that are not stable. To best combat this, we have set up [automated runtime tests](https://autopkgtest.kali.org/) that have to be passed in order to allow the packages to migrate into [kali-rolling](/docs/general-use/kali-branches/). However, most packages at this time don't have a comprehensive autopkgtest associated with it causing this process to not reach its full potential. This is where the community can help us, by contributing test for packages that lack them.

### A bit of autopkgtest background

Autopkgstests were created as a way to test a package as accurately as possible on a real Debian system. This method can leverage virtualization and containers to create an accurate and re-usable environment that will be the testing area. There is a lot more information available on this topic. For a more in depth look than what will be covered here please see the following articles (which served as sources of information during writing):

* [The official Debian CI doc page](https://ci.debian.net/doc/file.TUTORIAL.html)
* [A great talk covering autopkgtests](http://meetings-archive.debian.net/pub/debian-meetings/2015/debconf15/Tutorial_functional_testing_of_Debian_packages.webm)
* [A paper going in-depth on writing As-Installed tests](https://gitlab.com/terceiro/installed-tests-patterns/raw/pdf/final/installed-tests-patterns.pdf)
* [The best practices Debian wiki page](https://wiki.debian.org/ContinuousIntegration/AutopkgtestBestPractices)

We encourage users to spend some time looking through these after finishing reading the rest of this doc. We will only be really covering a small portion of what is a large topic to explore and learn.

For more information on the environment itself, the `autopkgtest-build-qemu` and `autopkgtest-virt-qemu` man pages are beneficial. As are the docs located at `/usr/share/doc/autopkgtest/`.

### System Prep

Before getting started with writing tests, we need to get our environment set up. So we can validate our test once it is written.

```console
kali@kali:~$ sudo apt install -y autopkgtest vmdb2
kali@kali:~$
kali@kali:~$ sudo mkdir /srv/autopkgtest-images/
kali@kali:~$
kali@kali:~$ sudo autopkgtest-build-qemu kali-rolling /srv/autopkgtest-images/kali-rolling.img http://http.kali.org/kali
kali@kali:~$
```

### Learning by examples

There are a few things to learn about with autopkgtests that we can see in action already. We will look at what tests can look like, what types are more beneficial, and a few tricks that we can see.

The [cloud_enum](https://gitlab.com/kalilinux/packages/cloud-enum/-/tree/kali/master/debian/tests) control file looks like the following:

<p class="codeblock-label">cloud_enum control file</p>

```
Test-Command: cloud_enum --help
Depends: @
Restrictions: superficial
```

Whereas the [python-pip](https://gitlab.com/kalilinux/packages/python-pip/-/tree/kali/master/debian/tests) control file looks like the following:

<p class="codeblock-label">python-pip control file</p>

```
Tests: pip3-root.sh
Restrictions: needs-root

Tests: pip3-user.sh
Restrictions: breaks-testbed

# https://bugs.debian.org/cgi-bin/bugreport.cgi?bug=823358
Tests: pip3-editable.sh
```
With includes a test that looks like:

<p class="codeblock-label">python-pip pip3-user.sh test file</p>

```
#!/bin/sh

export HOME=$AUTOPKGTEST_TMP
export PATH=$PATH:$HOME/.local/bin
export PIP_DISABLE_PIP_VERSION_CHECK=1

if [ $(id -u) = 0 ]
then
    adduser --quiet --system --group --no-create-home testing
    user=testing
    mkdir $HOME/.cache
    chown -R $user $HOME
    runuser="runuser -p -u $user --"
else
    runuser=""
fi

$runuser python3 -m pip install world
$runuser python3 -m pip list --format=columns
$runuser python3 -m pip show world
ls -ld $HOME/.local/lib/python3.*/site-packages/world-*.dist-info
$runuser python3 -m pip uninstall -y world
$runuser python3 -m pip list --format=columns
# Temporarily disabled.  See #912379
#$runuser python3 -m pip list --outdated
if [ $(id -u) = 0 ]
then
    deluser --quiet testing
fi
```

It is apparent that there is a big difference between the two. Both will technically create a pass or a fail, and one might think that the first option, the cloud_enum control file, seems easier and better to use. However, this is a superficial test which means "The test does not provide significant test coverage, so if it passes, that does not necessarily mean that the package under test is actually functional". Because of this, we would rather have the python-pip test environment. It is more detailed and includes tests of different types of end-user environments.

We did just throw a bunch of text and code at you all to look at without much explanation, as it was important to note the differences before getting too involved. But lets take a step back now and look more closely at this.

Looking more closely at the `cloud_enum` control file, we have three things going on.

```
Test-Command: cloud_enum --help
Depends: @
Restrictions: superficial
```
The first thing we do is tell what command we are running. Because this is just a superficial test, we do not tell it to run a script and instead just do one line to test if it outputs the help. We then tell it to depend on the same dependencies that the package depends on, by using '@'. Should the test need other dependencies, we can list them here. In another test we will look at, this will be seen in action. We finally tell it that yes, this test is just a superficial one. We could tell it other things, like that `cloud_enum` needs root, or that after running this test the testing environment will need to be scrapped.

The next control file we will learn from is [pytest-factoryboy's](https://gitlab.com/kalilinux/packages/pytest-factoryboy/-/tree/kali/master/debian/tests).

```
Tests: test3-pytest-factoryboy
Depends: @, python3-pytest-pep8
```
As we can see, it depends on a python module. This python module will help us to test the tool, as we can see being done in the test:

```
#!/bin/sh
set -e
cp -r tests "$AUTOPKGTEST_TMP/" && cd "$AUTOPKGTEST_TMP"
for py in $(py3versions -i); do
    $py -Wd -m pytest -v -x tests 2>&1;
done
```
We can also see something else being done that is a good practice. `$AUTOPKGTEST_TMP` can be used during these tests, and will help to clean a system and start on a next test, assuming the `breaks-testbed` flag is not set.

Another package that can show a lot of info is [hyperion](https://gitlab.com/kalilinux/packages/hyperion/-/tree/kali/master/debian/tests). However, due to the size of the files and the breakdowns associated that will not be explained here.

### What to do

So you have a system set up, have seen tests in practice, but now you need to actually write the tests. Once you find a package that has no test or a superficial test, you should learn the tool. Let us say that we have a tool that pings a server and then depending on the response will tell the user something. What does a fail look like? What does a success look like? Are there any edge cases that might mess up your test?

The next step after learning about a tool's purpose is to figure out what you will need to tell autopkgtest. Using our tool from the previous example, will it need root to ping properly? What else might need to be done with the machine or environment to get the tool to work? What about after the test? Will there be an output file, and so therefore you should use $AUTOPKGTEST_TMP?

Once you learn and note what you can about the tool, you should start to write the test using the best practices and tips given earlier. There are many available resources that stem from those resources, and lots more packages that have solid tests that you can learn from example with if need be.

Running the tests after you have written them is fairly simple. All you have to do is the following command; `autopkgtest -u debci ~/packages/mytest/ -- qemu kali-rolling` and point to the directory that contains the package you want to test. There are many other variations which will potentially be needed, and can be found at /usr/share/doc/autopkgtest/README.running-tests.html.
