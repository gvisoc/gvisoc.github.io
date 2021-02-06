---
layout: post
title:  "Fixing a Common User Home Service Error in Synology NASes"
date:   2021-02-06 16:32:00 +1100
categories:
  - "tech"
  - "linux"
excerpt_separator: <!--more-->
---
If you have a Synology NAS and you are a technology enthusiast, or have some interest in using such machines as home servers, you may run into a problem that is quite common. Judging from the amount of Google results, the User Home service gets somehow broken quite often the first time it is activated, and neither is detected by some other services that use it as a dependency, nor can it be deactivated.

I came across the cause of the problem and its solution, keep reading to know more.
<!--more-->
# The Symptoms
The symptoms are as follows:

At some point you need to activate User Home service as per the requirements of some other package (for example, Synology Mail), or just because you want to work through SSH, or any other valid reason.

Although you activate it, the dependent package keeps asking you to enable it. Or, if you just enabled it to use home folders via SSH, although you land on a home directory when you log in through SSH, you can't see it through DS File or FileStation:

![File Station Not Showing Homes](/assets/FileStation.png)

In the above picture you should see a folder for each user under `homes`, plus a `home` directory that would host your files (it's like a symbolic link dynamically created when you log in to the NAS through DSM or DS File).

If you try to disable and re-enable User Homes Service, it shows a sad and brief "Operation Failed" error pop up, and in Log Center you just see a useless message "User home service disable failed" like the one below:

![Log Station Message](/assets/LogCenter.png)

# The Problem
The problem is that the user homes' hosting folder, named `homes`, should be created in one of the main volumes inside the disk array, for instance, `/volume1/`, and a symbolic link back should be created at `/var/services/homes`. Instead, in this case the `homes` folder was created in that location directly; we can check it by doing SSH to the NAS:

```bash
user@hostname:~$ pwd
/var/services/homes/user
user@hostname:~$ cd ../..
user@hostname:/var/services$ ls -lda homes
drwxr-xr-x 6 root root 4096 Feb 5 08:00 homes 
```

That causes everything to fail. The expected result of a successful User Home service activation is this one instead:

```bash
user@hostname:/var/services$ ls -lda homes
lrwxr-xr-x 6 root root 4096 Feb 5 08:10 homes -> /volume1/homes 
```

Although the user still has its home folder that works, it is outside the disk array and in the operating system's partition. The consequences of this problem are various. 

In the first place, all Synology packages operate with directories under the volumes created inside the disk array, and that's why Synology Mail won't detect the user homes directories and keeps asking you to enable User Home Service. As with Synology Mail, if the NAS is kept in this situation, files in such directories won't be accessible by any backup or synchronisation service, as well, and something as simple as updating DSM can have the files wiped. 

Another consequence is that `user` home directory will be lost in the event of machine upgrades or replacements, as it is in the operating system's filesystem itself instead of in the disk array, meaning that if you move your disks to a new machine, it won't be moved.

A much worse scenario would be the one where we run out of space in the system's partitions because we put too many files under our home directory, causing DSM to crash and even to have problems to boot.

# Solving the Problem
Solving the problem is as easy as login in through SSH to back up our files and delete the `/var/services/homes` folder:

```bash
user@hostname:~$ pwd
user@hostname:/var/services$ ls -lda homes
drwxr-xr-x 6 root root 4096 Feb 5 08:00 homes
user@hostname:/var/services$ tar cfz /volume1/[dest. folder]/homes-backup.tgz homes
user@hostname:/var/services$ sudo rm -rf homes
```

After that, we create a symbolic link to `/volume1/homes`:

 ```bash
user@hostname:/var/services$ sudo ln -s /volume1/homes homes
user@hostname:/var/services$ ls -lda homes
lrwxr-xr-x 6 root root 4096 Feb 5 08:10 homes -> /volume1/homes
```

Finally, we log into the DSM web interface, to disable without error and re-enable the User Home service. 

No services will fail on activation, and the DSM File Station should show all users' home directories; time to restore the previous users' home folders contents if needed.
