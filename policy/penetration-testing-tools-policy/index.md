---
title: Penetration Testing Tools Policy
description:
icon:
date: 2019-10-26
type: post
weight: 100
author: ["g0tmi1k",]
tags: ["",]
keywords: ["",]
og_description:
---

## Kali Linux Tools Policy

One of the key tasks in transitioning from Backtrack Linux to Kali was combing through the packages and selecting the "best of breed" from what was available.

We realize that there are many tools or scripts that can do the same job. Some are clearly better than others in some respect, some are more a matter of personal preference. With this in mind, keeping an updated, useful penetration testing tool repository is a challenging task. The Kali Development team uses some of these questions to help decide whether a specific tool should be included in Kali Linux.

* Is the tool useful/functional in a Penetration Testing environment?
* Does the tool overlap functionality of other existing tools?
* Does the licensing of the tool allow for free redistribution?
* How much resources does the tool require? Will it work in a "standard" environment?

The answers to questions such as these, among other considerations, help us come to a decision whether the tool should be included in Kali.

Most of the members of the Kali development team are working penetration testers, and we rely on our combined experience and expertise to select the best tools to add the most value to the Kali distribution as we continue its development.

Tools which are specifically aimed at DOS, DDOS or anonymity are _rarely used in legitimate engagements_, and are therefore _not installed by default_ in Kali Linux.

## New Tool Requests

We are always open to adding new and better tools to our distribution, but we ask that a case be made for each tool. Please put some thought and effort into the tool submission, and please do _not_ just send the developers a one line request. Submissions for new tool requests can be made through our [Kali Linux bug tracker](https://bugs.kali.org).
