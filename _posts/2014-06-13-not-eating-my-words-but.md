---
layout: post
title: "Not eating my words, but..."
updated: 2015-02-20 01:58:00 SGT
tags: [tumblr, architecture, software architecture, oop, object oriented programming, design, solid]
categories: [not yet categorised]
comments: true
fullview: false
---

&hellip;thinking some people might wonder if I’m nibbling on the punctuation.

The comment above an internal class-within-a-class (in Ruby) I’m just finishing off reads:

```ruby
    # Information about a blog entry, currently only body and title. This is
    # a *subset* of what's stored in the database (e.g., no timestamps), and
    # is meant to quack properly to code that wants to *think* of a thing as a
    # blog post, but has no business whatever touching the actual machinery.
    # This is arguably a permissible violation of the Law of Leaky
    # Abstractions, invoking the Interface Segregation Principle.
```

[SOLID](https://en.wikipedia.org/wiki/SOLID) is a _basic_ tool of minimal developer proficiency. So, in my view, is Spolsky’s [Law of Leaky Abstractions](http://www.joelonsoftware.com/articles/LeakyAbstractions.html) &mdash; _except_ where it contravenes SOLID (usually the ISP).

Dissenters?
