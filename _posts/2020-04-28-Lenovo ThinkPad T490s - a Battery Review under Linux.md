---
layout: post
title:  "Lenovo ThinkPad T490s: a Battery Review"
date:   2020-04-20 17:30:00 +1100
categories:
  - "tech"
  - "Linux"
excerpt_separator: <!--more-->
---
It's been a few time since I decided to renew my laptop and, finally, the replacement to my MacBook Pro mid-2014 came in the mail last Monday (April 21st, 2020). The selected laptop has been a Lenovo ThinkPad T490s, and I decided to use it entirely with Linux.

In this post I will link some power management tweaks and explain them, as well as some other tips and resources, and analyse how this laptop battery performs using Ubuntu 20.04.
<!--more-->
# Laptop Configuration in Brief
This laptop has the following configuration:

| Item       | Value                               |
| :--------- | :---------------------------------- |
| Processor  | Intel i7-8665U                      |
| Memory     | 32 GB at 2400 MHz, soldered         |
| Storage    | 1 TB M.2 2280 NVMe TLC OPAL         |
| Graphics   | Integrated Intel UDH Graphics 620   |
| Display    | 14", 1920 x 1080, IPS Mate, 250 Nit |
| TrackPoint | Yes :)                              |
| OS Shipped | Windows 10 Pro                      |
| OS Used    | Ubuntu 20.4 LTS (no dual boot)      |

The laptop came with Windows 10 Pro, which is an operating system that I like. The reason I switched to Linux is the nature of my work with the laptop, which is more inclined towards Unix. 

The initial configuration was a dual boot between elementary OS and Windows 10. After a week of not booting Windows more than for a few tests, and suffering some [very annoying 3 year old bugs](https://github.com/elementary/gala/issues/63) when connecting an external display to the laptop, I decided to change the layout of my disk to 100% Linux, switch to Ubuntu to avoid bugs, and no Windows.

# Energy Configuration
Linux has evolved quite nicely when it comes to manage the power consumption of a laptop. The packages TLP and `powertop` give a lot of control over the energy management of modern computers, and some nice specific extensions for the ThinkPads. 

Although out of the box those packages give a nice performance, it is recommended to tweak their configuration a little and the [ThinkPad subreddit has a very useful wiki page](https://www.reddit.com/r/thinkpad/wiki/os/linux) with instructions to follow. 

For my laptop, at the moment of writing this post, this is the scripted configuration I applied right after installing the system. It is basically and automation over the aforementioned wiki, to most of its extent, only omiting the packages and modules that don't work for this model.

```bash
#!/bin/bash

# THIS IS SPECIFIC FOR THINKPAD LAPTOPS.

if [ $EUID != 0 ]; then
    sudo "$0" "$@"
    exit $?
fi

apt update

apt -y install software-properties-common
# PPA for TLPUI
add-apt-repository ppa:linuxuprising/apps 

apt update

apt -y install tlp tlpui acpi-call-dkms tlp-rdw powertop

powertop calibrate

rm -f /etc/systemd/system/powertop.service
echo "[Unit]					" >> /etc/systemd/system/powertop.service
echo "Description=Powertop tunings		" >> /etc/systemd/system/powertop.service
echo "						" >> /etc/systemd/system/powertop.service 
echo "[Service]					" >> /etc/systemd/system/powertop.service
echo "Type=idle					" >> /etc/systemd/system/powertop.service
echo "ExecStart=/usr/sbin/powertop --auto-tune	" >> /etc/systemd/system/powertop.service
echo "						" >> /etc/systemd/system/powertop.service
echo "[Install]					" >> /etc/systemd/system/powertop.service
echo "WantedBy=multi-user.target		" >> /etc/systemd/system/powertop.service

systemctl enable --now powertop
systemctl enable --now tlp.service
```
You can find a bunch of scripts [in one of my GitHub repos](https://github.com/gvisoc/linux-setup). They are not a software masterpiece, just automation to ease the set up of a new Linux installation.

After doing this, I modified the TLP configuration to apply the following values:

- Set up `MAX_LOST_WORK_SECS_ON_BAT` to 15, with either TLPUI, or with a configuration file that will depend on the Linux distribution used. In case of Ubuntu, these files are:
  - `/etc/default/tlp` for Ubuntu 18.04 LTS
  - `/etc/tlp.conf` for Ubuntu 20.04 LTS
- Set up some battery thresholds for my target usage. More on this later; it won't affect the figures.

That is, fundamentally, the energy configuration of my laptop.

# Final Preparation: a Few Days of Battery Warm-Up
I used the laptop for a total of 4 cycles, that is, an equivalent of 4 full discharges and charges back to 100%. One of them was a recalibration of the battery gauge, something that is supported by the hardware of the ThinkPads and that works at follows:

- **Plug the ThinkPad** with the AC Adapter to a power outlet.
- Open a terminal and run `sudo tlp recalibrate BAT0`
  - The terminal will clear and some statistics will show during the process.
  - Do not close the terminal
  - **Do not unplug the laptop**
- The laptop, while plugged, will make use of the battery to force a full discharge. That's very nice of the ThinkPads.
- Use the laptop normally.
- When discharged completely: 
  - The laptop won't power off
  - It will automatically begin to charge the battery, as it is plugged in.
  - The terminal will notice it, and it will ask to not unplug the laptop until it reaches 100% charge.

At that moment I left it alone, powered on and with the lid closed, and I just did other things. I suppose that you can use the laptop normally, though.

# The Numbers
WIP

# Network Factors that will Influence the Battery Life
The network traffic is very energy-hungry. Nowadays, the network cards manage the power very efficiently to reduce their standby power usage, but when they have to intensely download information, they use their fair amount of energy. 

Try to reduce your network-intensive activity while on a battery. Examples, more or less obvious, are:

- Do not update the system on a battery (clearly!)
- Do not update or refresh the dependencies of your software proyects while on the go
- Do not clear caches. Also, incognito windows are more energy-expensive.
- Be very careful with scripted software repositories that, if need something from the internet, will download the Solar System and build and compile the World, the Moon and Mars. `rvm` is one of them, I use it to blog in this site (runs on Jekyll), and when it does that, the laptop fans literally go bananas.


I very aggressively block all the advertisement at my home network with a [Pi-hole](https://pi-hole.net/). Also, I use Firefox with the shields up for any website that works well with all the blockers that the browser ships out of the box.

These last two factors will have a dramatic impact on the energy that each of the browser tabs will drain from the battery, and it's something I recommend: block the advertisement, save battery and reduce your emissions.

# Extending the Life of the Battery with the Charge Thresholds
Most consumer laptops will stop charging the battery at the 100%, and won't charge it back unless the battery charge level drops below a slightly smaller percentage, often 96%. 

While those thresholds are also the factory configurations of the ThinkPads, these allow the user to establish a different charge thresholds. This is useful for extending the life of the battery as a component, as it is known that for the Li-Ion batteries, if you are not going to use them for a long period, they are best preserved with a charge level between 60% and 80%.

When you set a different set of thresholds for a ThinkPad, like 60% and 80%, the laptop won't charge unless the battery charge level falls below 60%, and it will stop charging when it is back at 80%. All the rest of the time on AC, the machine will use the adapter power, and the battery will be... just disconnected. This is very useful and very kind with the battery if you don't use the battery too often.

This is definitely another very nice, charming perk of these laptops.

For Linux, you handle these values with either TLPUI, or with a configuration file. These files are:

- `/etc/default/tlp` for Ubuntu 18.04 LTS
- `/etc/tlp.conf` for Ubuntu 20.04 LTS

Check your distribution documentation in any other case.

The values you want to uncomment and tweak are the following for the internal battery, `BAT0` (`BAT1` is a specific external one):

- `START_CHARGE_THRESH_BAT0="60"`
- `STOP_CHARGE_THRESH_BAT0="80"`

These values can be temporally modified by commands like `sudo tlp fullcharge BAT0` or `sudo tlp START_CHARGE BAT0 75`. If you want the defaults to be restored every time you go on a battery ride, keep going and uncomment the following and set it to 1:

- `RESTORE_THRESHOLDS_ON_BAT="1"`

Once you do this, these values will be configured system-wide, at some place external to the OS; I'm not sure if it is the UEFI, the system's NVRAM or a NVRAM in the battery or in the power subsystem, but they will affect other Operating Systems in the machine.

For Windows 10, all these are done with [Lenovo Vantage](https://www.lenovo.com/us/en/software/vantage).