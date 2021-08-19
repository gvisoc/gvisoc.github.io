---
layout: post
title:  "Manjaro Post-Install"
date:   2021-08-19 17:20:00 +1000
categories:
- personal
- linux
excerpt_separator: <!--more-->
---
Although Manjaro is one of the GNU/Linux distributions that I find to be more frictionless for me, it still need some for some actions to be done after a fresh installation.

This post covers the following:

- Install additional modules for a modern ThinkPad.
- Enable VA-API support for an Intel 8th Gen's integrated GPU
- Configure local hosts
- Set up certificates and cryptographic keys
- Set up VS Codium / Visual Studio Code - OSS
- Set up user backup and system snapshots

<!--more-->
## Install Power Management Modules (`acpi_call`)
`acpi_call` is a module that provides power management related functions like the recallibration of the battery gauge. While the module currently responsible for the battery thresholds (`natapi`) is usually present by default, `acpi_call` is not.

```
sudo pacman -Syu linux510-acpi_call
```

To check for more / other modules that may be needed, run:
```
sudo tlp-stat -b
```
And look for indications at the top of the output:
```
$ ~ sudo tlp-stat -b                                                      
Place your finger on the fingerprint reader
--- TLP 1.3.1 --------------------------------------------

+++ Battery Features: Charge Thresholds and Recalibrate
natacpi    = active (data, thresholds)
tpacpi-bat = active (recalibrate)
tp-smapi   = inactive (ThinkPad not supported)

+++ ThinkPad Battery Status: BAT0 (Main / Internal)
...
...
$ ~
```

## Install VA-API support
VA-API is the hardware video codec technology present in AMD GPUs and Intel integrated GPU for 8th Gen. and newer processors.
```
sudo pacman -Syu manjaro-vaapi
```

## Configure hosts
If you have home servers configured with static IP or static DHCP leases, this is handy:
```
echo "IP hostname\nIP2 hostname2" | sudo tee -a /etc/hosts
```

## Configure Your Cryptography Certificates and Keys
Normally, the relevant basic packages to run all the following commands are already installed.

### GPG Keys
Import your private GPG key with the following: 
```
gpg --import ~/path/to/private.pgp
gpg --edit-key <KEY_ID> # Issue 'trust' to select trust level
```

Chances are that a stock or default email client, like Geary for Pantheon or Gnome 4, does not support GPG. Install Evolution and configure `pinentry` to use your GPG key.
```
sudo pacman -Syu evolution
```
When trying to sign a message, chances are that an "`unappropriate ioctl for device`" error pops up. This is a misconfiguration of the program `pinentry` that is meant to ask for the passprase to unlock the private key.

The solution described in [this StackOverflow thread](https://stackoverflow.com/a/59170001/211280) works for a current Manjaro using Gnome 4.

Add this to `~/.gnupg/gpg.conf`:

```
use-agent 
pinentry-mode loopback
```

And this to `~/.gnupg/gpg-agent.conf`:

```
allow-loopback-pinentry
```

Then restart the agent with `echo RELOADAGENT | gpg-connect-agent`.

### SSH Keys
Generate a SSH key and import it in your home servers and GitHub to be able to operate thriough SSH without supplying your password from this machine:
```
ssh-keygen
# copy .ssh/id_rsa.pub contents into .ssh/authorised_keys at home servers
# copy .ssh/id_rsa.pub contents into GitHub Settings 
```

### Government Certificates
For public communications and govt. processes, import an official certificate into Firefox: provided that you have backed them up in a `.p12` file protected with password.


## VS Code - OSS
Visual Studio Code - OSS, a.k.a. "VS Codium", is my preferred editor for general purposes, and with the extension "Markdown All-In-One", for Markdown in particular. 

Manjaro has VS Codium in its repositories, but the extensions marketplace sometimes is not working. It's a bug in the store, not in the program. In case we need urgently an extension we can change the configuration to use Visual Studio Code Marketplace instead of the default, VSX. To do so, edit `/usr/lib/code/product.json` with elevated privileges, and change:
```
"extensionsGallery": {
    "serviceUrl": "https://open-vsx.org/vscode/gallery",
    "itemUrl": "https://open-vsx.org/vscode/item"
},
```
for: 
```
"extensionsGallery": {
    "serviceUrl": "https://marketplace.visualstudio.com/_apis/public/gallery",
    "cacheUrl": "https://vscode.blob.core.windows.net/gallery/index",
    "itemUrl": "https://marketplace.visualstudio.com/items"
},

```

## Backups
Install and configure *Backups* and *TimeShift*.

- **Backups** is the user friendly name of the tandem Duplicity & Dejà-Dup. This one is useful to backup the user home directories and its behaviour is similar to Apple's Time Machine or Microsoft's File History, with its differences and limitations in some cases. 
- **TimeShift** is useful to have system snapshots and be able to roll back a broken system to a healthy status. Similar to Mac's Carbon Copy Cloner or Super Duper but based on either `rsync` or `brtfs`; each backend with its pros and cons. Although it can snapshot the whole disk contents, the program recommends to exclude the user home directories to save space in the snapshots, which may or may not make sense depending on the user. That's why we also use the previous one.

Timeshift uses *cron* to schedule snapshots. Manjaro uses cron's backwards-compatible successor *cronie*, but apparently it is not enabled by default after the installation, so TimeShift's shnapshots won't happen automatically. 

To solve that, check that the cron entry is created (it should) and that *cronie* is running:
```
ls /etc/cron.d/timeshift-hourly
journalctl -b -u cronie
```
If the second command shows no entries or result, then enable cronie, check the enablement result, and check the journal again:
```
sudo systemctl enable --now cronie
systemctl status cronie
journalctl -b -u cronie
```

Nevertheless, as Manjaro is a rolling release distribution, it's recommended to complete the TimeShift set up by installing the package `timeshift-autosnap-manjaro`. This package will add a call to TimeShift for it to create a snapshot before installing new packages or updating the system.