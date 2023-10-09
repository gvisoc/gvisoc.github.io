---
layout: post
title:  "Fix: Firefox not  Opening Links from Other Gnome Applications"
date:   2021-11-25 17:20:00 +1100
categories:
- linux
hidden: true
permalink: /linux/2021/11/25/Firefox-Open-Issue.html
excerpt_separator: <!--more-->
---
Firefox is my browser of choice. With the latest versions of the browser and of Gnome, it is not working as expected when clicking a link from another application.

If Firefox is already running, and you click on a link in any other application, such link should open in a new tab. Instead, you can get the following error:

>Firefox is already running, but is not responding. To use Firefox, you must first close the existing Firefox process, restart your device, or use a different profile.

![Firefox is already running, but is not responding. To use Firefox, you must first close the existing Firefox process, restart your device, or use a different profile.](/assets/firefox_remote.png)

<!--more-->

This is solved with the following lines in `/etc/environment`:

```
MOZ_ENABLE_WAYLAND=1  # Neded if you're using Wayland and not Xorg/X11.
MOZ_DBUS_REMOTE=1     # Fixes the problem
```
You will need administrator permissions to edit that file.

This environment configuration should be automatically performed by the package of Firefox, when installed. While it has such an easy solution, this bug has been around for a while. 

I hope this helps!

**Credit**: this solution has been found in [this Manjaro Forum answer](https://forum.manjaro.org/t/firefox-does-not-open-link-from-other-applications/75504/7), rather buried within. Posting for easier finding.