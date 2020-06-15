---
layout: post
title:  "Installing Linux with Secure Boot and Friends"
date:   2020-06-15 10:55:00 +1000
categories:
  - "tech"
  - "linux"
excerpt_separator: <!--more-->
---

This post is motivated by the confusion and the still prevalent recomendation of disabling Secure Boot when installing GNU/Linux, no matter what distribution we are talking about.

Contrary to the mainstream trend of such a disablement and proceeding with an oudated bootstrap, security-wise, many distributions do support Secure Boot since a while ago.

In this post I will provide some links, references, and "glue text to make sense" in order to provide clarity about Secure Boot: what it is, what it provides, and what it takes from us --mostly in terms of user experience, or friction.
<!--more-->

I do not want to reinvent the wheel or take credit for the existing state of the art, but to provide some insight that I hope it will be easy to understand to the Linux novel users. Or at least, to provide a lead that can be followed to read more and understand what's happening.

## Structure
You will find:
1. What I assume you know, or a list of things for you to search about.
2. A process to install a Linux system with Secure Boot enabled.
3. Post-Install situations affected by Secure Boot
4. What is Secure Boot, and what's it based on. 

## What I assume you already know (a.k.a., what you're supposed to search in the interwebs if not!)
I assume that you are familiar with the following concepts, which I try to summarise. But, please: use your search engine.
- Kernel: it's the hert of the operating system. It provides the applications with access to the hardware.
- Modules: specialised code that the Kernel will load to tailor its capabilities to very specific hardware or configurations. Many times, when it allows the Kernel to understand a piece of hardware, it can be called "a driver" like in Windows lingo, but there are modules for many other things.
- Root password: it's the password of the root user, which is the most powerful authority in a Unix or Unix-like system, as GNU/Linux for instance. Absolute privileges.
- BIOS: stands for Basic Input and Ouput System, and it was the way that older (early 2000's and before) computers had to connect the Operating Systems to the hardware. This included a boot menu and a very few more options, and many specialised configurations for some hardware devices had to be done by plugging, unplugging or changing physical jumpers in the motherboard.
- (U)EFI: stands for (Unified) Extensible Firmware Interface. It's the evolution of BIOS, looks after the same hardware to operating system bridge (which is where firmware, another fancy word, sits in!). It is much more advanced and comes with many more options, many of them enabled for modern system by default, like the GUID Partition Table for the Disks, and the Secure Boot.
- GUID Partition Table. A modern partition schema that identifies partitions on the disk by giving them Globally Unique IDentifiers, rather than names. Amongst other things, supports much larger disk sizes.
- Secure Boot deserves a whole section on its own. Go and check "What is Secure Boot" at the end of this post.

## Installation Process
### Check whether you actually have UEFI
First of all, have a look at what you have in your computer: whether BIOS or EFI. If you have BIOS you can skip the whole article, because Secure Boot was introduced in the UEFI.

If you're not sure which system your motherboard uses, and if you still have Windows there, check the following:
- Look for an EFI folder within `c:\windows\boot`
- Open the Disk Management Utility and check if you have a partition labeled EFI in your system disk, like this one:

![EFI Partition](/assets/DiskManagerEFI.png){: .center-image }

If you don't have Windows or you are unsure, 
- Reboot the computer and look for a message telling you to press a key to enter setup. 
- Press that key, quick! 
- No worries, it happens to everybody. Reboot and try again.
- Once inside, and without changing anything, look for indications of an EFI system: search for options like "Secure Boot", "Legacy/UEFI" boot mode.

If your system shows any of the above symptoms, you have an UEFI.

### Choosing a distribution that supports Secure Boot
There is a way to query distrowatch to get a list of the distributions that may support Secure Boot by searching included packages like [`shim`](https://distrowatch.com/search.php?pkg=shim&relation=lessequal&pkgver=1&distrorange=InAny) or [`efibootmgr`](https://distrowatch.com/search.php?pkg=efibootmgr&pkgver=0.&distrorange=InLatest#pkgsearch), but the best way is to check your favourite distribution's documentation (kudos to [firmwaresecurity.com for the queries hint](https://firmwaresecurity.com/2019/04/29/list-of-linux-distros-support-of-trustedsecuremeasuredboot-and-fwupd-needed/)). 

The good news is that the support of secure boot is already in the Kernel and the base system for all of them, so it would be a matter of time and ideology for it to arrive to your computer. If you want to install a different distribution than those, you'll have to disable Secure Boot in your EFI.

### Download and verify an ISO, preferably a Live one with non-free firmware and modules included
It is advisable, always, to download an ISO installation image that offers you the possibility of trying Linux in your system without installing it first. This will give you an idea of whether or not your installation media will have the required modules for your hardware, most precisely (and critically neccessary) one module for the graphics adapter and another one for the network. Also, depending on your hardware and distribution, you may have to download specifically an ISO with *non-free* software included for it to work! This is almost always necessary (99%+ cases) in the case of Debian and most  laptops. 

Download your distribution in ISO format, as usual, and look for a way to verify the files you download. Most every website that offers free or open source software these days will have a side or footnote telling offering you "the hashes" or the "sums": MD5 or SHA checksum strings. 

Checksums are strings that represent the contents of a file, much smaller than the files they represent. They are generated by a mathematical algorithm that ensures that the probability of getting the same result with different files is ridiculously small. So ridiculous that it's difficult to explain, but the chances are cosmically low. 

These are techniques that allow you to check that the ISO you downloaded has not been corrupted by the download process, and more importantly, that they are authentic. It helps you know that the mirror or the server where the actual file is hosted, usually different than the server that publishes the checksums, has not been compromised: you're downloading an authentic image, without any malware with it. What they do is run the same procedures that you're about to run, in their side, and tell you to check that you get the same results.

If your distribution publisher offers you SHA verification sums, open Windows PowerShell and run `Get-FileHash` over the ISO you just downloaded:

![Checking the sum!](/assets/GetFilehash.png){: .center-image }

If your distribution publisher if offering MD5 sums, then open the regular command prompt and use `certutil -hashfile` over the file.

Whichever method you use, your local result should match with the check sums provided by the software publisher (leaving uppercase or lowercase aside).

If they provided some other methods, check their documentation.

### Creating a USB that boots under UEFI conditions
In order to create a bootable USB drive under UEFI's newer boot schema, that USB has to be formatted using GPT (Guid Partition Table), as the MBR (Master Boot Record) drives are considered legacy!

To do that, you can use Rufus to create the USB. Rufus is an open source program that you can download from its official site [https://rufus.ie/](https://rufus.ie/), and will offer you a simple, but powerful interface to create the bootable thumbdrive. Just make sure that you select GPT as Partition Scheme, and UEFI as Target System, leaving all other options by default (unless you wanted a persistent partition to use in a Live System):

![Rufus](/assets/Rufus.png){: .center-image }

### Reboot in Linux
- Reboot the computer and look for a message telling you to press a key to show the boot menu. 
- Press that key, quick! 
- No worries, it happens to everybody. Reboot and try again.
- Search for your USB drive. 
    - They are usually by the EFI the word "USB" and the brand of the device, plus some other model numbers and maybe the capacity.
    - Select it and press the Enter key.

Select the option to *run* Linux, or to *try Linux* without installing.

If everything works, specially the graphics and the network, then you're good to go and install it.

### Installing with Secure Boot: things that can happen.
In most of the cases, the installation process will be smooth and without any surprise. But, in some cases, either:

- The installation would remain stopped in a package for too long. If you expand the details of the installation process, you may see something like the below image.
- You will prompted by the system that some third party software needs to be installed and you will to do some funky stuff for it to work, something similar to this:

![Ubuntu Prompt](/assets/UbuntuPrompt.png){: .center-image }

This is caused by Secure Boot, and nothing is wrong. What is happening is the following: the system found that it needs to install a package that will be loaded at boot time, but it is not signed. Secure Boot requires the software that runs at boot to be signed by a trusted key. The installation process can create a key for you, called "Machine Owner Key", and sign the software with it, but in order to make the EFI trust the signature, it needs you to collaborate. 

- Press the Enter key, or the tab key to focus OK and then Enter, or click OK if the prompt is run in a graphical environment. 
- In the next screen, you will be prompted to set a password: do it and jot it down in a paper, as you will prompted to write it in the first reboot. 

> **Here be dragons**: if you have a non-english keyboard, *refrain yourself from using symbols that may be in a different key than the one in a US layout keyboard*, as they can cause problems when typing the password later.

Once done, the installation should continue.

When you finish installing the system, if you were prompted with such an ugly message, you will see a succession of screens like the following (taken from [this Gist](https://gist.github.com/reillysiemens/ac6bea1e6c7684d62f544bd79b2182a4), as they are the same in any case):

![1st screen](/assets/MOKEnroll1.png){: .center-image }

Select **Enroll** and press Enter.

![2nd screen](/assets/MOKEnroll2.png){: .center-image }

Press **Continue**. Yes, it is a weird screen. If you press `View key 0` you'll see some information about the key you are enrolling, but it's quite technical and can be nonsense, specially if it was the installation program who created the MOK key.

![3rd screen](/assets/MOKEnroll3.png){: .center-image }

Select **Yes**.

![4th screen](/assets/MOKEnroll4.png){: .center-image }

Type the password you've in your piece of paper and press Enter.

![5th screen](/assets/MOKEnroll5.png){: .center-image }

Press Enter

When the system reboot, and provided that the installation program meets its quality goals, things should work.

## Post-Install Miscellanea

| Case | Solution | 
|:----|:---|
I am installing a binary package and I'm prompted with the nefarious screen of the MOK!|Repeat the process as told in the previous section. This is what will happen in many systems when you try to install third party binary packages. There are other systems, like Debian, that while supporting Secure Boot, they don't have an interactive process like that.|
| I'm trying to install a module that I know I need and it should work, but it won't load | If you are getting a laconic `Permission Denied`, or `Opperation not permited` after trying to install a module in the Kernel, check `dmesg` after `modprobe module`, then look for something like `Lockdown: modprobe: Loading of unsigned module is restricted; see man kernel_lockdown.7`. If this is your case, you will have to create a MOK key, sign a module with it, and enroll the MOK key, all manually! Don't worry and go to the next section.|
| I want to compile and install a module | If this is your case, you will have to create a MOK key, sign a module with it, and enroll the MOK key, all manually! Don't worry and go to the next section.|

## Manually signing binaries (e.g.: modules)
This is the case of a module compiled by hand, or when you install unsigned modules with Secure Boot installed, in more crude distributions like Debian.

The steps for Debian (credit to its official documentation and to my experience) are:

1. Compile your software.
2. `modprobe` will fail. Check that it is due to a Kernel Lockdown state imposed by the Secure Boot, to discard other errors. In `dmesg`, search for something with similar semantics to this:

```Lockdown: modprobe: Loading of unsigned module is restricted; see man kernel_lockdown.7```

3. If you have already enrolled a Machine Owner Key (MOK) yourself, and it is available for you, put it in your home directory and jump to step 8. Otherwise, continue here.
4. Install the packages `mokutil` and `openssl`.
5. Open a terminal and create a MOK (Machine Owner Key) with the following process (`# commented`), documented [here](https://wiki.debian.org/SecureBoot#Enroling_a_new_key):

```bash
# Go to your home folder
cd ~
# Create a Machine Owner Key pair valid for 100 years. 
# In the following, replace "gabriel" for your user name
openssl req -new -x509 -newkey rsa:2048 -keyout MOK.priv -outform DER -out MOK.der -days 36500 -subj "/CN=gabriel/" -nodes
``` 

6. You will obtain two files, `MOK.der` and `MOK.priv` in your home folder. Enroll the keys in your EFI

```bash
# If you don't have sudo installed, run `su` and type the root password
# before the following commands without sudo
sudo mokutil --import MOK.der
# This will prompt for a one time password to set up.
```

7. Reboot the computer. You will be promted with the blue screens of section "Installing with Secure Boot: things that can happen". In this case, if you select the options to `View Key 0`, you will be able to see your name and the name of your computer. In my example, I saw "gabriel@rodas", and rodas is my computer named after [this beach](https://en.wikipedia.org/wiki/Playa_de_Rodas) very close to my hometown.
8. Open a terminal to sign the binary. Follow the process changing the `/path/to/file.ko` with the path to the file you need to sign.

```bash
# Assuming that you have kernel version X.Y.Z
# Replace "gabriel" by your username.
cd /lib/linux-kbuild-X-Y/
sudo scripts/sign-file sha256 /home/gabriel/MOK.priv /home/gabriel/MOK.der /path/to/your/file.ko
```

9. Try to load again the module

```
sudo modprobe /path/to/your/file.ko
```

It should work now. This is the most common use case for this. 

> **Here be dragons**: hold your horses, one last thing before you go. Take these two files, `MOK.der` and `MOK.priv`, away from your user directory, hide them in a safe place. Personally, I keep them attached to an entry in my password manager, well encrypted. **They are the door for someone to sign any malware so it can run at your computer's boot**!

## What about signing a custom Kernel?
If you need to sign a Kernel, the `/path/to/your/file.ko` looks like `/boot/vmlinuz-[KERNEL-VERSION]`, and probably you will have to follow more steps. Although I haven't have the need for it, I found several forum and Stack Overflow questions that pointed to [this document](https://github.com/jakeday/linux-surface/blob/master/SIGNING.md) as a valid process to do it.

## What is Secure Boot
Secure Boot is a mechanism designed to protect the process of booting your computer, that is to say, making your boot secure. It is not a very creative name.

It was introduced at some point in the UEFI specifications, and it made a weird entrance in the scene because some poor communications skills and shady decisions from Microsoft. They told at some point that, for any PC machine to be Windows-Certified, they should pre-ship a cryptographic key (similar to the Machine Owner Key we've used before in this post, but Microsoft's) to digitally verify the Windows bootable parts and the Kernel. They didn't specify at the point that they didn't care whether the Secure Boot could be disabled, or that many other keys should be able to be shipped or installed alonside Microsoft's! Thanks to that narrow statement, the community went crazy and, although they fixed the statement pretty much immediately, for many time Secure Boot was seen as a Microsoft attempt to close PC hardware to them. You can check this story [here](https://en.wikipedia.org/wiki/Unified_Extensible_Firmware_Interface#Secure_boot_criticism). It is true that the controversy continued for a while, specially related to ARM devices, but as always: technology isn't bad by itself, it is us who make good or bad use of it.

Indeed, Secure Boot is (to me) pretty awesome as it is a simple but powerful way to protect the boot sequence of your system against malware.

The "Secure" word of Secure Boot comes from Information Security, as it is based on asymmetric criptography. It works in such a way that, for the EFI to load any code at boot time, being it a Kernel, modules, or the GRUB itself, such code has to be cryptoraphically signed by a trusted key. Let's decypher such two sentences.

- **Asymmetric cryptography** refers to a compendium of techniques that are based in a pair of keys, public key and secret key. They public key is meant to be available to everybody, while the secret key has to be kept secret to its owner. 
    - That par of keys are often packed in a single software artefact called "certificate", in such a way that the private part is protected by a passphrase. It can also be splitted in such a way that you can produce a certificate that will only contain the public key, to be able to share it. It provides two operations: encryption and signature.
    - Asymmetric Encryption works the following way: if I want to **encrypt a message for you**, so that only you are able to understand it, I will **use your public key** and a lot of standard maths. Once done, only your private key, plus a lot of standard maths, can decrypt it!
    - Asymmetric Signature works the opposite: if I want to prove you that **it was me who wrote** a message, I will **sign it using my private key** and a lot of standard maths. You will be able to check the signature using my public key and a lot of standard maths.
        - If someone changes a single character of that message, the signature won't be valid anymore.
        - The last point is great: a signature identifies the author of a message, and protects the communication against tampering.
- We can cryptographically sign a message, or a binary file like a Kernel, or a module. 
- According to the above, when we sign some code and install it to be loaded at boot time, like a Kernel or a module, the EFI need to have available the public key to check the signatures. This defines the need of *enrolling public keys in the EFI*.
- You can create a pair of keys, enroll the public key, and use them to sign software you create or compile, if you are the root user of a GNU/Linux system. When you create your own pair of keys, they are together called a **Machine Owner Key**, or MOK.
- Enrolling a public key in the EFI is a process not only means to make available the public key to the EFI: it also makes the EFI trust that signature. Practically speaking, it will ultimately trust the software that is signed with a trusted key, and load and execute it with all the consequences.
    - Only a trusted software manudacturer, or an administrator of an operating system (root user in Linux) can enroll a key in the EFI and manually sign software.
    - Enrolling a key in the EFI is a process that requires such an administrator to collaborate and actively accept and prove his or her identity after a reboot. It cannot be automated by a malware process.

A bit of recap. If you have the system bootstrap process checking signatures of the binary code that it loads, this means a lot of security. 

Let's understand the two main *vectors of attack* it mitigates.

1. **Only trusted signers** (meaning software manufaturers, or you) **can install code that runs automatically in the bootstrap of the system.** That means, that if some random guy comes to your desk with a USB while you're away, he won't be able to infect your computer's bootstrap by putting some modules or writing code in the bootable partitions. He or she would have to enroll a key, too, and that requires (in Linux) to know the root password, which is meant to be secret and complex. They will have to be able not only to write code to the EFI, which may or may not be a challenge, but to hack the operating system they want to compromise.
2. If you accidentally allow a malware to run in your computer, that malware cannot infect the bootstrap chain even if it has the maximum privileges, as it will require active cooperation by an admin user to create and enroll its own MOK: check back the rest of the article at the point we set up a password, reboot, and type the password when enrolling the key. The only thing you have to do is to never leave your MOK private key in the computer to prevent the malware to sign itself with your own keys!

I hope you didn't have to search many terms, and that this article was, at least, easy to follow. Feel free to send me some comments to comments (at) gvisoc (dot) com!