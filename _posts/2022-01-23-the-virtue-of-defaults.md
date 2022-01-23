---
layout: post
title: The Virtue of Defaults
date: 2022-01-21 12:55 +0800
description: "I used to want to control every tiny detail. I'm better now."
tags: [rails, ruby, defaults]
categories: [development, practices]
comments: true
fullview: false
---

I started writing this as a response to one of [Jason Knight](https://link.medium.com/zTvV1qeOYmb)'s posts on Medium, titled "[Let’s Document The “Non-Tailwind Starter Kit Rewrite” 2 of 2: CSS](https://medium.com/codex/lets-document-the-non-tailwind-starter-kit-rewrite-2-of-2-css-5d600e9ede24)". I'd been eagerly following his posts for some time; read his post and this one, in whatever order suits you, to understand why I no longer do.

----

I’ve been writing code for well over 40 years now. For most of that time, my career philosophy was

1. Learn as much as I could about
2. As much as I could, so that I could
3. Be ready to go when the market for a skill exploded, while working to
4. Maintain top-level skill credibility in two or at most three apparent longer-term stable areas as a fallback for when I guessed wrong.

As you might expect, I wasn’t a fan of what’s now called “conceptual compression”; as an early colleague described my fetish for the most minute detail,

> It's not that you don’t always see the forest for the trees; you spend an amazing amount of effort polishing the knots on the roots.

The advent of modern search engines and developer knowledge bases cemented that even further so that, by the time I found Mr Knight's writing on Medium a year or two ago, I was an instant disciple.

Riding my most recent startup all the way down to its (in hindsight, blindingly obvious-well-in-advance) controlled hypersonic flight into terrain after nearly a decade of insane work schedules was the final reminder needed that change was a prerequisite for physical and mental survival. (*Note to self:* when approached by an obviously inexperienced, apparently wantrapreneurial CEO, _run far, far away!_) Finding [this RailsConf keynote](https://youtu.be/zKyv-IGvgGE) rubbed my nose in how I’d been Doing It Very Wrong for at least the last decade and a half.

----

## Drill Down, Not Up

Tools should above all be _useful_. That usefulness is in direct proportion to the amount of detailed, numbing, repetitive scut work they free you from, so that you can focus on what makes your current project valuable, instead of the low-level details that make it like thousands of other projects.

Frameworks that achieve wide-scale use solve the [80-20 problem](https://en.wikipedia.org/wiki/Pareto_principle) for their users, if not going well beyond that. They don’t guarantee that you’ll _never_ interact directly with “[the man behind the curtain](https://youtu.be/YWyCCJ6B2WE)”, but they work at making such interaction a specialist endeavour embarked upon only rarely, if at all.

I used to denigrate frameworks that tried to be everything an app or developer would need. “Sure, that’ll work just fine, until you try to draw outside the lines.” It took me a further decade to get through my nearly solid-[duranium](https://memory-alpha.fandom.com/wiki/Duranium) head that essentially all developers stayed well within the “lines” drawn by a reasonably mature framework essentially all the time. Special cases will regularly come up; good frameworks understand and accommodate that. They also understand and accommodate you experimenting with replacing tools with other similarly functioning tools. Again, defaults exist but, in the end, you own your project.

Like an ex-smoker being less tolerant than others of second-hand smoke, I've now become far less tolerant of those who celebrate not seeing the value of conceptual compression, making juvenile potshots at things that don't fit into or, worse, competently refute the need for the gate they keep with fanatic zeal. I know; I used to be one of those folks, but I'm working hard to make a little progress away from that every day.

That sounds like something that a 12-step programme might be able to help with. Deprogramming programmers. What a craft community. What a world.