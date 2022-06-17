---
layout: post
title:  "Cleaning the Terminal Screen While Preserving Last Exit Code"
date:   2022-06-17 20:20:00 +1000
categories:
- linux
excerpt_separator: <!--more-->
---
When working on a shell script it's usual to find oneself trying commands in the terminal emulator to capture their behavior. This usually garbles the screen, and sometimes we need to clear it.

There's a command to do so, `clear`, present in most shells. 

The downside of running a command to clear the screen is that its exit code will overwrite the previous command's in the environment variable `$?`. This can make our sesions on the shell a bit harder: as the `clear` exit code is `0` except in catastrophical scenarios, we lose the ability to prototype the script's control sentences on the value of `$?`.

![Cleaning the terminal](/assets/clear-terminal.gif)

The alternative consists on pressing `CTRL+L` in the Linux terminal emulator (e.g.: Gnome Terminal). By doing so, we make it to scroll down the necessary amount of lines for the screen to appear clean, no command involved. Thus, the exit code of the previous command will persist in `$?`.

On macOS' terminal the keyboard shortcut is `CMD+K`.