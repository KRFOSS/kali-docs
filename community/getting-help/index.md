---
title: How And Where To Get Help
description:
icon:
weight:
author: ["gamb1t",]
---

This doc shares some of the same information as our [basics of troubleshooting doc](/docs/troubleshooting/basic-troubleshooting/). If you feel like you do not need a lot of extra information or explanation, basics of troubleshooting may be the doc for you.

If you believe something is missing or could be improved, please submit an issue to help to try and get this to be as complete as possible.

## Local Documentation

The first item that should always be checked when an issue is encountered is the man page. Man pages will contain helpful information such as command switches available and what they do, descriptions on parts of the tool, and additional man pages that can be read. To access a man page, type `man` followed by the command that you are requiring assistance on. Keep in mind not every command will have a man page. In those cases, try running the command followed by `--help`, `-help`, `-h`, or `-H`.

One thing to note about man pages is that sometimes names will conflict with other commands. In these cases it is helpful to know the organization structure:

1. Commands that can be executed from the command line
2. System calls (functions provided by the kernel)
3. Library functions (provided by system libraries)
4. Devices (on Unix-like systems, these are special files, usually placed in the /dev/ directory)
5. Configuration files (formats and conventions)
6. Games
7. Sets of macros and standards
8. System administration commands
9. Kernel routines

The `read` command is a good example of this as it is a system call as well as a command. Running `man read` will go for the earliest available page, meaning the command, so it will show as read(1). To get information on the system call we would have to run `man 2 read`.

Another helpful tool to have is `pinfo`. Certain programs may have a manual written for it in the _info_ format, and `pinfo` is a good way to view them. Alternatively, `yelp` or `konqueror`. If there is not anything of the sort available it will fall back to the tool's man page.

Packages also typically have specific documentation that they put out along with their tool. This can be found most commonly in **/usr/share/doc/_package_**. In other cases, the documentation might be in a separate package. Commonly this will be **_package_-doc**.

Should you need access to a command and you cannot remember the name of that command, `apropos` will help you find it. It returns the command name and man page section. It searches through the summary of man pages looking for the key words that you supply, which is also what is presented:

```console
kali@kali:~$ apropos "copy file"
cp (1)               - copy files and directories
cpio (1)             - copy files to and from archives
install (1)          - copy files and set attributes
ntfscp (8)           - copy file to an NTFS volume.
kali@kali:~$
```

## Online Documentation

Asking questions online can receive some pretty unhelpful responses in certain cases. Most famously, RTFM, a.k.a. read the fine manual _(Which is the friendlier version)_. Eric Raymond has a [guideline](http://catb.org/~esr/faqs/smart-questions.html) which is a good way to avoid the most common mistakes and get useful answers.

When you encounter an error or have a question and need to seek help, you want to be sure to find the right sources of information. In many cases there should be some form of error or error log produced if there is a bug or issue encountered. This might be found in **/var/log**.

A few places of note to check:

- Anywhere mentioned in the tool's documentation
- **/var/log/syslog**
- `dmesg`
- If it is a service, `systemctl status package-with-issue`
- The tools website

In order to find the tool's website what can be done is utilizing search engine's advanced search functions and ways of completing requests. Should you need information on the brute forcing tool Hydra, you could search any of the following: "Hydra linux", "Hydra hacking", "Hydra brute force", "Hydra Kali", "Hydra pentest", etc. This can also be used for errors, if you search a snippet of the error you can utilize extra key-words or quotes to force certain things to be in the results. Keep in mind sometimes an error might not be the fault of that tool however, and quotes may remove the actual answer you need.

_StackOverflow and similar sites/forums, tool's homepages, bug reports, and studying write-ups have been in my, Gamb1t's, opinion the best sources online for information. Twitter, Reddit, Discords, etc all have their place and have been useful now and then however they are the last place I will consult. To best learn about a topic or solve a problem, I will read and read to try and fully understand where an issue is. Again, this is my opinion and each person will learn in their own way. However this is not always going to work, and occasionally issues will have to be figured out individually._

## Troubleshooting

When all options are exhausted and something still isn't known or resolved, troubleshooting and learning by doing is sometimes necessary. In these cases, there are a few things to remember to prevent damaging anything important.

When possible, create a VM just for the purpose of learning something, and try and see if you can replicate the issue in that VM if that is the case. If you are using a VM, remember to utilize snapshots. In cases where VMs are not a possibility or the problem cannot be recreated there, be sure to backup any important files if you believe they may be altered in any way. Commenting out lines in files that you are altering is also a good idea. Utilizing the previous information, you likely would have a really good idea of what is going on with the tool and where things are at. Use this to pinpoint the issue and attempt to resolve it. Configuration files, launch scripts, missing files, etc all can be frustrating to work with but the next time you have a similar issue the resolution time will be quicker.

## Reporting

Always remember to report bugs to where they should be. Kali Linux manages certain packages, and Debian manages others. If the tool creator's GitHub/GitLab cannot be found then reporting to Kali Linux or Debian would be a good replacement, however in the majority of cases the GitHub/GitLab will be able to be found. Doing `apt show package` will list a homepage which may link to the GitHub/GitLab. In the case that either their homepage does not or the `apt show` does not show a homepage, Google will likely produce results.

In the case of reporting a bug to Kali Linux, we have a doc on just [that topic](/docs/community/submitting-issues-kali-bug-tracker/).
