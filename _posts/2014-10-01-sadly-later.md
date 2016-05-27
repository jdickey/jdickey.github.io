---           
layout: post
title: Sadly, Later.
updated: 2015-02-16 00:45:00 SGT
tags: [tumblr, docker, development, web development, software, infrastructure, process improvement, virtualisation, virtualization]
categories: [not yet categorised]
comments: true
fullview: false
---

Like a lot of people, I'm really excited by *the promise of* [Docker](https://www.docker.com/). A reproducible, reliable environment that banishes "it works on *my* machine" from any team's vocabulary? What's not to like?

Well, if you're a less-than-Core-Team expert in Rails and all the details of how to provision/configure a Rails app and its entourage for production, you may find yourself beaten about the head by the learning curves, definitely plural.

1. The write-ups I've seen over the last week that describe working deployments are pretty universally describing running Rails on [Phusion Passenger](https://www.phusionpassenger.com/) and [`nginx`](http://nginx.org/). If you haven't yet mastered those tools, then you have some learning to do.
1. As previously with Heroku, those write-ups are nearly unanimous in describing either PostgreSQL for a SQL-based database and/or MongoDB as a NoSQL alternative. Using something else — MySQL, maybe? You have some learning to do.
1. Every single Mac we have has a copy of VMWare Fusion on it because, after evaluating it and the competition ([Parllels Desktop](http://www.parallels.com/products/desktop/), [Oracle VirtualBox](https://www.virtualbox.org/)) two years ago, Fusion was the hands-down winner for "if it runs on bare metal, it can run in a VM with decent performance". Since then, of course, the dev/ops world, including Docker (and Vagrant and Chef and Puppet and legion other projects, have standardised on VirtualBox. Which means that each and every one of our Macs is going to have to be cleaned off, reformatted and reinstalled, since I've learnt never ever *ever* to mix bits of Fusion and bits of VirtualBox on the same Mac that I hope to continue using reliably. (We're just at that sour spot where we're not *quite* big enough to have any sort of centralised system management.)
1. The tooling and documentation (particularly third-party tutorials) for Docker itself are, unsurprisingly, in a *very* early state. (When [O'Reilly](http://oreilly.com/), [InformIT/Pearson](http://www.informit.com/), and [Manning](http://manning.com/) each have no books, even early releases, on their websites, you know random blogs are your best bet.) You have some learning to do, and you're going to have to *work* at it.
1. Obviously, all the only-on-development tools and hacks you've been using up to now, such as Sqlite databases and running `rails server` are going to go away, since you're *replicating a production-capable environment* &mdash; that's the whole *point*.

All this together, along with some outside influences, has led me to, *very* reluctantly, delay my learning and exploration of Docker until we get past the current crush at work, complete the relocation of our development operations, and staff up sufficiently that *this* single point is no longer the critical path for *everything*.

----

Entropy, at least in the software world, is *not* a constant, but it's constantly *increasing*. I've been saying for 30 years that "70% of what you need to know as a developer will be out-of-date before your milk is; the rest will never be out of date". What that fails to take into account is that the *sum total* of "what you need to know" at any given time is increasing, as is the rate of increase itself.

Anyone else remember that old Waylon Jennings song, [*Mama, Don't Let Your Babies Grow Up to be Coders*](https://www.youtube.com/watch?v=Dgh6fDayDTI)?
