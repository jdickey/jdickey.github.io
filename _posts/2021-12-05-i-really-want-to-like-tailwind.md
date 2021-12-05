---
layout: post
title: "I REALLY want to like TailwindCSS"
updated: 2021-12-05 20:20:00 SGT
description: "Tailwind blows my mind and lets me see CSS in whole new ways. But..."
tags: [tailwind, tailwindcss, css, workflow, disruption, possibilities]
comments: true
categories: [development, practices]
fullview: false
---

[Tailwind](https://tailwindcss.com/) is a [utility-first](https://xenox.dev/tailwind-css-utility-first-css-framework/) CSS framework that's grabbing developer mind-share, and use in production, extremely quickly. A quick search-engine session will find many testimonials for the framework. Tailwind by default eschews external custom CSS/SCSS files in favour of specifying styles using the `class` attribute of whatever element you're working with.

CSS frameworks such as Bootstrap, Semantic UI, and most others, give you a collection of "components" such as Card, Navbar, etc. This makes it really fast and easy to whip together a Web page or site -- *assuming* you don't need to customise anything beyond what is provided for in the framework's components. One problem common to pages using such frameworks is that it's relatively easy for a developer to look at them and (usually) correctly guess which framework was used, whereas to the user, they "all look the same". If your client is looking for a site that "doesn't look like all the others", this can be A Problem.

Another problem for the developer (and especially maintainer) is ["divitis"](https://csscreator.com/divitis); the tendency for components and sub-components to be coded using a seemingly endless series of `div` elements differentiated only by their classes -- which often requires flipping back and forth between the code and the framework documentation. Tailwind isn't *entirely* free from divitis, but its classes are sufficiently granular and mnemonic that the developer quickly commits the most commonly-used ones in her project to memory, or makes use of the excellent [Tailwind Cheat Sheet](https://nerdcave.com/tailwind-cheat-sheet).

Once you have things looking the way you (and/or your client) want, it's then very straightforward to extract commonly-reused sets of styles (e.g., for button styles) into a separate CSS file with more mnemonic class names. This makes the HTML less "cluttered", while still preserving the granularity and ease of modification of the styles used.

So what's not to love?

If you're using ERB (and possibly HAML) for your HTML templates (with or without partials), not much. Whichever mainstream editor you're using likely has extensions/packages available to suggest/auto-complete Tailwind style names as you work. Easy-peasy, and also quite useful in that you can see what your options are.

If you're using [Slim](http://slim-lang.com), however, you lose that suggest/auto-complete goodness (**please** show me how I can be wrong on this). The hack I have resorted to using is along the lines of:

1. I want to add an `h1` element with several Tailwind classes in my Slim template;
2. I code a raw HTML `<h1 class="...">` with the Tailwind classes I want inside the `class` attribute. Auto-correct and auto-suggest work as expected if I have the basic Tailwind extensions loaded in my editor (VS Code);
3. I translate the complete `h1` tag markup into Slim, using periods to separate class names. (Modern Slim is fine with hyphens in class names);
4. Remove the raw HTML.

For example:

Step 1: intent only; no code yet;
Step 2: code `<h1 class="font-bold px-4 py-4 text-2xl">Title text</h1>`;
Step 3: code `h1.font-bold.px-4.py-4 text-2xl Title text`;
Step 4: delete the code from Step 2.

(Why specify both `px-4` and `py-4`, you ask? Because I might want to experiment with different padding settings on the *x-* and *y-*axes as I'm coding this up. I can always refactor later.)

This. Is. A. Pain.

If anyone can point me towards a better workflow, probably with VS Code extensions in support, I'd be greatly appreciative. After 6-7 years and 50+ Ruby (with or without Rails) projects, I'm extremely loath to leave Slim, *especially* for ERB.

Comments here or on [Twitter](https://twitter.com/jeff_dickey) more than welcome.

## And One More Thing...

I've been AWOL for almost exactly a year, and I've *missed* blogging. I'd chalk it up to A Series of Unfortunate Events, mostly family-related and/or health-related. In the absence of further such Events, I can see where blogging at least once a week will make me feel much better about myself and several issues.

Thanks for your patience and well-wishes.