---           
layout: post
title: Imitation Is NOT The Highest Form of Usability.
updated: 2015-02-19 23:40:00 SGT
tags: [tumblr, html, haml, slim, markup, css, styling, pitfalls, errors]
categories: [not yet categorised]
comments: true
fullview: false
---

I *really* appreciate [Slim](http://slim-lang.com) view templates. What I *don't* appreciate is [Haml](http://haml.info)-style shorthand for things like CSS class names.

Haml (and Slim) allow you to code

```html
<span class="foo bar">blah blah</span>
```

as

```haml
span.foo.bar
  blah blah
```

That works just fine, until you have class names (as in [Bootstrap](http://getbootstrap.com) or [UIKit](http://www.getuikit.com) with dashes (hyphens) in the class names.

If you want to code

```html
<div class="col-md-8">
  <!== markup... -->
</div>
```

the naive Haml-influenced stylist might write

```haml
.col-md-8
  / ...
/ ...more markup...
```

which won't work. Ruby comes along, interpreting the Haml/Slim DSL, and looks
for variables named `col` and `md` to build the expression `col - md - 8`, and
barfs when it can't.

The fix is to write the HTML without the angle brackets:

```haml
div class="col-md-8"
  / ...
/ ...more markup...
```

You Have Been Warned.
