---
layout: post
title:  "Fixing a Common User Home Service Error in Synology NASes"
date:   2021-02-06 16:32:00 +1100
categories:
  - tech
  - linux
excerpt_separator: <!--more-->
---
If you have a Synology NAS and you are a technology enthusiast, or have some interest in using such machines as home servers, you may run into a problem that is quite common. Judging from the amount of Google results, the User Home service gets somehow broken quite often the first time it is activated, and neither is detected by some other services that use it as a dependency, nor can it be deactivated.

I came across the cause of the problem and its solution; keep reading to know more.
<!--more-->
# The Symptoms
At some point you need to activate User Home service as per the requirements of some other package (for example, Synology Mail), or just because you want to work through SSH, or any other valid reason. Although you activate it, the dependent package keeps asking you to enable it. Or, if you just enabled it to use home folders via SSH, although you land on a home directory when you log in through SSH, you can't see it through DS File or File Station:

![File Station Not Showing Homes](/assets/FileStation.png)

In the above picture you should see a folder for each user under `homes`, plus a `home` directory that would host your files (it's like a symbolic link dynamically created when you log in to the NAS through DSM or DS File).

If you try to disable and re-enable User Homes Service, DSM shows a sad and brief "Operation Failed" error pop up, and in Log Center you just see a useless message "User home service disable failed" like the one below:

![Log Station Message](/assets/LogCenter.png)

# The Problem
The problem is that the user homes' hosting folder, named `homes`, should be created in one of the main volumes inside the disk array, for instance, `/volume1/homes`, and a symbolic link back should be created at `/var/services/homes`. Instead, in this case the `homes` folder was created in that location directly, as a regular folder (`/var/services/homes`). We can check it by doing SSH to the NAS:

```bash
user@hostname:~$ pwd
/var/services/homes/user
user@hostname:~$ cd ../..
user@hostname:/var/services$ ls -lda homes
drwxr-xr-x 6 root root 4096 Feb 5 08:00 homes 
```

The expected result of a successful User Home service activation is this one instead:

```bash
user@hostname:/var/services$ ls -lda homes
lrwxr-xr-x 6 root root 4096 Feb 5 08:10 homes -> /volume1/homes 
```

Not only you can see where it points, but also the `l` flag on the folder permissions, meaning *link*, instead of a `d` from *directory*.

It is important to note that, although the user still has its home folder that works, **the user homes created this way are outside the disk array and in the operating system's filesystem**. This fact have several consequences, all of them dangerous for our files, for DSM's stability, and therefore for our NAS reliability. 

In the first place, it causes almost every Synology package that depends on the User Home service to fail. Synology packages usually operate with directories under the volumes created inside the disk array, not under `/var/services/`, and that's why Synology Mail didn't detect the user homes directories and kept asking for User Home service to be enabled. As with Synology Mail, if the NAS is kept in this situation, the files in those directories won't be accessible by any backup or synchronisation service, and something as simple as updating DSM can have all the files under `/var/services/homes/` wiped, and lost forever. 

Another consequence is that all user home directories will be lost in the event of machine upgrades or replacement, meaning that in the event of moving the disks to a new NAS, those user home directories won't be carried over.

A much worse scenario would be the one where we run out of space in the system's partitions because we put too many files under our home directories, phisically located under `/var`, causing DSM to crash and, probably, to have problems to boot.

# The Solution 
Solving the problem is as easy as login in through SSH to back up our files and delete the `/var/services/homes` folder:

```bash
user@hostname:~$ cd /var/services
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

No services should fail on activation, and the DSM File Station should show all users' home directories; time to restore the previous users' home folders contents if needed.
