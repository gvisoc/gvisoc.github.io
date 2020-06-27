---
layout: post
title:  "From macOS to Plasma: Making Myself at Home"
date:   2020-06-27 12:20:00 +1000
categories:
  - "personal"
  - "linux"
excerpt_separator: <!--more-->
---
I have been a Mac user for the last few years and, while I have been a GNU/Linux user for more than 22, I have to say that some of the macOS desktop conventions very well matched my needs, and rooted very deep in my work-related workflows, carving themselves into my brain.

I recently changed my great MacBook Pro for another great machine, a Lenovo ThinkPad T490s. I have chosen Debian with Plasma by KDE (formerly just "KDE") as the desktop environment. It is known that Plasma is much closer to the Windows 10 usability conventions than to the macOS', so I needed to enable a few features to make myself at home. 

This is a very personal post to detail how I have tweaked Plasma, not to look like macOS, but to adopt the bits of its behaviour that were useful to me and that I would miss:

- A global application menu
- An icon task list at the bottom, which is _not_ a Dock

The result is a very clean environment that doesn't get in the way, that doesn't distract with too much eye candy, and for that, perfect for coding tasks and data display.

![Desktop Result](/assets/desktopresult.png){: .center-image }

I left the `neofetch` output just in case someone wants to know more about the themes used (quite unremarkable choices I think, but hey).
<!--more-->

# Global Application Menu
I wanted a Global Application Menu for two reasons: personal preference and windows efficiency.

One thing that happens to me all the time, is that I always, consistently, look to the upper left corner of my main screen when I need to do something in the current application's menu. Both Windows 10 and Plasma use by default the same user experience convention that consists in putting the application menu in the main window of an application. 

When you have multiple monitors and work with non-fullscreen windows, this will make you spend a few very annoying seconds searching for a menu: first bit of friction to remove. You could get used to it, but why? It's always better tell the software what to do to work for you rather than let the software tell you how to work.

Also, a single, global application menu, will get you an extra amount of space in every window as it disappears from it. The key point of all this is that the global application menu will take the menu away from each window and into the panel it lives in. This not only increases the usefulness of every pixel within every window, but also will make a lot easier to tile and fit multiple windows in a display, resulting in a much more efficient set up.

Plasma supports a global menu based on the D-Bus, a standard process intercommunication channel, object and data sharing bus that is part of most of the GNU/Linux set ups, and also present in many other operating systems. While available through personalisation, a global is not enabled by default, but more a user choice. 

In order to have the global menu in the same position than in my old Mac, I turned my KDE panel into a more macOS-like menubar, doing this:

1. I removed the task list widget from the main panel. When doing this, the system tray will slip all over the panel to the left.
2. I took back the system tray to the right corner, by adding a spacer in the panel and drag the widgets around it.
3. At this point, I had a panel that only had the application launcher at the left, a system tray at the right, and nothing else.
4. I dragged it to the upper screen border and give it the appropriate height. I chose 24 pixels.
5. I finally added the proper widget, the Global Menu. I placed it between the Application Launcher and the Spacer I just added in step 2.

At this point, you have a Global Menu that won't work for now, because the applications won't publish their menu to it! You need to install the following packages:

```bash
sudo apt -y install appmenu-gtk2-module appmenu-gtk3-module \     
    appmenu-qt appmenu-registrar libdbusmenu-glib4
```
These packages will allow all the applications that use the desktop environment, in any of the available UI variants (GTK2, GTK3, QT), to publish their menu to the Global Menu using the D-Bus.

After that installation, you should have a working global application menu a la macOS in your Plasma desktop. It will display the application menu of the application you are using at every moment, and no compliant applications will have an application menu in them. 

![The Application menu showing the VS Code menu. Notice that the VS Code windows doesn't have a replica of the menu](/assets/globalmenu.png){: .center-image }

Also, as the only warning, you will depend on the application developer to make the application menu to comply with this UX convention. One example of an application that doesn't publish its menu, is Firefox. I find this very annoying, as its macOS version does provide a global application menu to the Mac menubar. I guess this is an example of a restrictive, *policied* desktop environment that *requires compliance*, as macOS does, as opposed to an environment with a much more loose set of definitions and choices, such as GNU/Linux' desktop environments are.

# Recovering the Bits of the macOS Dock that I Like and Miss
I always had an almost empty Dock in my Mac. It only had the Trash and Finder icons, but because you really can't get rid of them. In reality, what I found the Dock somehow useful for, was to show the open applications list there. 

So for my Plasma setup, I created a second panel to have in the bottom of my screen, I added as the icon-only tasklist as only widget, and I configured it to grow dynamically from left to right (I really never liked the center obsession with the macOS dock). Other settings for this panel are the following:

- The icon-only tasklist widget is configured by default with two rows of icons. I don't like that, so I configured the panel with only one row.
- Its height is 48 pixels
- My current set up is "Windows can go below". As I said, I hardly ever maximise any window, so I don't really have overlapping. But if I do, I prefer that behaviour rather than the auto-hide or hiding the panel. For no strong reason, to be honest.

![Task List is Back](/assets/tasklist.png){: .center-image }

No package installation is needed for this. Of course you can install a proper Dock for these kinds of set ups, because I know: this is not a dock, it's just a panel with the task list. But, again: this is what I was using the macOS dock for, so...