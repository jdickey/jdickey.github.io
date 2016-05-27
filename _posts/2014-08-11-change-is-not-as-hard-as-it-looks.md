---           
layout: post
title: Change is NOT as hard as it looks.
updated: 2015-02-16 23:29:00 SGT
tags: [tumblr, migration, blogger]
categories: [not yet categorised]
comments: true
fullview: false
---

(But it tries, at least on first appearances.)

As you may know, I'm working on consolidating my various blogging-ish sites into a single, combined, Github Pages site. This involves moving some 200 posts from my [current blog](https://archlever.blogspot.com/) and my old, nearly-defunct [personal blog](https://jdickey.blogspot.com/) to GH Pages (which is based on [Jekyll](http://jekyllrb.com)).

Fortunately, Blogger/Google give you a proof-against-*most*-idiots [process](https://support.google.com/blogger/answer/97416) to export *everything* about your blog &mdash; posts, comments, templates, and several kitchen sinks, all rolled up into one *ginormous* XML file. (My raw export data was ~5,260 lines; pretty-formatted (as in "legibly formatted"), it's 16,239 lines long. Well, I *did* start blogging in 2003.

I am *not* hand-splitting that up into individual <code>_posts</code> files.. That's what software is for. Right?

**Step 1.** Google [*convert blogger to github pages*](https://www.google.com/search?q=convert+blogger+to+github+pages). That led me to [this Gist](https://gist.github.com/kennym/1115810) which will pull your posts' RSS feed and spit out appropriately-formatted Jekyll/Markdown files, suitable for publishing in GH Pages.

For your 25 most recent posts. [*Archimedes' Lever*](https://archlever.blogspot.com/) has roughly seven times that. Oops.

**Step 2:** More Google-fu. Learn that we can solve the '25 posts' problem by adding `?max-posts=500` to the feed URL. Run the script and see all the pretty files, *almost* ready to publish on Github Pages.

The operative word there is "*almost*". There are two problems immediately apparent:

1. The files contain the original HTML markup; no Markdown conversion has been applied. This should have been expected, and really shouldn't be a problem, but still. More important is
1. Those tags-turned-categories are not separated in any way; the original tags *"best practices", "competence", "development", "ruby", "ruby on rails", "startup", "teams"* get written to the `categories` field as "best practices competence development ruby ruby on rails startup teams". *Definitely* not what we want.


But we're closer. Now all I need is a program to *either* read a list of all tags from a different file, and then search each post's `categories` string for each match, longest match first; *or* read the Blogger/Google export file to build a list of categories for each post (since they're properly quoted there), figure out which file in `_posts` matches each post in the export file, and blast in the `categories` as a proper YAML array.

We're a *lot* closer. Stay tuned.
