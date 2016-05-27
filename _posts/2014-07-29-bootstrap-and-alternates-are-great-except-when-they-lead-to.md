---           
layout: post
title: "Bootstrap and alternates are GREAT! Except when they lead to..."
updated: 2015-02-07 09:07:00 UTC
tags: [archlever, Web development, Ruby on Rails, deprecation, Bootstrap, blogging, markup, rails, blog, Ruby, HTML]
categories: [not yet categorised]
comments: true
fullview: false
---

...[divitis](https://en.wiktionary.org/wiki/Citations:divitis) like this (in a [Slim](http://slim-lang.com) template):

<script src="https://gist.github.com/jdickey/2f663776849e16bf2bc8.js"></script>

Yes, that's *eleven levels of indentation* for the link within the `.list-group-item` list item. Can *you* glance at that and grok it in reasonable fullness without spending ten minutes rebuilding the DOM in your head? I can't, and I *wrote* the sucker yesterday.

What's the problem? Nesting styles is the problem. Using [line 23](https://gist.github.com/jdickey/2f663776849e16bf2bc8#file-show-slim-L23) as an example, we have, in reverse order:

* an anchor link
  * within a list-item tag
    * within a loop (that arguably doesn't count, but does kick the indentation in a level);
      * within an unordered list
        * within a `.row` `div`;
          * within a `.col-md-10` `div`;
            * within *another* `.row` `div`;
              * within a `.col-md-12` `div`;
                * within an outermost, containing `.row` `div`.

Got all that? At least we're using something like Slim and not, `$DEITY` forfend I'd need to use something that looked more like HTML soup, like ERB.

Why *bother?* Because, as far as I can tell, that's what it takes to work with nested columns in Bootstrap. Everything's within an outermost row, in which you define columns; within any given column, you can use another row to break the *containing* column into sub-columns. Remember when, in ancientest days of the Web (circa 2001 at latest), we started telling people not to use tables for layout because it led to inscrutably crufty hairballs? They're *baaaaaack!*

There's *got* to be a better way. Isn't there? *Isn't there?!?!?*


## In other news...

By this time next month, after [over more than ten years](http://jdickey.blogspot.sg/2003/12/hello.html) of blogging on Blogger.com, I expect to have the process of moving my blogs (and my [Tumblr](http://yeoldeprogrammer.tumblr.com), and many of my [G+ posts](https://plus.google.com/108905645314102533472/posts) well underway, if not yet completed. (**NOTE 7 Feb 2015:** Delayed due to overwork. A 50 hour/week job turned into a 90 hour/week job, and then a 110+ hour/week job. Lessons learned; several of which to be blogged about In The Glorious Future&trade;.) Why? I'd like to

1. Reduce my Google footprint or, more precisely, the footprint GOOG leaves on *me*;
1. Be able to point people to *one* place where they can see all my non-code online writing;
1. Use a markup language that's less actively hostile to writing than HTML is; with Markdown and [Jekyll](http://jekyllrb.com) on [Github Pages](https://pages.github.com), I get that;
1. Have more control over that "one place" than any of the Google-based "places" presently give me; and
1. Be able to embed Gists or other bits of code in my writing and have them properly formatted.

It's not for everybody. But if you've been using Blogger in HTML-editing (rather than WYSIWYG) mode, as I have since the beginning, you can certainly learn (and benefit from and *enjoy*) Markdown and Jekyll.
