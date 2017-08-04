---
layout: post
title: "Using Tools and/or Solving Problems"
description: "A funny thing happened on the way to deployment"
tags: [tools, deployment, devops, problem solving, rant]
categories: [practices]
comments: true
fullview: false
---

Are you a developer who views your job as primarily *solving problems* or as *building and using tools?*

Our culture has a word for when the second becomes overly separated from the first: *bike-shedding.* Yet, no matter how diligently you focus on "accomplishing the original mission" or "moving the ball down the pitch" or whatever military/sports trope you like, at some point you'll say to yourself "hey, I've been doing this same 37-step process every other day for the last month; I bet I could save *a lot* of time by automating it."

<figure style="padding: 4rem 0;">
<img style="margin-left: 20%; width: 60%;" src="https://imgs.xkcd.com/comics/is_it_worth_the_time.png" alt="Is it worth the time? Probably not"/>
<figcaption style="clear: both; text-align: center;">Relevant <a href="https://xkcd.com/1205/">XKCD</a> wisdom</figcaption>
</figure>

Raise your hand if you've *never* **far** overshot those limits.

*(deafening silence)*

Thought so. What brought this on, you ask? Funny you should ask.

I've been a happy customer of [Digital Ocean](https://m.do.co/c/e3f4d7dea0e2) for a while now. Their VPSes (which they call *Droplets*) come in either traditional bare-OS flavours (Ubuntu, CentOS, CoreOS, etc) and "one-click app" installations (Rails, Node.js, Docker, and so on). They have excellent [API documentation](https://www.digitalocean.com/community/tags/api) and [community tutorials](https://www.digitalocean.com/community/tutorials), and are, *for the most part,* a joy to work with and on.

One of the tools they maintain and support is [doctl](https://github.com/digitalocean/doctl), an open source tool with a relatively [mature and regular release history](https://github.com/digitalocean/doctl/releases). This lets you create and manage Droplets and other Digital Ocean resources (like *Floating IP*s, which let you, e.g., register an IP address with a domain registrar and move that IP between Droplets as you provision updates). However, the `doctl` tool, like the API it exercises, *does not* easily support configuring and managing the software running *on* a Droplet; for that, you'll need `ssh` or a tool like [Ansible](https://www.ansible.com/), [Packer](https://www.packer.io/), [Buddy](https://buddy.works/), or a host of others.

*And that's where the **rant** comes in.*

I've spent the last month, off and on, teaching myself enough Ansible to get by. I have two Droplets that I built and configured using Digital Ocean's [Web control panel](https://cloud.digitalocean.com/droplets) a month ago; let's call them `server1` and `server2`. I can use `doctl` to manage them (power up/down, swap a Floating IP between them) as I wish; I can use *ad hoc* Ansible commands like the following:

```
$ ansible -m shell -a 'docker pull jdickey/an-image:dev24' server2
server2 | SUCCESS | rc=0 >>
dev24: Pulling from jdickey/an-image
# ... ~30 lines of detailed logging...
8c5df88fde12: Verifying Checksum
8c5df88fde12: Download complete
8c5df88fde12: Pull complete
e2b2fc459b42: Pull complete
Digest: sha256:7a4f6098e01a5211b14b63428f9cdcd9929724bf83500f37745acfb1c3655181
Status: Downloaded newer image for jdickey/an-image:dev24
$
```

That's fine. Now I have two newer droplets (let's call them `demo1` and `demo2`) *configured identically to the first two* that I created with `doctl`, that I can `ssh` into but that Ansible gets blocked from:

```
$ ssh jeff@$DEMO2_IP
# ... banner ...
jeff@demo2:~$ docker ps -a
CONTAINER ID        IMAGE                    COMMAND                  CREATED             STATUS              PORTS                NAMES
c74b5515f893        jdickey/an-image:dev24   "/bin/sh -c 'pushs..."   3 hours ago         Up 3 hours          0.0.0.0:80->80/tcp   eager_meitner
jeff@demo2:~$ exit
$ ansible -=m shell -a 'docker ps -a' demo2
demo2 | UNREACHABLE! => {
    "changed": false, 
    "msg": "Failed to connect to the host via ssh: Permission denied (publickey).\r\n", 
    "unreachable": true
}
$
```

I've had people suggest "Use Packer", "Use this other tool", "Nuke and reinstall Ansible".

In other words:

> Don't sweat the problem; keep swapping out tools until one of them Just Works.

Aside from that being *horrifically* bad devops practice, it doesn't answer the real questions that urgently *need* an answer:

> What changed in the DO Droplet provisioning between a month ago and now that broke Ansible? How do I get *Ansible* working again with new Droplets?

Because, *until and unless I have answers for those,* I have *zero* confidence in any other tool (or any other deployment provider, for that matter). If you don't understand why your tools broke, then simply using new random tools won't help you prevent, or at least react effectively to, the *new* tools breaking. And the assumption you *must* make is that they *will* randomly break, until and unless experience shows you otherwise.

### Postscripts

I don't mean to be casting aspersions on Digital Ocean, Ansible, Pusher, or the well-meaning but short-sighted folks telling me to "just get on with it, already". I'll bet that there's some doc out there somewhere that I just happened to miss that tells me exactly what's broken and why, and how to fix my process to deal with the change. If you know of such a thing, **please** comment below or ping me on [Twitter](https://twitter.com/jeff_dickey) or Gitter.


