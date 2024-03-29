---
layout: post
title: 'piscine-unrelated: I wonder how hard it would be...'
updated: 2015-02-16 00:06:00 SGT
tags: [tumblr, c++, python, ruby, object oriented programming]
categories: [not yet categorised]
comments: true
fullview: false
---

[**piscine-unrelated:**](http://piscine-unrelated.tumblr.com/post/100395787102/i-wonder-how-hard-it-would-be-to-make-the-jump)

> I wonder how hard it would be to make the jump from C++ to Python and/or Ruby on Rails.

You'll get the basics quickly enough, but extensive C++ experience actively inhibits your ability to think in terms of dynamic OO as used in Ruby or even Python. The way you *should* think about "good" design is just that different. No matter what subset of C++ you're used to (*nobody* uses the entirety; perhaps a few dozen people in the history of the language have fully understood the entirety), you're used to playing by some fairly strictly-enforced, static rules about construction that simply don't apply in the New Order of Things.

Obvious example: classes and interfaces. In C++, you define interfaces through classes with virtual (usually pure virtual) methods that you then implement in (often multiply-inherited) subclasses. Ruby, done properly, is *different*; there's no equivalent, really, to C++'s pure virtual methods (thank `$DEITY`); "duck typing" a) makes the "contract" between components much more casually fungible and b) encourages smaller, more atomic classes than you'd typically write in C++. It sounds simple and straightforward, and it *is* and you *think* you know all about it &mdash; until you're a few years in and the light bulb finally flips on.

(source: 15+ years coding C++ on a daily basis, and coming up on 5 total years of equivalent Ruby and 2 years of Python experience.)

YMMV. TIKOAR. IANALNDIPOOTV. LSMFT. And, for you Singaporeans, as always, terms and conditions apply<sup>*</sup>.
