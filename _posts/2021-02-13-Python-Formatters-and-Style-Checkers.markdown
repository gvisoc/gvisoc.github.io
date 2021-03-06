---
layout: post
title: Python Formatters and Style Checkers
date: 2021-02-12 21:30
tags:
- Python
- Git
excerpt_separator: <!--more-->
---
TL;DR: check [this repo](https://github.com/gvisoc/python-project-template).

Working in a software project, as part of a team, has quite a few challenges. Other than the cultural side of things, timezone differences, remote work, communication tools, and sharing the knowledge across the team members, there are a lot of technical bits that can create a lot of issues and friction at the source code level.

One of this bits can be the lack of a set of code style conventions and their enforcement.
<!--more-->

Having a consistent code style not only benefits the code readability, the Developer Experience, and the cohesion of the source code as a documentation source, but also prevents serious issues in Git. The eternal war between the tabs and the spaces, very personal code editor preferences, the line width, the kind of quotes used,... and a range of cosmetic changes to the code will make tools like Git detect false changes, that is, changes in the source code that don't contribute to the functionality. 

Those false changes can get a team very close to the merge-hell situations: at the end of a sprint, thousands of meaningless format changes are submitted alongside the real ones. Those would make the code visually inconsistent, and thus the whole contribution needs to be either rejected, or processed manually in a painful process that can take hours.

A way to avoid the above situations is the following:

- Select and enforce the use of a code formatter and a style checker. 
- Configure them with the project standards, and distribute such configurations across the team members. 
- Last, but not least, make sure that they are wired with the build tools in such a way that when a developer tries to contribute changes, then: 
  - the formatter ingest the changed files and reformats the code if needed,
  - the style checker verifies the result, and
  - if either the formatter changed a file, or the checker failed, the commit operation for the changes is stopped for human review.

This way, the developer can re-run the tests, inspect the style changes made by the formatter, fix any outstanding issue, and try again. The code will only be committed when the formater has nothing to do and the style check passes.

For Python, [Black](https://github.com/psf/black) and [Flake8](https://flake8.pycqa.org/en/latest/) are usual suspects. I came across a way to automate them in the pre-commit stage of Git, described in [this post by LJ Miranda](https://ljvmiranda921.github.io/notebook/2018/06/21/precommits-using-black-and-flake8/). I have consolidated all those in a [Python Project Template](https://github.com/gvisoc/python-project-template) at my GitHub account, with some more documentation and links.