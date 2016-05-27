---
layout: post
title: "Ruby Is Not Haskell, But..."
description: "Ruby isn't 'primarily' a functional language. But that doesn't mean that functional concepts shouldn't be used in Ruby."
tags: [ruby, haskell, development, craft, mea culpa, revision]
categories: [not yet categorised]
comments: true
fullview: false
---

First of all, welcome back. I've been up to my eyebrows in alligators for the last year and a half, and not blogging during that time. Memo to future wantrapreneurs trying to "motivate success" by encouraging people to work more than 40 hours a week: **You're kidding yourself.** Sure, crises happen, and during crises, people step up — but there's *orders of magnitude more than enough* data and analyses in the academic and popular literature that shows that productivity and quality take a controlled flight into terrain as that "crisis mode" continues. Your people will take longer and longer to bounce back and perform at their best, and that "new best" is likely to be significantly below the "old best" for quite some time. Continue that for a year and a half, or a far more pathological four and a half years, and permanent damage will be done.

----

The title of this post is a play on [my post from last April](http://jdickey.github.io/2015/04/11/ruby-is-not-haskell-deal/), *Ruby Is Not Haskell. Deal.*. In the last year, particularly as more explicit and/or functional Ruby code appears in the wild and is seen as valuable (see in particular the [dry-rb project](http://dry-rb.org)), I've come to realise that the objections I had (and failed to completely write up) were more stylistic and organisational than principled. In particular, my objections had more to do with the Haskell "flavour" of the [`kleisli`](http://blog.txus.io/kleisli/) Gem as [evangelised](https://blog.abevoelker.com/you-got-haskell-in-my-ruby-cleaner-ruby-validations-using-either-monad-kleisli-gem/) by Abe Voelker. It's *valuable,* but the resulting code feels so much more like Haskell than fluent Ruby that many Rubyists will be put off by it.

Enter dry-rb, and the [`dry-transaction`](http://dry-rb.org/gems/dry-transaction/) Gem which [depends](https://rubygems.org/gems/dry-transaction) on Kleisli. By implementing a more Rubyist-friendly DSL, and in cooperation with other Gems such as [`dry-container`](http://dry-rb.org/gems/dry-container/), many of the benefits touted by Kleisli and the Voelker blog post are made accessible to a wider community that doesn't *want to* (explicitly) "get Haskell in (their) Ruby". I expect that, as these Gems and projects that make use of them gain traction in the community, wider interest in and acceptance of purely, or largely, functional components will become apparent. Quite a number of Ruby bloggers and project maintainers have latched onto one aspect commonly associated with functionalism, *immutable data,* as enabling more reliable reasoning about code. If you have a (relatively) large collection of [SOLID](https://en.wikipedia.org/wiki/SOLID_(object-oriented_design)) classes, each of which, once instantiated, is immutable, it's far easier to find your way around (and understand) your code than if you have a handful of God classes with dozens or even hundreds of methods each. (And then there's Rails, which serves as a declaration that everything we've taught ourselves about good OO design in the last 50 years is wrong.)

I've been further influenced during this time by the writing of several great (IMO) developer/writers, particularly [Jim Gay](http://www.saturnflyer.com/blog/), [Piotr Solnica](http://solnic.eu), and [Andrzej Krzywda](https://twitter.com/andrzejkrzywda). Andrzej's recent Medium post, [*The journey to Rails and TDD*](https://medium.com/planet-arkency/the-journey-to-rails-and-tdd-da639d921e17), finally fully illuminated an idea light-bulb that had been flickering for some time, with his discussion of [domain-driven design](https://en.wikipedia.org/wiki/Domain-driven_design) as "the missing piece in my TDD flow" and his discussion of [bounded contexts](http://dddcommunity.org/uncategorized/bounded-context/). I've been organising classes in a frankly unhelpful way for some time; I've recognised it, but couldn't put my finger on a better system that "felt organised enough". BCs promise to help with that.

I expect to start having fun again with code. Especially if I can gain something approximating the heretofore mythical "work-life balance".

Thoughts?
