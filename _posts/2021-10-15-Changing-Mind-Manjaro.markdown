---
layout: post
title:  "I've changed my mind about Manjaro"
date:   2021-10-15 10:20:00 +1000
categories:
- personal
- linux
excerpt_separator: <!--more-->
---
I've changed my mind about Manjaro.

About two weeks ago I noticed some warnings where `pacman`, the CLI package management tool, was warning me about a couple of installed packages that were newer than the ones available in the repositories. 

How that could be? Where did those packages come from, if not from Manjaro official repositories? And why were they *newer*? 
<!--more-->

I did some terminal fu, and I found out that those packages were declared as dependencies from an [AUR](https://aur.archlinux.org/) package. I was lucky they were just cosmetic, theme-like packages.

I searched the web a bit and I came across [this post](https://www.hadet.dev/Manjaro-Bad/). 

To summarise: 

- **Manjaro** is an Arch derivative that **lags behind Arch about a week**, to perform some extra testing and ensure stability. 
- If you[ dig into Manjaro documentation](https://wiki.manjaro.org/index.php/Arch_User_Repository), you may find that its developers discourage you from using AUR. But it turns out that the operating system makes it so easy to use, and without so much of a warning, that **most users end up using AUR a lot**. Most of the users I know and I've read from at the forums use AUR, and they see the AUR availability as an advantage of Manjaro. I bet the do without knowing that it can break your system, or why.
- **Why AUR can break your Manjaro system?** Because AUR is not held back the same week as the official packages. Also, if an AUR package's needs dependencies they may very well be pulled from AUR as well, instead of being pulled from Manjaro's repositories. This may lead to some catastrophic inconsistencies in the system if those newer AUR variants replace core system packages, and are not backwards compatible.

This last point was the reason behild the warnings I was referring at the begining of this post.

As I find the overall problem a result of am overal bad practice and worse communication from Manjaro, I have decided to step away from this distribution and re-think.

Yesterday I begun trying Fedora 34 instead of falling back to Debian (sort of my lifelong system), and it feels good for now. It looks also a much better fit for my laptop, as I didn't have to do as much [post-install tinker as with Manjaro](https://gvisoc.com/personal/linux/2021/08/19/Manjaro-Post-Install.html).
