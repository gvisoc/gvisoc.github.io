---
layout: post
title:  "Why do You Want to Install Your Personal Mastodon Instance at Home"
date:   2023-01-10 15:00:00 +1000
categories:
- linux
excerpt_separator: <!--more-->
---
**TL;DR**: 
> This post is about the rationale of installing my own personal Mastodon instance at home, not in a VPS or in a cloud provider.
>
> In it, you will find hints on how to do it, but not a detailed recipe to overcome all the tasks, as such is not the goal of the article.

I'm not going to relate all my trajectory on Mastodon because I want to make things short. 

The idea is that Mastodon is an application with non-trivial machine requirements, and **the Cloud is Expensive**.
<!--more-->
## Mastodon is Heavy
Let's see where Mastodon's footprint comes from, from my very limited understanding of things and my limited experience:

 - Mastodon is a Ruby on Rails application that has *at least* three independent processes, or services, as shown in `systemd`: 
   - The web application, that offers a user-facing applicaton. In it, we *need to do things* in order for the application to fetch information. It's very passive.
   - The streaming API, which does things in the background and streams things to the clients, both web and mobile. An example of this is sending the notifications to the web application, as they happen.
   - The Sidekiq worker, which runs in the background and implements the federation, or so I believe, in the sense of replicating data in and out of our instance to the other known instances.[^1]
 - It depends also on a Postgresql database.
 - It uses Redis, an in-memory cache backed into the disk, to support Sidekiq processes.
 - It uses ***A Lot*** of disk space, as a result of two things: 
   - The activity and uploaded media of the instance's users.
   - The federated content coming from other instances.[^2]
 - You will need several other smaller services hanging around, like an NGINX to act as a web server and gateway.

In my experience, if we use a Digital Ocean Droplet of 2GB RAM and 2 CPU, the  Mastodon services will have to be stopped for us to be able to run maintenance tasks without Digital Ocean killing them. 4 GB and 2 CPU will be the minimum suitable droplet, and a disk space of less than 60 GB will be tight.

And that's just a minimal installation. Add HA (High Availablity) on those components, and you'll have to put much more hardware and extra networking services. 

That is to say, the beast is heavy.

## The Cloud is Expensive
One of the aforementioned droplets would cost you US$24/month, which in Australian dollars is about A$40/month. 

That's a lot of money: A$480 a year. And the droplet is a dumb and isolated virtual machine that doesn't do *anything* for you with regards to maintenance and administration.

- Do you want application backups? They are charged separately.
- Do you want machine snapshots? They are charged separately.
- Do you want alerting when the droplet is down or slow? Charged separately.
- Are you running things in HA? Do you need extra network things on top of the droplets you're adding? Charged separately! 

And, of course, consider your labour. You must keep not only Mastodon, but also the droplet's operating system up to date and finely tuned. That's a lot of work, on top of being a lot of money.

## What it Takes
Here's the thing:
- In little over 2 months, you would have spent more money than the cost of an ARM single board computer, that exceeds the above requirements (like the Raspberry Pi[^3] 4B, 4GB) *and the electricity cost of running it for that time*. 
- In another 2-3 months, you can add to the pack an SSD drive of hundreds of GB, and externalise all the disk usage to it.
- From there, you are effectively saving a lot of money that you could use to support the projects around the network, or the instances you sympathise with. Or ☕.

The only things that you need are: 
- A small computer with enough resources. 
  - 4 GB of RAM, 60 GB or more of storage, 4 core is enough to get you up and running.
  - In the medium term, if you're using an SD card for the system, attach some proper storage for high I/O load for performance and reliability. Small SSD and USB 3.0 cases for them are very cheap.
- A domain managed by a domain provider that supports DDNS[^4], 
- Install `ddclient` (from [its GitHub repository](https://github.com/ddclient/ddclient)) on your machine [^5], 
- A router that supports NAT and that allow you to expose services at ports `80` and `443`.[^6]
- A decent internet connection.[^7] 

> 🌱 Preferably, choose a low power device. Don't run Mastodon 24/7 on your forest-burning gaming PC 🌷

It won't support more than a few friends and family members. But if your instance is personal, or if your crew is limited, you'll be fine. The amount of extra work that you need to do by having the instance at home, compared to having it in a provider like Digital Ocean, is negligible. 

And it's **a lot cheaper**.

---
[^1]: A federated application is not a distributed application. While in a distributed application all instances try to discover all other instances and be able to communicate with each other, a federated application is passive. The instances will communicate only if they know each other. How an instance can know other instances? In Activity Pub, only through the users' actions. If I decide to follow you at [https://mastodon.social](https://mastodon.social) from my [https://fedi.gvisoc.com](https://fedi.gvisoc.com), then those two instances will begin talking to each other. From that moment, they will talk **a lot**, but the federation details are out of the scope of this article. Just let's assume that there is a lot of media files and data going back and forth.
[^2]: Mastodon caches a lot of information, including, and especially, pictures and videos from other instances. It will cache media from all the accounts you follow across any instance, and from all the known, but not followed accounts, such as those whose posts appear in your timeline as someone you actually follow boosts them. From all those, it will cache avatars, headers, emojos, and all the attachments of the posts that are *visible* in your timelines. Visible doesn't mean viewed: those resources will be cached in your instance's disk even if you never see them. And I'm sure this doesn't end there, but I am not 100% sure of all the implications of the federation and ActivityPub.
[^3]: Although [the people around the Raspberry Pi is not very popular at the moment](https://www.buzzfeednews.com/article/chrisstokelwalker/raspberry-pi-hired-ex-cop-mastodon-controversy), the community around these machines is huge and I'm sure many of us will find some at the bottom of a drawer.
[^4]: I have all my domains at Cloudflare, who are ethically very slow and tone deaf as well (please suit yourself with a web search engine for this one claim), but very cheap. And in the free tier of Cloudflare, you can update the DNS record for your machine through their API, which is spoken by `ddclient`.
[^5]: Check my configuration (last reviewed at the time of this writing) in this [public Gist](https://gist.github.com/gvisoc/9bc137a7c01a149f5d5e146f1827f211) at my GitHub account.
[^6]: I haven't found any router that wouldn't allow you to do so. Even the provided by the ISPs can be configured in such way (I am using one of those right now).
[^7]: I am getting very good results with an internet connection that offers 20 Mbps upload speed. This includes not only me accessing information on the go, but also the other instances to find my information quickly: there are posts that get reactions almost immediately after they are published... in those rare cases I publish anything interesting.