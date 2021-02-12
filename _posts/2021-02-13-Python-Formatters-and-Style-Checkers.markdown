---
layout: post
title: Python Formatters and Style Checkers
date: 2021-02-12 12:24
tags:
- Python
- Git
excerpt_separator: <!--more-->
---
Working in a software project, as part of a team, has quite a few challenges. Other than the cultural side of things, timezone differences, remote work, communication tools, and sharing the knowledge across the team members, there are a lot of technical bits that can create a lot of issues and friction at the source code level.

One of this bits can be the lack code conventions and style enforcement.

TL;DR: check [this repo](https://github.com/gvisoc/python-project-template).
<!--more-->

Having a consistent code style not only benefits the code readability, the Developer Experience, and the cohesion of the source code as a documentation source, but also prevents serious issues in Git. The eternal war between the tabs and the spaces, very personal code editor preferences, the line width, the kind of quotes used,... and a range of cosmetic changes to the code will make tools like Git detect false changes, that is, changes in the source code that don't contribute to the functionality. 

These false changes will get the team closer to the merge-hell situations where, at the end of a sprint, there are thousands of changes to merge and hundreds of them are meaningless format changes that makes everything difficult and visually inconsistent, and the whole contribution needs to be either rejected, or processed manually.

A way to avoid the above situations is the use of a code formatter and a style checker in the project, and wire them in such a way that if a developer tries to contribute changes that don't respect the code style of the project, the code is attempted to be automatically reformatted, and rejected automatically if after such attempt it's still not compliant.

For Python, [Black](https://github.com/psf/black) and [Flake8](https://flake8.pycqa.org/en/latest/) are usual suspects. I came across a way to automate them in the pre-commit stage of Git, described in [this post by LJ Miranda](https://ljvmiranda921.github.io/notebook/2018/06/21/precommits-using-black-and-flake8/). I have consolidated all those in a [Python Project Template](https://github.com/gvisoc/python-project-template) at my GitHub account, with some more documentation and links, and it is ready to clone and reuse.