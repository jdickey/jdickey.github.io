---           
layout: post
title: Change is the Only Thing that Never Changes
updated: 2015-02-16 01:48:00 SGT
tags: [tumblr, mac, docker, virtualization, virtualisation, devops, web development, change, migration, vagrant, chef, puppet, standards, standardisation, best practices]
categories: [not yet categorised]
comments: true
fullview: false
---

I keep running across questions on various lists along the lines of "why is agile better than waterfall, besides it not being waterfall?" The answer to that bit me once again over the weekend: agility is better because, done reasonably well, it responds to change far better (by any standard) than waterfall does.

The straw that broke *another* camel's back was virtualisation, more specifically on Macs; more specifically still, "which of the available VM packages for Mac ([VMware Fusion](http://www.vmware.com/products/fusion), [Parallels Desktop](http://www.parallels.com/products/desktop/), or [Oracle VirtualBox](https://www.virtualbox.org/)) do you use, especially considering that Parallels is Mac-only?"

Three years ago when I looked at all three with a view towards running (multiple) Linux servers for Web application development on our Macs, the choice seemed a pretty clear no-brainer: Parallels was essentially Windows-only, and since we didn't (really) care about Windows, was irrelevant. OpenBox, being free and open source, had two big pluses going in, but made glaciers' movement look supersonic in comparison, especially when running multiple VMs concurrently. That left VMware which, although being marketed by Big Corporate *for* Big Corporate, ran anything I could throw at it (unlike Parallels) with quite-acceptable speeds (unlike VirtualBox). We bought a license for every developer and test server we had at the time and went on to worry about other things (like shipping product).

Three years on, [Chef](https://www.getchef.com/) and [Puppet](http://puppetlabs.com/) and [Vagrant](http://www.vagrantup.com/) and especially [Docker](https://www.docker.com/) have come along and eaten ever-more-significant chunks of the development and devops world. They each depend on virtualisation, and they each depend on VirtualBox.

*Don't* try to install VirtualBox on a Mac that already has Fusion on it (or, presumably, vice versa). Based on our experience, you'll have to do a bare-metal (or Recovery Mode) install from scratch to get the selected survivor up and happy again.

Fusion is smooth, fast enough, and with enough features to handle most anything you'll want to throw at it, from Ubuntu Breezy Badger to the latest Debian-based derivative to some bizarre OS that only you and the development team and three bloggers will ever hear of; if it runs on bare metal, it is *highly likely* to run in a Fusion VM. **But&hellip;**

&hellip;the dev/ops world has standardised on OpenBox, and looks set to stay that way for some time (until the *next* massively disruptive change). While both vendors may swear on a stack of manuals that you *should* be able to just uninstall one and reinstall the other, our experience indicates that you'll be better off with a fully sterilised Mac OS X underneath.

That's the one part of the necessary that Docker and the rest simply can't automate the pain out of for you.
