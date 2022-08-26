---
title: "How I made these Blogs"
date: "2022-05-29T23:51:58+05:30"
description: "Deployment is hard. Sometimes. Also, fuck DNS"
tldr: "Fuck Cloudflare. Thank god for Cloudflare."
tags: ["tech", ]
draft: "false"
---

I wanted to have my own blog for quite some time. Originally my plan was to build it in ruby using Jekyll or Ruby On Rails. But I gave up on Jekyll due to me not finding good enough themes. I was NOT going to build it from scratch. I do not yet possess the needed patience and design skills to pull it off (read as I was not serious enough). I came across Hugo a while ago and knew it was the fastest and also the easiest, so I looked it up and found a decent theme, and here we are.

# The Easy Part

Coding the actual website was absolutely the easiest part. I did not even have to look up its documentation or any tutorials. It was so simple I figured it out just by looking at the theme’s README, and its code. So, what comes next is how do I deploy it and how do I write new posts and trigger deployments efficiently and easily. I did have to figure out git submodules and all, but that was as trivial as I expected.

# The Hard Part

Now I had broadly **2** options.

1. Deploy using services like Vercel, Netlify, etc.

1. Use my own server.

At the time both of them sounded very very lucrative.

Using services would mean less work because they would handle auto-deployment on every git commit. Using my own server meant, I would get to figure out how these services that auto deployed on commit really worked under the hood.

On a good day, I would have gone for the second one without a second thought. But today was not a good day. I had just finished AOT manga the other day and residual sadness was still lingering.

Option 1 it is.

## This seems trivial I wonder who would be dumb enough to spend more than an hour doing this

First try was Netlify because I had read somewhere it supported Hugo.

So I signed in, connected my GitHub, gave it access to this one repo, and it worked! Ofc. Right? Ok no. CSS wasn’t working. A quick look at the network tab showed that it was our good old friend CORS (also the fact they just did not exist at the requested URL). I had given a different baseURL (blog.ayaan.ga originally) in Hugo config.toml so while generating, Hugo put absolute URLs in generated files. No worries. I thought, it will sort itself out as soon as I add my custom domain. EZ.

Fuck no. Cloudflare DNS started actin’ up. Thinking back, maybe if I had flushed my local DNS, it would have probably worked later. idk. Too late for that. Anyways ***Note to Self***:

> When working with DNS, ALWAYS purge local DNS before accusing the DNS service.

So, CNAME stopped working. I did check on leafdns, but there were no signs of life. Google told me to shift my DNS to netlify and it would work 100%. But I am definitely not dumb enough to do that.

OK no big deal, I did not have high hopes for netlify from the start anyways.

## Vercel is amazing, right? It just works every time. Why would it be any different now?

Alright. I was sure this was it. I mean, Vercel has never once failed me. Never. So I looked it up and sure enough, Vercel supported Hugo.

So I signed in, connected my GitHub, gave it access to everything I had, and it worked! Ofc. The CSS thing still existed. No styling, but solving that’s supposed to be the easy part, right?

Fuck No. Cloudflare DNS was still actin’. Idk what I was expecting tbh. Ofc if it did not work with netlify, why would it magically start to work just because it’s vercel now? CNAME records were not propagating for some unknown fucking reason. I am pretty sure it wasn’t my local DNS because I rechecked with dnschecker this time.

Also, I did give it a fair amount of time this time to let it propagate. Ofc it can be true I did not give it enough time the last time. But who cares? That was netlify, this is vercel. Vercel always gets the benefit of the doubt. Always.

But does that bitch deserve it? That's a completely different story. It did not work. So I googled a bit more, and found out, that if I shift my DNS to vercel, It would work 100%. I was definitely dumb enough to do that. (No shade to netlify but come on this is vercel we are talking about)

As I was mentally preparing to manually migrate my 10+ DNS records from “trusty” old Cloudflare to Vercel, I changed my nameservers on freenom to those of Vercel’s. This was it. It should just work. It should. Come on…. This is vercel right??????

Mfer did not work. But looking back, I am pretty sure I was the mfer back then, I totally should have flushed my local DNS cache. It would probably have worked. I did not check with leafdns or dnschecker this time idk y. I just forgot.

But well, 🤷‍♂️. This was more for testing purposes anyway. I would not have kept this forever. I was not going to manage my DNS partly on Cloudflare and partly on vercel.(I do have one other domain. One you are reading this on)

So Vercel did not work as well. This is a first. Though not really ‘not worked’, Maybe I was a dumb fuck here.

Also this time I even tried to host it directly on my root domain (ayaan.ga). But that failed to work as well. I really need a time travel debugger for real life. I would be sooo much better if a thing like that existed. It would suit my way of learning very well.

## Time to roll ma sleeves up

I ssh’ed into my main-sever (OCI, 4 OCPU, 24 GB, arm), and prepared for the worst. I had a number of challenges to overcome before first if I was to actually actively write blogs. If I had to ssh into my server run git pull and rebuild every time I wanted to redeploy my blog, it would never work for sure, never, I will never be that dumb I promise. If I turn out to be though I hope I have the required self-consciousness to know so that I can slap myself.

Thoughts like these make me want to re-believe in god. But that's a conversation for another time.

Ok. I thought I knew what CI/CD servers did. Turns out I didn’t and possibly still don’t. I do plan on figuring that out though, soon enough. Ok, so I looked up top self-host-able CI/CD servers, and I as read on I recognized some names, like Jenkins, CircleCI, etc.

I had heard good things about Jenkins, so I looked up its Installing section and to my surprise, it did not work. What? How in the world can the Installing section be wrong? Isn’t that the most important piece of paragraph or steps in the whole fucking website? If your users can’t install your software how the fuck are they supposed to … idk … use it? Ok never mind, maybe they forgot to update it. Googled that shit and found a stack overflow answer from a day ago (that's a first) linking the .deb file. I wget’ed it, installed it and it did not work.

Fuck that. idc. Ofc there are better CI/CD servers in the wild, I was not going to be spending my time looking for the right .deb. Surely my time was worth more than that. Learning Jenkins can wait until after they start taking their docs seriously.

Next thing I remember I was looking at TeamCity. JetBrains has always been good. No way they make a subpar software that would not work as I wanted it to. Maybe because of my ignorance, but I am not thaaaaatt self-conscious to admit that. (😉)

Ok fair enough their ‘How To Install’ docs worked and I had a fresh install of TeamCity in front of me. But as expected, I had an expected holdup.

As any good dev should, I use Tailscale to connect with my multiple servers, and maybe use them as exit nodes (traditional VPN endpoints). It’s a brilliant piece of software with an amazing free tier. It has never once failed me and I very strongly believe it will never.

1 amazing feature of Tailscale is that it gives me access to my remote machine’s local network (it builds a P2P WireGuard tunnel between connected devices). So, all I need to do is main-server:8111 (MagicDNS!!) to access the localhost:8111 of my remote machine. That stopped working.

I knew there was no way its Tailscale’s fault. I checked its status, restarted it and sure enough, it was not it. Then I remembered when I installed it, it had instructed me to run some firewall-cmd commands (idk what those meant I am very bad at Linux networking. For now.). Sure enough, all I needed to do was start firewalld.service and there it was. EZ.

Ok enough of this filler shit. I went through the setup of TeamCity, installed psql, gave TeamCity its credentials to use as a Data Store, and it worked!

So tldr; TeamCity worked but it didn’t. At the same time. I feel like it did everything it promised, but that was not exactly what I wanted, and I refused to learn what it was exactly. In short, I know how to set up TeamCity and make it do what it is capable of.

But that is not what I wanted to do, is it? I said fuck it, cloned my repo, installed Hugo, generated public/, and looked up how to use Nginx to serve static files. Simple enough right? No way this does not work. This is as bare bones as it gets (sans building my own web server, CI/CD server but that's way out of my league).

### OMG I cannot quantify how much I hate Cloudflare

Cloudflare still did not fucking work. And this time, I am sure it was not my local DNS. idk why but my subdomain blog did not work. How that does even happen? Everything else worked. Later on, I even tried blogs.ayaan.ga. That worked. Blog refused to.

So, I said fuck it blogs it is and started solving the CSS issue. To solve that was trivial. All I had to do was put blogs instead of blogs in my config.toml for baseURL and it should just work.

So ofc it did not. Fuck whatever I expect I guess. I almost gave up trying to figure out why it didn’t. It was wayyyy more convoluted than what I could have dreamt. First I thought it was Hugo and tried every single thing under the sun. I literally read every single link on the first page of google search results. Nothing worked. The next logical step was debugging Nginx. It had to be nginx. I did the same thing with nginx which I had done did with Hugo, but no results. I did learn one very unexpected thing though. CSS was being fetched all right by the browser. But that mfer refused to apply those styles. WHAT? Is that even possible? HOW? It was trivial ofc. The type of those files was text/plain. So that type tells the browser what to do with the file. Ok makes sense.

I looked it up and they told me to import mime types in my Nginx server. But that did not work idk y. It feels like it should. But it very surely did fucking not.

I had this peculiar setup as I used Cloudflare as my DNS provider. That mfer wants to give everything under the sun to you. And I do not want everything under the sun no. I wanted to use Cloudflare only and only as my DNS provider. I thought I had turned off Cloudflare trying to provide me HTTPS Certificates, and only had it redirecting my website to HTTPS if I made an HTTP call by mistake. Turns out, I was wrong. That bitch was provisioning its own certificate. ***I think*** (idk) I think, that's why my CSS was not working.

I have some basis for thinking that because as soon as I switched that off which also switched off HTTPS redirects, my website worked!!!!! CSS worked! Blogs subdomain worked!! But there was one issue. HTTP. I was not going to deploy my website like some half-assed mfer without HTTPS. Also for some reason, HTTPS redirects did not work if I set them up on Nginx. Too Many redirects it said. Cloudflare should not have been redirecting me. Idk who caused that issue. I am willing to bet it was Cloudflare though.

So, around this time, I was beginning to exhaust my brain’s bandwidth. HTTPS did not work. I still had no way to enable automatic rebuilds on new commits (Looking back, a simple webhook that ran on a commit on GitHub would have solved this).

I was like fuck it. Idc. Maybe ‘god’ does not want me to write blogs. Experiences like these make me want to re-believe in god.

So, as I was beginning to close my colossal number of open tabs, it struck me.

## Cloudflare surely is amazing huh

Cloudflare has this thing called Cloudflare pages.

If it supports Hugo, and I do everything on Cloudflare, surely it would go smoothly. Right??

Turns out Cloudflare supports Hugo, CSS started working after I changed baseURL to the Cloudflare pages deployment, HTTPS worked. But blog subdomain still did not work.

At this point, I was happy, but I was fed up as well. Too fed up. So I just changed the domain to lolwierd.ml, and sure enough, it worked, and here we are.

Woohoooo!!!! Cloudflare came back to save the fucking day after wasting half of it.
It solved all the problems, that it caused, using its own services.

OK. I thought, so now I have a way to document my experiences and try to learn more from them by penning them down. How about I have a personal version of this blog as well, which only I can access and maintain, like a journal, every day, and use it to become a better human maybe? Sounds like a good idea to me.

So I copied the blog folder on my local machine called it personal-blog, and started thinking how the fuck would I password protect it. Hugo is an SSG. So this access control thing would have to be on top of Hugo.

Then I remembered Cloudflare Access. Sure enough, I found out something similar was possible. I tried to make sense of it but it looked complex and I was very exhausted. So I shut down the Cloudflare Access thing, and googled some, and found [Octauthent](https://octauthent.com/).

PERFECT!!! Generated an access token from Cloudflare, fed it to Octauthent, and it worked flawlessly.

I was just about to close my laptop to take a break when I realized I would not be able to live with myself if I did not check out Cloudflare Access for real once. Also, Octauthent seemed reliable but I did not trust it enough idk y.

So, I looked around, made sense of Cloudflare Access, used it and it works pretty perfectly. Added Google Auth as well, so as to make logging in easy for me, though it does provide OTP for emails.

The personal Site is hosted at https://personal.lolwierd.ml. Accessible only by me.

I also kind of figured out my blog writing setup. It’s not perfect, but it is good enough. At least it’s not completely dumb. I currently use [Obsidian](https://obsidian.md/) as my markdown editor to write posts, then commit them manually (for now) push them to GitHub and everything else is automated after that.

Idk if this Cloudflare situation is good or not. It provides a bunch of services, HTTPS rewrites, Brotli Compression, Certificates, Always Online, Caching, Pages, CDN, Workers, etc, etc. I did not want to use all of these and wanted to offload some of them to other services, as you know by now, it did not go soo well for me. Idk if that’s good or bad.

***It took a while huh? I thought it would be over in an hour max. Fuck me for expecting I was better I guess.***
