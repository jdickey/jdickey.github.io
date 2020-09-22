---
layout: post
title: "In Defence of Use Cases, and the Service Objects Encapsulating Them"
description: "Many service objects in production serve as excellent arguments against the entire concept."
tags: [development, service objects, use cases, ruby, rails, Ruby on Rails]
categories: [Development, Practices]
comments: true
fullview: false

---

### An Appetiser

[Code With Jason](https://www.codewithjason.com/) is a generally *outstanding* site where Jason Swett offers courses, tutorials, and opinion (generally) related to Rails and/or Ruby in general. Almost exactly a year ago as I write this, he posted a quite well-thought-through article, [_Beware of service objects in Rails_](https://www.codewithjason.com/rails-service-objects/). I agree with quite a lot of what he says. For example, I, too, treat class names ending in `-er` (`MoneyManager`, `CorrencyConverter`, `WishGranter`) as code smells until proven unavoidably otherwise. (I haven't seen such an *unavoidable otherwise* counter-example in *decades*.)

However, I've been on teams that have effectively used use cases (more typically for the last ~30 years, Jacobson [_use cases_](https://en.wikipedia.org/wiki/Use_case)). These have been in a variety of languages, from C and its derivatives; to OO languages like Ruby, Objective-C and Delphi; to functional languages like Scheme and Elixir (in which I'm still far closer to the novice end of the spectrum than I'd like). I say all this to give some clue as to how my (too often self-)education as a software (and, in recent decades, Web) developer has progressed. I was working with Rails from 2.1.*something* up to 4.1.0, followed by a segue into other frameworks until some recent 6.0.3.*x* work. I don't claim to be a modern Rails expert yet (I've been working on [Hanami](https://hanamirb.org/) code for the last couple of years), but I've done enough OO in Rails, Ruby, and elsewhere to have formed opinions. (My current touchstone is Clean Architecture, but that's a whole other series of posts.)

What follows was drafted in late October, 2019, as an open letter and/or rant in reaction to Jason's [post](https://www.codewithjason.com/organizing-rails-projects-domain-objects-good-service-objects-bad/), with relatively minor revision and expansion in September, 2020.

References to "we" and "our" denote usage and standards for the small startup where I serve as Chief Engineer and director of all technology-related work (development, quality engineering, dev/ops automation, etc.). Occasionally, I refer to myself individually, using a first-person pronoun.

----

### Discussion is Served

We've been using service objects in Ruby for a few years now; I've previously used them in a wide range of other languages, OO and otherwise. Given a bit of reasonable self-discipline (perhaps best supported by the [Clean Architecture](https://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html)), they can be really effective. But I agree with your dings against the examples, to the point where they almost might be considered straw-men were it not for the fact that they are too likely in production code running somewhere. I'd like to argue for use of the concept within limits, by riffing on a few of our

**Use cases are implemented in one or more cooperating service objects.** A *use case* encapsulates an action performed by or on behalf of a user. *Register as Member*, *Publish Article*, and *Respond to Proposed Contribution* are use cases that each make use of one or more service objects (which generally, as is traditional, have a single entry point which returns a "result" object encapsulating the success/failure result of the service usage, along with appropriate entities on success or error information on failure. These are black boxes into which necessary dependencies are injected along with the per-use parameters.

**Service objects should be _pure domain logic_.** Any outer-ring (to use [Clean Architecture](https://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html) imagery), such as frameworks, databases, and the like, are to be either injected or wrapped around the use of the service object itself. Your TopTal `CurrencyExchanger` example is flat-out egregious, and in my opinion, at least, should never have passed anybody's code review. You do an excellent job of damning it by visibly pulling several of your punches.

The point of using a use-case-driven approach leveraging service objects is to minimise both the cognitive load on the developer (oberwhelmingly singular) working on that code at the moment, as well as to isolate it from the rest of the application, preventing Debugging Whac-A-Mole<sup>&reg;</sup> as the hapless developer has to stop and think (and `grep`) to figure out why data that "has no good reason to change" *is* changing. After four decades in this craft, I have yet to meet a developer who's shipped at least one academically or commercially interesting product who hasn't beaten her head against *that* at least once.

One of the defining bullet points for OOP, remember, is *encapsulation*. Good architecture, Clean or otherwise, applies that at larger scale than the individual class. That is a nearly insurmountable challenge when attempting to maintain a legacy Rails app written in the traditional Rails Way.

Models and controllers, themselves, simply don't have the necessary depth to fully delineate a sustainable architecture for modern applications. For a post-2010 or so product, you're going to have to add at least one more layer in a hopefully successful attempt to keep your developers reasonably sane and your schedule-focused managers reasonably appeased. We believe that taking well-defined and -understandable concepts that have been proven in production is highly preferable to a one-off, everybody-does-it-their-own-way approach. DHH may well rail against anything that adds layers to the original NVC-style layout, but I'll bet Basecamp uses something recognisable as service objects &mdash; even if they're wrapped up in separately-deployable "microservices". That just takes the service object out of the monolith and puts it on the other end of an HTTP or RPC connection that *can* fail.

Thoughts?