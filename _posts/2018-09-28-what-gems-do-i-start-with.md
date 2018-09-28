---
layout: post
title: "What Gems Do I Start With?"
description: "Helping answer the question, 'What will keep me sane as a developer?'"
tags: [tools, gems, ruby, rails, hanami, best practices]
categories: [development, practices]
comments: true
fullview: false
---

Well, hello again! Excuse the dust&hellip;

This post is in response to a [question on Reddit `r/rails`](https://www.reddit.com/r/rails/comments/9isbvh/should_i_use_trailblazer/), and specifically a [follow-up question](https://www.reddit.com/r/rails/comments/9isbvh/should_i_use_trailblazer/e6nmfbz/) from my response to his initial question. He later asked me to "list the gems [I] use on every project that [make] the code more readable and will let the developer keep his sanity".

I started out by thinking that I'd just give him a copy of a `Gemfile` I'm using for a project in its very early stages, where the vast majority of the 30+ Gems listed as direct dependencies are from boilerplate I carry around to every project, with all but one of the rest being the kind of boilerplate he was asking for. Then I thought that hey, [RubyGems.org](https://rubygems.org) is a great resource for identifying what individual Gems are and what they depend on, but dumping a list of more than 25 Gems on someone without at least briefly explaining what each is and why it's in my boilerplate probably wouldn't be a very effective way to convey useful information. As what I started typing grew longer than I'd want to see in a Reddit post response, I decided I'd better write it as a blog post. So here we are.

To recap, what I enumerate below are Gems I use on practically every Ruby project I've started in the last couple of years, whether that project used Rails, [Hanami](https://hanamirb.org/), or [Roda](https://roda.jeremyevans.net/). They've each been useful for me and my team, and I expect to be using them individually and collectively until and unless I find something that does the job more to my liking. (Yes, this is largely a subjective list; others may not think you need all this, but give them a try before you dismiss them.)

This turned into a lot more than you asked for; more than I expected to write; and *far* more than anyone would bother reading in a Reddit comment. Comments, nonetheless, are welcome. Thanks for reading.

## Testing

### Testing Features

It's *essential* to have feature specs, which automate "doing things the way a user would do them and verifying the resulting actions/side effects", especially in a BDD shop. (Doing inside-out TDD instead? How do you prove you've finished something correctly if you don't have feature specs?) It's important to remember that it's normal, if a bit disheartening, to have your feature specs fail *every single time* as you're in the trenches of building out a new feature; the thing that tells you you're making progress is that your feature spec makes it *farther* without falling over.

The de facto feature-spec package for Ruby for *many* years now has been [Capybara](https://github.com/teamcapybara/capybara). To use it with modern applications (i.e., apps which incorporate JavaScript client-side functionality), you need a *driver* more capable than the default `rack-test`; that's where [`capybara-webkit`](https://github.com/thoughtbot/capybara-webkit) comes in. An alternate, "industrial-strength" driver would be the [`selenium` Gem](https://github.com/shvets/selenium), which provides a Ruby wrapper for the [Selenium server](https://www.seleniumhq.org/projects/webdriver/), which is a Java application.

Docker is your friend.

### Testing Code

For the last 3-4 years, I have preferred to use Minitest, particularly Minitest::Spec rather than RSpec. In my experience, it's faster, more explicit, and therefore easier to understand what it's doing and why. I also prefer its philosophy of "add separate (Gem-based) enhancements to a minimal core" to what I see as RSpec's "override the built-in defaults as you like". That view seems to be a bit less widely-held of late, particularly in the Rails community.

The Minitest enhancement Gems I use are

1. [`minitest-hooks`](https://github.com/jeremyevans/minitest-hooks), which adds `before(:all)`, `after(:all)`, `around`, and `around(:all)` to Minitest. In any non-toy project, you *will* find these handy.
2. [`minitest-matchers`](https://github.com/wojtekmach/minitest-matchers) adds support for [Shoulda](https://github.com/thoughtbot/shoulda-matchers)/RSpec-style matchers to Minitest::Unit and Minitest::Spec. This style of matcher has been described as "crack cocaine for RSpec, analogous to ActiveSupport for Rails". This gives you the literate-test-style "high" without the same level of attendant side-effects.
3. [`minitest-reporters`](https://github.com/kern/minitest-reporters) lets you create [custom Minitest output formats](https://github.com/search?q=%22Minitest%3A%3AReporters%3A%3A%22), or use one or more of the eight that are packaged with the Gem. (Yes, you can run multiple reporters at once. Want a red/green-displaying report *and* a report that integrates with RubyMine? Got you covered.)
4. [`minitest-tagz`](https://github.com/jbodah/minitest-tagz) gives you a nice "tagging" capability, perhaps most commonly used to tag examples and groups as `:focus`. With a properly-set-up `spec_helper.rb` or `test_helper.rb`, this will allow you to run `ruby spec/path/to/your_spec.rb` or `rake spec` (or their Minitest::Unit equivalents) and have *just* the tagged examples run. Indispensable. *Be advised that* this Gem has a `z` at the end; there *is* a `minitest-tags` Gem (with an `s`) that hasn't been updated since Version 0.0.5 in April, 2012. `minitest-tagz`, on the other hand, is actively maintained; Version 1.6.0 was released on 9 May.

### Other Useful Gems

Does your app use a database? Then you almost certainly should be using [`database_cleaner`](https://github.com/DatabaseCleaner/database_cleaner), which does just what it says on the tin: helps you ensure that your integration tests (and any test that touches the database while doing something else *is* an integration test, remember) clean up after themselves.

Feeling like if you see another _John Doe_ username in tests, you'll break something expensive? You need [`ffaker`](https://github.com/ffaker/ffaker), ffriend; this will generate whatever [type of data value](https://github.com/ffaker/ffaker/tree/master/lib/ffaker) you need. Korean _Lorem Ipsum_? People's names, using either default Western conventions or any of a couple of dozen localisations? Got you covered.

That comes in handy when you get tired of developing and maintaining fixtures and seed data to populate your models during tests. Use [`fabrication](https://www.fabricationgem.org/) (or its better-marketed if less feature-rich analogue, [`factory_bot`](https://github.com/thoughtbot/factory_bot)) will help. Define the attributes of a model, and you can create an instance based on your template (with or without saving the instance to your database), or simply get a `Hash` with the attribute values.  For example, here's a simple `fabrication` User Fabricator from one of my projects:

```ruby
# frozen_string_literal: true

require 'securerandom'
require 'timerizer'

Fabricator(:user) do
  name { FFaker::Name.html_safe_name }
  email { |attrs| FFaker::Internet.safe_email attrs[:name] }
  profile { FFaker::HTMLIpsum.p(1, fancy: true) }
  hashed_pass { SecureRandom.base64(48) }
  created_at do
    seconds_per_month = 24 * 3600 * 30
    # 30..60 days before now
    rand_offset = SecureRandom.random_number(seconds_per_month)
    (seconds_per_month + rand_offset).seconds.ago
  end
  updated_at do |attrs|
    # at least one hour after user creation
    limit = Time.now.to_i - attrs[:created_at].to_i - 3600
    SecureRandom.random_number(limit).seconds.ago
  end
end

```

See how that works? You can even define field values relative to other field values, as with the `updated_at` value being dependent on `created_at`, or a "safe" email address being generated based on the `name` value. Instantiation can be as simple as `author = Fabricate(:user)`. Need thirty User entities all with the same `created_at` timestamp but don't want them to hit the database right away? `students = Fabricate.build(:user, created_at: 3.days.ago)` does just what you think it will. Support exists for [Rails](https://www.fabricationgem.org/#rails), [Hanami](https://github.com/jodosha/hanami-fabrication), and a [documented procedure](https://www.fabricationgem.org/#configuration) to support any other ORM you may be using.

## Debugging

I *can't stand* the `irb` console; I much prefer [Pry](https://pryrepl.org/), in the [`pry-byebug`](https://github.com/deivid-rodriguez/pry-byebug) enhancement. `pry-byebug` is only one of the numerous [plugins](https://github.com/pry/pry/wiki/Available-plugins) available for Pry; the only other one I use consistently is [`pry-doc`](https://github.com/pry/pry-doc), which adds MRI Core documentation and source code to the Pry REPL. There are few learning aids or reminders more effective than `show-source` and `show-doc`.

Sometimes you just want good old-fashioned "`printf(3)` debugging", so you can see where things are Going Horribly Wrong in your code/data without actually forcing a debugger breakpoint. For those situations, [`awesome_print`](https://github.com/awesome-print/awesome_print) was my go-to tool for *years* before I realised that a proper logger like [Hanami::Logger](https://www.rubydoc.info/gems/hanami-utils/Hanami/Logger) or [Semantic Logger](https://rocketjob.github.io/semantic_logger/) is *far* more flexible and useful.

## Static Analysis

These are tools I generally use via Rake tasks, or which integrate into tools typically run from Rake, like Minitest. This is *probably* the most direct answer to the question of what Gems make "the code more readable and will let the developer keep his sanity", but do read on.

Writing code without static-analysis tools, to me, is very much like driving on the expressway on a dark and foggy night without headlights: if I'm very careful, and *very* lucky, I can *probably* get where I'm going without hitting anything of consequence, but all it takes is one unseen obstacle (say, a few bags of cement accidentally dropped from a truck) to ruin my whole night. With a good pair of fog headlights, I'm much more confident of where I'm going.

The tools I use on *every* project are

* [`flog`](https://github.com/seattlerb/flog) is a complexity metric. As your project grows, its Flog report will show you the classes and methods which are the most complex, with a view towards prodding you to refactor any excessively-highly-Flogged method. Our shop rules define a method score above 7.5 as a [*code smell*](https://blog.codinghorror.com/code-smells/); not even presumed to be buggy, but more likely to warrant further attention. A score above 12.0 (soon to become 10.0) will block a feature branch from being merged back into `develop`. You'll want to adopt rules that make sense for your shop, and adjust them as you gain experience and as individual projects mature.
* [`reek`](https://github.com/troessner/reek) is a general, wide-ranging code-smell detector. With some four dozen or so [smells](https://github.com/troessner/reek#code-smells) defined, and with an easy-to-use, flexible [configuration](https://github.com/troessner/reek#configuration) scheme, you can use Reek in its default configuration or make it as permissive or strict as your shop standards allow.
* [`rubocop`](https://github.com/rubocop-hq/rubocop/) is a static code analyser and (perhaps primarily) format enforcer, supporting the guidelines from the community [Ruby Style Guide](https://github.com/rubocop-hq/ruby-style-guide) (which is a great resource for new/newish Ruby devs).
* [`simplecov`](https://github.com/colszowka/simplecov) is a simple ([C0 coverage](https://grosser.it/2008/04/04/whats-my-coverage-c0-c1-c2-c3-path-coverage/) only) test-coverage checker for Ruby. If tests never exercise your code, especially in a proper BDD or TDD process, then the untested code is *anomalous*; possibly a bug, but definitely worthy of examining why it's there. You may not always (especially in a legacy project) always have 100% coverage as reported by your tool, but you *should* have a shop standard for deciding whether your test coverage (at a method, class/module, and project level) is adequate for your purposes.
* Finally, [`flay`](https://github.com/seattlerb/flay) analyses code for structural similarities. DRY is a Good Thing (once you've established you're not using [the wrong abstraction](https://www.sandimetz.com/blog/2016/1/20/the-wrong-abstraction)), and Flay points out code that "too-shamelessly" duplicates other code in your project. *Any* non-zero score here is treated as cause for immediate concern.

My current project's `Rakefile` has a default task which looks like

```
spec default: [:spec, :flog, :flay, :reek, :rubocop]
```

## But Wait; There's More!

Do you miss the nice date/time arithmetic from ActiveSupport in your non-Rails project? Look no further than [`timerizer`](https://github.com/kylewlacy/timerizer) which delivers on the promise to be a "simple Ruby time helper, just like the Rails ones. You know, without the Rails."

Are your config files unwieldy to deal with, especially with sensitive data; or do you need to be able to support different environment variable values in different environments (development, test, etc), perhaps because you're developing a proper [12 Factor](https://12factor.net/) app? You'll find [Chamber](https://github.com/thekompanee/chamber) *very* useful. Protip: install the Git pre-commit hook that won't let you commit unencrypted secret data. Your auditors can thank me later.

Do you work with or for people who don't have simple email addresses that can be validated by any of the simple regular-expression matchers out there, [buggy](https://www.natashatherobot.com/ruby-email-validation-regex/) or not? Then you'll find [`email_address`](https://github.com/afair/email_address) probably handles anything you can throw at it, especially when you enable it looking for appropriate DNS `A` records to match the domain against.

Finally, if you're not already familiar with it, you should have [Awesome Ruby](https://awesome-ruby.com/) near the top of your bookmarks list. A categorised, community-driven, curated list of Gems for most things you'd want to pull off the shelf, and a few that you might not have thought that you could. Part of the awesome [Awesome](https://github.com/sindresorhus/awesome) list of awesome lists, which includes lists on what have to be *hundreds* of different topics, from your favourite programming technology to [science fiction](https://github.com/sindresorhus/awesome-scifi#readme) to [places to post your startup](https://github.com/mmccaff/PlacesToPostYourStartup#readme). (If startups aren't science fiction, what are they? Well, most of them turn out to be fantasy instead, yes?)
