---
title: "Mental Reset"
date: "2022-09-12T08:48:00"
description: "things i did, things im bout to do."
tags: ["tech", "pheelosophy", ]
draft: "false"
---

In the time period between my last post and this, I did a bunch of things. Some of them dumb, some of them cool and most of them unnecessary. I’m proud of all of them nonetheless (but ofc I won’t talk about the dumb ones)



I learned go. It is a pleasant language. Not amazing, not bad, good. Using go, I then proceeded to redo the backend of this blog. 

# 1 thing I love 2 do is overdo

I wanted to find a way to write, without getting tied down to using markdown and my laptop.

Basically, a system where I can comfortably write on my phone and not markdown. So I started researching different CMSes and one of them which piqued my interest was [Wagtail ](https://wagtail.org/)and its streamfield. But that would mean I would have to rewrite everything in Django and deploy the site on my own server, which opened its own bag of worms (CI/CD, CDN, etc). 



Then, I came across Notion. I had used it in the past, but have never thought of it as a CMS. But it made perfect sense to use it to write blog posts. The formatting choices it provides are excellent, they cover almost everything from markdown and some more. The writing experience is really good, they provide a mobile app. It seems like the perfect choice!



So I used the [notionapi](https://github.com/jomei/notionapi), whose lack of documentation I actually appreciated. It got me comfortable reading go types and go source code thanks to that. So, I hacked together a simple script, which queried a notion database and picked the posts which were updated, converted those notion posts (basically blocks) to their corresponding markdown counterpart, saved them on disk, git add ed, and git commit ed those changes (if any). Cloudflare CI/CD did the rest. ez.



Was thinking about wrapping this whole thing up in a simple webpage/API so that I can revalidate my posts by making a visit to a page. Something like revalidate.lolwierd.ml. But I lazied out.

As of now, this setup works pretty well. I even have a bunch of metadata (like tags, tldr, description) in every notion page which gets added as frontmatter to each post. 



I also moved all my personal services, from nginx to caddy, which took wayyy longer than expected.

# How bout u redirect yourself to some bitches??

Setting up caddy was a breeze, the Caddyfile convention is very straight forward and it comes with some very sane defaults (like auto HTTPS, http/2, etc) so I didn’t need to worry too much.

All I had to do was move every server block in my nginx.conf to its corresponding Caddyfile counterpart. It took like 10 minutes.

But all of my sites stopped working.?.?.??

None of them loaded. ERR_TOO_MANY_REDIRECTS in all of them. Now, I knew it was Cloudflare’s fault because who else could it be?



I disabled automatic HTTPS rewrites and set the SSL/TLS encryption mode to off. Still nothing. Still too many redirects. WTF, I exclaimed audibly. After a bunch of digging, I found out the only way to make this work was to either set Cloudflare SSL/TLS to Full and let them serve their own certificates on the client side or disable the Cloudflare DNS proxy.



Cloudflare DNS proxy does provide a very useful feature which I need. CDN. So the second solution was a no-go from the get-go. The first one worked.



This shit took about 4 hours which shouldn’t have taken more than 10 minutes.

There is probably some lesson here about life and its unpredictable nature but it's probably too generalized or too nuanced for me to talk about.



Also after this, I got 2 more servers (making the total 3) and set up Tailscale ssh on all of them. I like not having to manage private keys. That part was actually easy and took like 5 minutes. Gotta love Tailscale.



Next, I set out to set up my own mail server!!

# The orange site told me this is very hard.

I wrote a scuffed af Twitter [thread](https://twitter.com/LOLwierd_/status/1566688228169875456?s=20&t=yrZbybcfF0Ihc0IKwkcdZw) as I went about building this.



I used maddy and spent the better part of a day setting up the DNS records and configuring the maddy service and maddy.conf. Now, the only thing remaining was to create a user account and start using maddy. The permissions in the certificate I generated using certbot gave me some headaches but it was nothing compared to what I had already gone through because of permissions in Linux.

The maddy docs talked about a CLI tool named maddyctl and how to use it to create user accounts and IMAP accounts. The only problem was that tool was nowhere to be found. I even tried compiling maddy from scratch on my machine, but it didn’t spit out maddyctl, only maddy. At this point, I was done for the day and popped a question in the discussion part of the GitHub repo and called it a day.



The next day, the author actually replied to my concern and explained he had merged maddy and maddyctl. Now, this should have been an obvious thing to try. I didn’t. I felt so very dumb after, but I dragged on. Created a user, opened some ports (for SMTP, IMAP, etc), and it worked!!



Added the IMAP account to thunderbird, sent an email to my domain, and there it was, sitting in my mailbox!!. EZ. But when I tried to send an email it was a completely different story. 

You see, Gmail, outlook, and all these big corps are very picky regarding which IPs they actually accept emails from. Which, for reducing spam is a good thing but makes it very hard to self-deploy a mail server. I had this server on oracle and so I didn’t own the IP. Which makes it almost impossible for me to add rDNS PTR records, which Gmail requires in order to send emails to their servers. Also, they have a (soft) requirement for DNSSEC, which is impossible in my case as my registrar, freenom does not support them ☹️.

So as I found out, the only option available for me is to use an SMTP relay to deliver my emails. Oracle provides email deliverability for free for 100 emails/day for life, and Sendinblue provides the same for 300 emails/day. No extra points for guessing which one I chose.

Both ofc.

So after setting up these SMTP relays, my email deliverability worked pretty flawlessly as well. I can send and receive emails at me@lolwierd.ml ez.



Wasn’t as hard as I was told it would be. Maybe because I offloaded the MTA to SMTP relays. But nonetheless, it works and I intend to make full use of it.



I also got my plex working properly. It worked pretty well on my pi but deployed one more instance on my main-server for friends to watch. Also deployed flood for torrents. Both servers are on the same account and I fully intend to buy a lifetime subscription in the future.



Now time for some personal talk. 

# Mental Reset.

Very very sure that these coming 5-6 months will decide how the next 5-10 years of my life are going to be. 

Currently aiming for a GATE rank which would allow me to pursue my master's at IISC, Banglore.

Seems pretty unreasonable but I idc. I will be ramping up the velocity at which I am going at it.

Which will require kind of a mental reset and restructuring of how I spend my day and when I wake up and sleep.

If I’m successful I would be where I want to be in the next 5 years, if I’m not, I am sure I would be happy either way.



Brute force seems to be the only way of going at it. Wish me luck. I doubt I would need it though.

2ez4me.




