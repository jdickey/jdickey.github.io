---           
layout: post
title: Privacy isn't always easy. Especially in real life, or in Ruby.
updated: 2015-02-20 02:09:00 SGT
tags: [tumblr, privacy, ruby, design, architecture, software architecture, abstraction, communication]
categories: [not yet categorised]
comments: true
fullview: false
---

If you have something like

```ruby
  class Foo
    # …
    private
    # …
    class Bar
      # …
      def voice
        'quack'
      end
    end # "private" class Foo::Bar
  end # ordinary class Foo
```

you _don’t_ have a private class. Any code that can see `Foo` can do something like

```
[1] pry(main)> require 'foo'
=> true
[2] pry(main)> Foo::Bar.new.voice
=> "quack"
```

You haven’t really hidden anything at all. All that you’ve done, by nesting `Bar` within `Foo` and sticking that `private` declaration above it, is telling the next poor soul to come read your code that `Bar` is _intended as_ an _internal implementation detail_ of `Foo` and should not be used directly.

You, by going to that trouble, have given yourself the responsibility to ensure that knowledge of `Foo::Bar` _never_ leaks out of `Foo` (e.g., by way of a method return value). I don’t always (often?) agree with Joel Spolsky, but [_The Law of Leaky Abstractions_](http://www.joelonsoftware.com/articles/LeakyAbstractions.html), nearly twelve years on, is a _classic_.
