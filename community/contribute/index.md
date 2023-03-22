---
title: Contribute to Kali
description:
icon:
weight:
author: ["gamb1t",]
---

## Kali Linux Packaging

### Package Updates

Occasionally there is a new upstream update for someone's favorite tool that we just can't get to in time before they notice. In these cases someone who knows how to update a package may help out and submit the new update as a merge request. This helps to get the package updated quicker for all users.

### Packaging New Tools

With the number of tools out there that users want to see in Kali, there is no way we can get them all on our own. A perk of us being on GitLab is that we can easily accept packages submitted by users, to learn more check out our [Public Packaging page](/docs/development/public-packaging/). If you would like to help but don't know of a package that should be done, we [have a list that can be chosen from](https://bugs.kali.org/search.php?project_id=1&category_id=Queued%20Tool%20Addition&sticky=on&sort=id&dir=ASC&per_page=9999&hide_status=80&match_type=0). A great example of a user-contributed package is [i3-dotfiles](https://gitlab.com/kalilinux/packages/i3-dotfiles) which is help maintained by [Arszilla](https://gitlab.com/Arszilla).

### Autopkgtests

When dealing with packages we may encounter updates that may break functionality. Something that helps to catch these problems are autopkgtests. These tests are defined by the packager, and can range from a simple help output test to a full package functionality test. While extremely helpful, they also take time to develop and in most cases require intimate knowledge of the tool being tested. For more information on the actual process of creating them, please refer to the [following docs page](/docs/development/contributing-runtime-tests/). It would be very helpful to have contributions to this effort of creating more advanced autopkgtests for all of the tools in Kali.

## Kali Linux Community

### Forums

If you want to get involved and help out others that are struggling, check out the official [Kali Linux Forums](https://forums.kali.org/). We do have doc pages on the [forums](/docs/community/kali-linux-community-forums/) that we encourage you to check out first. If you do decide to help out others, we encourage you to follow the example of a very active user named [Fred Sheehan](https://forums.kali.org/search.php?searchid=8162524). Fred helps others almost every day and points others to where they can find help if he doesn't know.

### Discord

Another great community where people commonly ask for help is the official [Kali Linux & Friends Discord](https://discord.kali.org/). While the Kali Linux & Friends Discord was not created to help troubleshoot issues or report bugs, often times there are people who come in for that purpose, and other members of the community often are able to help them out. While on the Discord, we encourage you to follow the example set by our trusted Community Moderators.

### Bug Tracker

The official [Bug Tracker](https://bugs.kali.org/) is a great place to look for things to do. If you are reporting a bug, we do have doc pages on the [bug tracker](/docs/community/submitting-issues-kali-bug-tracker/) that we encourage you to check out first. If you are instead attempting to help solve a bug or looking for a project to help out on, like helping with updating a package, you may need to refer to a different docs page.

## Kali Linux Documentation

### Kali Tools

Much like the Kali Docs, Kali Tools is a [markdown GitLab-based](https://gitlab.com/kalilinux/documentation/kali-tools) repository that can be viewed fully rendered at [this location](/tools/). If a page has incorrect information, or is lacking detailed usage methods, please submit a [merge request](https://gitlab.com/kalilinux/documentation/kali-tools/-/merge_requests) or an [issue](https://gitlab.com/kalilinux/documentation/kali-tools/-/issues) on GitLab.

### Kali Docs

You can contribute by helping us out right here! If there are any typos you notice, ideas you have, anything at all that could be beneficial then you can submit a request for the changes [over on GitLab](https://gitlab.com/kalilinux/documentation/kali-docs). Additionally, there are pages that we ask for help on. This can be found [by searching based off of labels](https://gitlab.com/groups/kalilinux/-/issues?scope=all&utf8=✓&state=opened&label_name%5B%5D=help-wanted). If ideas are needed, looking through the [merge requests](https://gitlab.com/kalilinux/documentation/kali-docs/-/merge_requests?scope=all&state=merged) is a good idea.

## Kali Linux Mirror Contributions

Mirrors are always good to have. The more we have, and the faster they are, the better. For more information about becoming an official Kali Linux mirror, check out our [Mirror Policy Page](/docs/community/setting-up-a-kali-linux-mirror/).
