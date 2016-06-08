---
layout: post
title: "A Plea for Tools"
description: "The Rails ecosystem has beaucoup tools. Other Ruby ecosystems?"
tags: [tools, teamwork, development, onboarding, ecosystem, ruby, standards, frameworks, gems]
categories: [development, teams, standards]
comments: true
fullview: false
---

While reviewing a basic Sinatra app from a dev who's newly returning to the craft after a many-years-long absence, I noticed that a Git repo with 3 commits in it took 21.5 MB to transfer. The experienced among you can probably figure out what happened; the Vim backup files (`foo.rb~` and friends) were being saved, along with his *entire* `vendor/` subdirectory. Further, his `.gitignore` appears to have been cargo-culled from a Rails app.

It's easy to blame the guy; after all, there's an abundance of reading material for Git out there (tutorials, books, etc). But it's not completely fair; we don't (yet) have shop standards for setting up a non-Rails project at all (and the Rails-project doc assumes a certain level of knowledge and experience). As Chief Engineer, lead developer and, in truth, the only senior dev in our shop, it's my responsibility to set standards and/or write documentation for things like that — on top of the other things I do in my 90+-hour work weeks, like write code that "ought to be" written by the 4-5 devs we *don't* have, or covered in the training paid for by the similarly nonexistent budget.

I'm familiar with several base Rails apps such as Thoughtbot's [`suspenders`](https://github.com/thoughtbot/suspenders) and with various Rails app builders; we're branching out into and/or evaluating other frameworks as well. Does anyone have any recommendations for "starter kits" for non-Rails Ruby apps &mdash; Sinatra, Padrino, Roda, etc?
