---
layout: post
title: "Swarms of Simplicity"
description: "Simplifying one thing *always* introduces a different kind of complexity."
tags: [abstraction,architecture,improvement,solid, ruby, rails, rant]
categories: [not yet categorised]
comments: true
fullview: false
---

Which would you rather work on:

1. A class that has ~10 public methods that, on each call, delegate to one of ~10 private methods based on the parameter values, with those methods performing a (possibly overlapping) series of steps that are each implemented in *other* private methods; *or*
1. A class with those same ~10 public methods, which each instantiates a [command pattern](http://www.c2.com/cgi/wiki?CommandPattern) object and calls its action method. Each of those command-pattern classes' action methods determines, based on its parameters, which of ~10 *other* command-pattern objects to instantiate and call. Those latest objects include various modules with methods that can be called from their action method to do the steps required.

One design has one class with (probably) hundreds of methods to cover all the different possibilities. The other has a hub-and-spokes pattern where the hubs are dispatchers that "know" how to select which spoke to use, but don't attach any meaning to that spoke, and each spoke is a simplest-possible single-path-of-execution class with perhaps a dozen actual lines of code each.

If you're a Rails fan, or the thought of managing numerous (mostly internal) artefacts scares you, you'll choose the first, and count on long, complex, highly-coupled test code to verify your long, complex, highly-coupled application code.

If you value your time, velocity, and sanity, you'll choose the second. Each class at each level can be tested independently with almost trivially-simple tests. You might have hundreds of files with app code or test code in them, but you'll be able to organise them sensibly and reason about each of them and how they all work together.

Too much of our time and energy is spent trying to make that first "obviously simpler" system work. With no practical means of isolating and understanding side-effects, finding and fixing bugs or adding new features quickly devolves into a game of Whac-A-Mole, where the moles are high on meth and we're on heavy tranquilisers. That doesn't work too well, and the resulting uncertainty is a major reason why many non-technical project stakeholders have a hard time understanding that the technical people are working as hard as we are. We bought into a way of doing things that promises (and delivers) quick up-front results, and then places us atop a kilometres-high cliff, with our goal tantalisingly out of reach across a gorge, when we try to go further than the "15-minute working blog example".

There's an old joke where a patient tells his doctor that "every time I drink a cup of coffee, I have a sharp pain in my eye". The doctor replies, "next time, take the spoon out before you try to drink the coffee. That'll be $75, please". As an industry, we need to get back to sound technical judgement making more decisions than marketing hype is allowed to; we, our projects, companies, and customers will all be the better for it.
