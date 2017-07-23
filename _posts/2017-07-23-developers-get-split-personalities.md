---
layout: post
title: "Developers Get Split Personalities"
description: "Productivity and perfectionism CANNOT mix. Deal."
tags: [tools, productivity, development, craft, technical debt, knuth, ruby]
categories: [practices]
comments: true
fullview: false
---

How many times have you trudged through brownfield code, say, from three months ago, and said to yourself "This is *manure;* I can't *believe* [anyone] wrote this", only to be told (by others or your memory) "we just needed to get it done and get on to the real work"?

How many times have you discussed [technical debt](https://blog.codinghorror.com/paying-down-your-technical-debt/) with colleagues, or even read about it? How many times have you experienced, directly or vicariously, the phenomenon where technical debt, once accrued, too rarely gets paid down until/unless a Greek-finance-class crisis forces the team to stop "the real work" and do the *hard* work of turning the house of cards you were trying to build "just sixteen more stories" onto into a steadfast, coherent foundation that you can (with proper discipline and respect) build *hundreds* more stories on?

How often has that exercise in austere self-discipline broken down, and you've agreed "screw this; we'll write it again from scratch", only to be back in the *exact same place* a year or two down the "new" raod?

Yeah, those are embarrassing numbers for me, too. I presently live in a society (Singapore) that is *existentially* hostile to admission of even the most innocuous of failures, and is thus so risk-averse that we *suck* at anything requiring creativity, teamwork, and/or original thought. (How many Singapore companies has the outside world heard of since Creative Labs, more than 30 years ago? How's our national football team doing this year? Oh, yeah; we just got thrashed **7-0** by Australia, whom we've never beaten.) And yet,

> There is no innovation and creativity without failure. Period. — Bren&eacute; Brown

We can't be perfect. We can't make anything perfect; certainly not within our (imperfect) understanding of the limitations imposed on us by our (imperfect) economnic and social self-imposed realities. We *can* be "good enough". We *can* "make things a bit better than they were before". We *can* follow the [Boy Scout Rule](http://www.informit.com/articles/article.aspx?p=1235624&seqNum=6) and "leave the campground" (our code) "cleaner than we found it".

We have some great tools to help us do that. If, for example, you work with Ruby code, you should be familiar with [RuboCop](https://rubocop.readthedocs.io/en/latest/), [Flog](https://github.com/seattlerb/flog/), [Reek](https://github.com/troessner/reek), and [Inch](https://github.com/rrrene/inch). They *can* help you ship better code, faster &mdash; or they can help you launch yourself into an endless cycle of [bike-shedding](https://en.wikipedia.org/wiki/Law_of_triviality) and navel-gazing that is usually broken only by a terminal event.

That cycle *can,* however, be broken by determined self-discipline. One of the more reliable ways to encourage that determination is to use at least some variant of [pair programming](http://wiki.c2.com/?PairProgramming). Many of the foundational principles of modern development presume pairing; for example, in Sandi Metz' classic [Five Rules for Developers](https://www.youtube.com/watch?v=npOGOmkxuio&feature=youtu.be) talk, at about [9:51](https://www.youtube.com/watch?v=npOGOmkxuio&feature=youtu.be&t=9m51s) she defines "Rule #6", *Dispensation,* as "Convince your pair!".

> You can break any rule you want, as long as you can talk someone into believing that it's a good idea.

Pragmatism for the win? Often. What the "rules" encourage, and the tools I mentioned above (among many others) encourage, is the creation of numerous small, granular objects without non-transient state. This makes a lot of sense in Web development that revolves around the request-response cycle. It also apparently nails Robert C. "Uncle Bob" Martin's [Three Rules of TDD](http://butunclebob.com/ArticleS.UncleBob.TheThreeRulesOfTdd); if you're writing the smallest new code that can make the smallest new tests that can possibly fail, pass, then you *should* wind up with a collection of small, granular objects without much individual state.

When you're pairing, it's a lot easier to maintain that discipline, after agreeing on what "smallest possible change" means. A feature? An object? A method? "It depends?" Whatever your convention, your pair is there to keep you out of the bike-shed and moving forward.

When you're not pairing, you *will* spend a lot of time in the bikeshed, *especially* if you have static analysis tools like RuboCop and Flog that you run in conjunction with or as *part of* your commit and CI workflows. You start out being gently nudged by, say, Reek pointing out a [Utility Function](https://github.com/troessner/reek/blob/master/docs/Utility-Function.md) here and [Feature Envy](https://github.com/troessner/reek/blob/master/docs/Feature-Envy.md) there, and pretty soon you're just...

<img style="width:60%; margin-left:20%" src="https://media.makeameme.org/created/shut-up-wesley.jpg">

Alternatively, you hunker down. You take your ten-method, hundred-line class that *worked* and gave you a green bar with 100% test coverage, but also gave you a Flog score of 30+ on each of three methods and you had sixteen comments disabling Reek and five disabling RuboCop checks, and you turn it into twelve classes totalling 450 lines with no method longer than five lines or Flogging higher than a 7.0. You're pleased with that, except for being faintly annoyed/guilty that it took you *three days* to get there.

Don't let this happen to *you!*

The tools aren't giving you "bogus results"; you're simply misusing them and/or the "agile" process you set out to use. On the one fork in the road, you spent three days (likely a couple thousand dollars' worth of your time to your employer) fixing something that *already worked and was proven to work* instead of moving on. "B-but, Reek and Flog and RuboCop said I had bad code," you whimper. No, they didn't; they said your code had some code smells (where bugs are *more likely* but *not proven* to be), and that it didn't comply with some common-sense style standards that an open community discussion had agreed were *usually* Good Ideas. But, again, "you can break any rule you want, as long as you can talk someone into believing that it's a good idea". The one thing you *can't* break as an agile developer is test coverage yielding a green bar; any code (statements, branches, conditionals) not covered by passing tests **must** be presumed to be defective until and unless proven otherwise. (This is part of what makes writing tests first a Good Thing.) If you have solid, trustworthy tests, then the style guidelines and analysis metrics are just that &mdash; guidelines and metrics. When you find bugs that your tests didn't catch, look to your guideline violations or egregious metric scores to help you narrow down likely places to look to figure out where the bug, or contributing factors to it, are hiding. Then write (passing) integration and unit tests (in that order) that exercise the bug; i.e., a green bar proves that the bug exists and that your understanding of it is correct (not necessarily *complete,* but you can get there). Make that unit test fail and see if the integration test also fails. (If not, then killing the bug is going to take more work.) Finally, replace them with tests that prove that (that aspect of) the bug has been fixed, up to and including your integration testing. *These* are the times that your static-analysis tools will help you  rather than distract you.

"But aren't high tool (Reek, Flog, disable-Rubocop-rule-comment count) scores indicative of technical debt," you ask. Yes, they *indicate* where you have code that likely could be improved, when it makes sense to do so taking resource constraints into consideration. You might want to keep a "technical debt" Trello card list or GitHub issue label, and write up cards and/or issues for each such instance. But no matter which flavour of agile development you use, *well-written tests* are the primary artefact proving that you have working code. Remember the Golden Rule of Development. "He that has the gold, makes the rules?" No, the *practical* Golden Rule,

> The real problem is that programmers have spent far too much time worrying about efficiency in the wrong places and at the wrong times; premature optimization is the root of all evil (or at least most of it) in programming.

Donald Knuth said that, in his *1974* Turing Award lecture, [*Computer Programming as an Art*](http://www.paulgraham.com/knuth.html) (CACM 17(12) p. 671). Forty-three years ago. Before the birth date of my average technical colleague over the last 15 years or so. If you came into the craft from a [JavaSchool](https://www.joelonsoftware.com/2005/12/29/the-perils-of-javaschools-2/) or a "bootcamp", you've probably never read him. That's a bug, not a feature.
