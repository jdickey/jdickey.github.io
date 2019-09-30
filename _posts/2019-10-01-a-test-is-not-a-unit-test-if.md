---
layout: post
title: "A Test Is Not A Unit Test If It Does Too Much"
description: "Thoughts on an epic Tweet defining what unit tests ARE NOT"
tags: [architecture, best practices, clean architecture, craft, software architecture, tweet]
categories: [Development, Practices]
comments: true
fullview: false

---

That, in a nutshell, appears to be the hammer that those arguing against Dave Cheney's excellent Tweet that [A test is not a unit test if:](https://twitter.com/davecheney/status/1178289934542655489). Allow me, folks, to offer some thought on why it's so easy to mistake your head for a nail in this context.

Taking Dave's four points in order:

1. *A test is not a unit test if it talks to the database.* It's an integration test, and the differences are important. The database access can fail for reasons *entirely unrelated* to your attempted use of it; that is then duly reported as a test failure or worse (e.g., an error, exception, `MajorMalfunctionInstance`, etc). [Assuming](https://www.urbandictionary.com/define.php?term=Assume) that databases are unfailingly reliable does not make them so. This is particularly true in production, but a depressingly regular occurence in test environments as well.
2. *A test is not a unit test if it communicates across the network.* The network is a world-class Heisenberg accumulator for the same reasons noted for the database in the previous point. Again, wishes don't make it otherwise.
3. *A test is not a unit test if it can't run at the same time as any of your other unit tests.* This is usually because either your test relies on outside events happening in a certain sequence (and is therefore an integration test) or it relies on exclusive access to an outside resource. Each of these reasons moves it firmly from would-be unit-test identity into the nebulously uncertain world of integration tests.
4. *A test is not a unit test if you have to do special things to your environment to run it.* This is a variation on the previous observation, usually relying on *both* outside-event sequencing and exclusive resource access.

"OK", you might well be asking, "what *is* a unit test, then?"

A unit test depends *solely* on the execution of *a* logic path through the code under test leaving observable artefacts in a predicted state. Further, these artefacts *should not* be global state, but changes of the state of the inputs to that logic and/or the value returned by that logic. Unit tests *don't care* if you're running the same test a thousand times in sequence, or a thousand times in parallel; each execution of the test (again, given the same inputs) will produce the same output.

"So how do I give the logic under test the resources it needs (and would have in production)" is the next question.

The answer is dependency injection, as included in the [Dependency Inversion Principle](https://web.archive.org/web/20150905081103/http://www.objectmentor.com/resources/articles/dip.pdf) (the _D_ in [SOLID](https://en.wikipedia.org/wiki/SOLID)) and, more explicitly, by the [Clean Architecture](https://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html). If your code under test takes its dependencies as parameter inputs (or, alternately, uses a DI container), then the code doesn't care if those dependencies are live resources (as in production) or test doubles. This becomes almost trivially easy in dynamic languages such as Ruby ("duck typing" is a thing) and JavaScript.

"Hm. It still doesn't sound all that useful. What would I use it for?"

Let me draw your attention once again to that diagram of the [Clean Architecture](https://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html), and then to the sections from _The Dependency Rule_ on down in that post. All your dependencies (database connections, UI mechanics, etc) are in the outer (blue) ring of the diagram, and pass control inwards. All code in the "business rules" rings receive and emit data; they don't know or care where it comes from or where it's going beyond the boundary layer they pull it from or send it to.

*Those* layers can be usefully TDDed (or BDDed, if you prefer) by unit tests honouring Dave Cheney's exclusions: they don't talk to the database or network, they don't touch the file system, and are independent of any other *unit tests* running at the same time. The green _Interface Adapters_ ring is exercised by your integration tests, and the blue _Frameworks and Drivers_ ring by your system tests, since at that point you're verifying that your understanding of how to make the database/Web framework/physical hardware interacts with the *already-proven* inner rings is accurate and sufficient for your need.

And, in real-world applications, these inner layers contain the real value that your application delivers to users. It isn't a great app because it uses the trendiest new Web framework and both Postgres and the NoSQL database *du moment*; it's a great app because it *solves user problems in an effective, understandable, reasonable* way. We, as a craft community, tend to completely, gleefully lose sight of, to the point where we curse anyone who tries to drag us back to that reality.

That used to be understandable, if not very defensible; twenty or thirty years ago, there really wasn't the understanding of how to cleanly separate an app into layers that were easy to reason about on their own, nor was there the language or hardware support for that layering that is readily available today. I've seen well-architected programs written for everything from the Raspberry Pi to your preferred desktop OS/environment to containerised apps to big-iron, high-volume mainframe apps. It *can* be done and, after 40 years in this craft going from one extreme to others, it's seemingly self-evident that "doing it" makes life (particularly during maintenance, after the superstar rock-star/ninja/quasigod consultants have cashed their cheques and left), *much* more survivable to those who want to have a life that isn't spent 24/7/365 in front of the screen, debugging in frustration.

There are no magic bullets. But we've seen, over and over again, that there are numerous supplies of more than sufficient rope to shoot yourself in the foot. Don't let architecture-that-isn't be one of those.

Thoughts?