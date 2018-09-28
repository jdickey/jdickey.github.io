---
layout: post
title: "Enthusiastically Poking at Hanami"
description: "Finally! A Ruby Web framework people can use with a straight face!"
tags: [tools, framework, web development, object oriented programming, oop, domain-driven design, clean architecture, hanami]
categories: [development, practices]
comments: true
fullview: false
---

This is indirectly a long-delayed follow-up to [this post](https://jdickey.github.io/not%20yet%20categorised/2014/06/24/lotus-a-complete-web-framework-for-ruby.html) from *June, 2014*. It took *amazing* a while to get here, but it *did.*

If you're an experienced OO developer, who also does Web development in Ruby and is in a use relationship with Rails, at some point you've beaten your head against the wall (often literally) in enraged frustration at how much effort it takes to make seemingly simple changes, and how few aids the framework really has for good architecture and design. Many megabytes have been published by more capable and patient bloggers than I regarding the walls one must power through in order to get work done with Rails. Nick Sutterer put it almost perfectly in his [*Trailblazer*](https://leanpub.com/trailblazer) book:

> For every Rails project, there is exactly two outcomes. Either someone in the team’s an experienced architect and leads the software to an advanced design with service layer, view components, maybe forms, and so on. Or, and that’s the classic way, the project strictly follows the Rails Way and will end up as a code disaster.

Or, to put it another way, your team will spend an allegorical *forty years in the wilderness* turning a Rails-Way megalith with no underlying structure into a compelling product that real people will pay you real money for, enabling you to hire enough developers to fix enough bugs that you can add a feature or two every quarter and not run yourselves into the ground (or out of money, or both). Someone wins the lottery almost every week, too.

For the last three years, I've been keeping a hopeful eye on [*Hanami*](http://hanamirb.org/), n&eacute;e *Lotus*, which appeared to tick all my oh-how-I-wish boxes for a Ruby Web framework:

* clean, understandable architecture;
* fluent in and encouraging of good design principles; at a minimum including [SOLID](https://en.wikipedia.org/wiki/SOLID_(object-oriented_design)));
* an active, open, welcoming development group; and hope of hopes
* documentation good enough that a new developer doesn't have to spend weeks parsing source files to understand basic concepts.

Hanami ticks each of those boxes with enthusiastic authority. The team discussion on [Gitter](https://gitter.im/hanami/chat) is informative, popular, and welcoming of new developers. The [documentation](http://hanamirb.org/guides/1.1/) is amazing. And the architecture&hellip;do I really have to rave for fifty paragraphs about how much I like the architecture? (I probably could.)

Give it a try. Work through the introductory project, hang out in the Gitter room, and learn a few (dozen) things that will make your work much more enjoyably productive than it may well have been.

----

# Yes, but&hellip;

The above is an introduction to what follows. The rest of this post documents notes I took as I took the end product of the `bookshelf` tutorial and added the analysis tools I use on most Ruby projects to the default task in the `Rakefile`. This required some adaptation, as documented below.

The project itself can be found as a Git repository [on GitHub]() https://github.com/jdickey/bookshelf). Git setup and commits *were not* specified by the tutorial itself. That, too, in my opinion, is a bug in the tutorial.

----
----

# (Over?) Analysing the Hanami Tutorial

What happens when we apply popular Ruby static-analysis tools to the final product of the [Hanami 1.1 tutorial](http://hanamirb.org/guides/1.1/)? How "clean" do the tools say our Clean Architecture code is?

Remember that the app at this point is a *tiny* MVC app; one controller has a single action (`Home::Index`), and the other (`Books`) has a total of three (for `index`, `create`, and `new`). There are a total of 424 lines of code in `.rb` files, many of which (e.g., the 326-line `apps/web/application.rb`) are auto-generated. We're not going to draw any broad conclusions about the quality of Hanami code here; we're just looking for the initial reactions that a developer familiar with the tools but new to Hanami might have.

We'll look at several popular Gem-based tools:

* [Flog](https://github.com/seattlerb/flog);
* [Flay](https://github.com/seattlerb/flay);
* [Reek](https://github.com/troessner/reek);
* [RuboCop](https://github.com/bbatsov/rubocop).

# Lessons and Conclusions

Aka the **tl; dr** for people who aren't already certain they want to go through the voluminous content for each of the tools mentioned, but have learned enough about [Hanami](http://hanamirb.org/) to at least be open to learning the framework. (You won't regret it!)

The generated (and lightly updated) code which exists at the end of the [Hanami 1.1 tutorial](http://hanamirb.org/guides/1.1/) tutorial makes sense; is in line with Clean Architecture as espoused by authorities such as Robert C Martin and Arkency, and lends itself to Domain-Driven Design principles. It has several near-duplicate source files (e.g., the view code); during a "real" project, these can be expected to evolve independently, making initial "maximum DRY" code an opportunity for bugs rather than a feature.

The Hanami documentation, both the tutorial itself and the remainder of the documentation, is superb; the only Ruby code that this writer has yet seen with similar depth, breadth, and accessibility to the Hanami 1.1 documentation has been the Rails Guides, and those can often be overwhelming, even to experienced developers merely trying to brush up on a topic.

With regard to the tooling added in the current exercise, we see a few mild to moderate pain points that developers experienced with these tools in other frameworks should be aware of.

1. Most significantly, the as-generated code and specs use Ruby's "compact" module specifications (`module Foo::Bar::Baz`) rather than the preferred explicit nesting (`module Foo; module Bar; module Baz`, preferably on subsequent, iteratively indented lines). The community Ruby Style Guide has [a good explanation](https://github.com/bbatsov/ruby-style-guide#namespace-definition) of the rationale, which links to a [good discussion of constant lookup in Ruby](https://cirw.in/blog/constant-lookup.html) which provides further explanation and justification for the preference. This writer would have preferred that Hanami encourage this usage, e.g., by using it for generated code and specs.
2. The tools provide a baseline of metrics which have been shown generally useful as projects progress from the bare-minimum state exemplified by the current code, and bringing code into compliance with guidelines (such as the Style Guide) and thinking through even "obviously trivial" violations of [SOLID](https://en.wikipedia.org/wiki/SOLID_(object-oriented_design)) principles *early* prevents confusion and more work later;

With that in mind, a project proceeding from a generated Hanami project **should** utilise these tools as part of its standard pipeline (e.g., default Rake task and/or CI process). It **must** also open issues in its issue tracker immediately after these tools are integrated, which note the configuration overrides needed to prevent Reek and/or RuboCop from reporting violations. The practices which necessitated those overrides for generated code **should** be remediated with a moderate to high degree of urgency, and new or modified code **must** report no violations when those tools are used in their default configurations to examine the new or modified files.

This writer must, however, re-emphasise his elation with his initial exposure to Hanami. The architecture includes many major features which his projects have been seeking to attain for several years, with frameworks/toolkits like Rails and even Roda making that discouragingly non-intuitive and clearly non-native to the underlying philosophy. Many [excellent](https://www.poodr.com/) [books](https://leanpub.com/growing-rails), [presentations](https://www.youtube.com/watch?v=WpkDN78P884), and [additional framework layers](http://trailblazer.to/) have been developed over the years to make Rails (in particular) more emenable to production-scale development and maintenance; finding a framework that (largely) takes those lessons to heart *out of the box* is, to put it mildly, a refreshingly vindicating experience.

# The Tools

## Flog

First up, [Flog](https://github.com/seattlerb/flog) says it

> &hellip;reports the most tortured code in an easy to read pain report. The higher the score, the more pain the code is in.

We've used this tool on numerous projects over the last several years. It's easy to use, has a Rake task that does *almost* exactly what we want (and is easily customised), so what's not to like?

We add the line

```ruby
  gem 'flog', '4.6.1'
```

to the `Gemfile` and rebundle. Then, we add to the `Rakefile`

```ruby
require 'flog'
require 'flog_task'

class FlogTask < Rake::TaskLib
  attr_accessor :methods_only
end

FlogTask.new do |t|
  t.verbose = true
  t.threshold = 200 # default is 200
  t.methods_only = true
  t.dirs = %w(apps lib)
end

```

Then we run the tool as a Rake task:

```
$ bin/rake flog
    22.8: flog total
     4.6: flog/method average

     9.8: Web::Controllers::Books::Create#call apps/web/controllers/books/create.rb:15-21
     6.2: Web::Application::configure#production apps/web/application.rb:295-323
$
```

On previous projects (using Rails or Roda as frameworks), we'd adopted the guidelines that any per-method score over 6.0 was a code smell, and over 8.0 was probably in need of refactoring. (Adjust those scores upwards by 1.0 for a Rails project.)

The per-method average of 4.6 is quite low (good), but what are the two methods being called out here about?  Let's take a look at `Web::Controllers::Books::Create`:

```ruby

module Web::Controllers::Books
  class Create
    include Web::Action

    expose :book

    params do
      required(:book).schema do
        required(:title).filled(:str?)
        required(:author).filled(:str?)
      end
    end

    def call(params)
      if params.valid?
        @book = BookRepository.new.create(params[:book])

        redirect_to routes.books_path
      else
        self.status = 422
      end
    end
  end
end
```

The `#call` method is exactly what you see in controller actions in tutorials for *any* Ruby MVC framework: if it's been fed valid parameters, it creates a new entity and redirects; if it hasn't, it complains by setting an HTTP status code (and expects the developer to remember/easily look up that `422` represents an [`Unprocesseable Entity`](https://httpstatuses.com/422)). If you tend to follow the guidance of Ruby leaders like Avdi Grimm (*Confident Ruby*), Sandi Metz (*POODR*), or Russ Olsen (*Eloquent Ruby*), you may be tempted to refactor the `Create` class to look something like

```ruby

module Web::Controllers::Books
  class Create
    include Web::Action

    expose :book

    params do
      required(:book).schema do
        required(:title).filled(:str?)
        required(:author).filled(:str?)
      end
    end

    def call(params)
      return report_unprocessable_entity unless params.valid?

      @book = BookRepository.new.create(params[:book])
      redirect_to routes.books_path
    end

    private

    def report_unprocessable_entity
      self.status = 422
    end
  end
end
```

You've added a [guard clause](http://www.rubydoc.info/github/bbatsov/rubocop/Rubocop/Cop/Style/GuardClause) to replace the alternate-block `if/else`, and of course the specs pass (`bin/rake test` does not report errors or failures). When you run `bin/rake flog`, however, you're a bit crestfallen:

```
$ bin/rake flog
    23.3: flog total
     3.9: flog/method average

     9.2: Web::Controllers::Books::Create#call apps/web/controllers/books/create.rb:15-19
     6.2: Web::Application::configure#production apps/web/application.rb:295-323
$
```

Your 9.8 score has come down, yes; but only to 9.2. Flog thinks there's still a lot of complexity in that method. To get a more detailed view of what it's complaining about, you flog that one file specifically:

```
$ bin/flog -adm apps/web/controllers/books/create.rb
    10.2: flog total
     5.1: flog/method average

     9.2: Web::Controllers::Books::Create#call apps/web/controllers/books/create.rb:15-19
     1.4:   routes
     1.2:   []
     1.2:   books_path
     1.2:   new
     1.1:   report_unprocessable_entity
     1.0:   redirect_to
     1.0:   valid?
     1.0:   assignment
     1.0:   create
     1.0:   branch

     1.0: Web::Controllers::Books::Create#report_unprocessable_entity apps/web/controllers/books/create.rb:24-25
     1.0:   assignment
     0.2:   lit_fixnum

$
```

It's not complaining about *any one thing*; it's saying that you have *enough* "things" in that method that there's likely an [SRP](http://rubyblog.pro/2017/05/solid-single-responsibility-principle-by-example) violation in there somewhere. Looking at the code, you say "Hang on; after the guard clause, *all we do is* invoke a method on the repository to create and persist a new entity *based on* the parameters being fed in, *and then* redirect. What's wrong with that?!"

As long as you're dealing with only 2-3 lines of code from a tutorial, not much is "wrong with that". However, given our tendency to copy-and-paste code from tutorials into production-destined code, which then grows significantly over time, what started out as a single conditional and less than a half-dozen lines of "real code" tends to grow. And mutate. And overwhelm the poor schmuck (likely you after a few months not seeing the code) sent in to make "a quick change".

Let's instead refactor `Create` as follows:

```ruby

module Web::Controllers::Books
  class Create
    include Web::Action

    expose :book

    params do
      required(:book).schema do
        required(:title).filled(:str?)
        required(:author).filled(:str?)
      end
    end

    def call(params)
      return report_unprocessable_entity unless params.valid?

      create_entity(params[:book])
      redirect_on_success
    end

    private

    def create_entity(book_params)
      @book = BookRepository.new.create(book_params)
    end

    def redirect_on_success
      redirect_to routes.books_path
    end

    def report_unprocessable_entity
      self.status = 422
    end
  end
end
```

When we run `bin/rake flog`, we see a significant change:

```
$ bin/rake flog
    25.5: flog total
     3.2: flog/method average

     6.2: Web::Application::configure#production apps/web/application.rb:295-323
     5.4: Web::Controllers::Books::Create#call apps/web/controllers/books/create.rb:15-19
     3.6: Web::Controllers::Books::Create#redirect_on_success apps/web/controllers/books/create.rb:28-29
     2.4: Web::Controllers::Books::Index#call apps/web/controllers/books/index.rb:8-9
$
```

Our `#call` method is now at a thoroughly respectable 5.4, and two of the three new methods are correctly noted as tiny. (The third is *so* tiny that it doesn't even make the top 60% of all methods in the code being reported on, which is Flog's default threshold.)

Now, take another look at the original code for the `#call` method compared to what we have now, and you'll see something important. Many writers and presenters reinforce the idea that methods should *either* call other methods *or* perform tasks, but *should not* mix the two in a single method. Following the guideline makes it easier for developers to reason about individual methods and their relationships by providing another level of useful bright-line categorisation. (For those of you saying "just be functional", this is a step in your direction.) The final code has half again as many lines as the original, and three new methods, but I'll buy you lunch if you can successfully defend the idea that the original code was more understandable than the final code.

*That's* the value of a tool like `flog`, and that's why it belongs in our default Rake task chain. Our (cleaned-up) `Rakefile` now looks like this:

```ruby

require 'rake'
require 'hanami/rake_tasks'
require 'rake/testtask'
require 'flog'
require 'flog_task'

class FlogTask < Rake::TaskLib
  attr_accessor :methods_only
end

Rake::TestTask.new do |t|
  t.pattern = 'spec/**/*_spec.rb'
  t.libs    << 'spec'
  t.warning = false
end

FlogTask.new do |t|
  t.verbose = true
  t.threshold = 200 # default is 200
  t.methods_only = true
  t.dirs = %w(apps lib)
end

task default: [:test, :flog]
task spec: :test
```

### Hang On, What About...

Our current Flog report now has as its highest-scored (most complex) method `Web::Application::configure#production` at 6.2. This is part of the generated file `apps/web/application.rb`, which is an intensely-commented chunk of DSL. *Since* it's an auto-generated file *and since* we haven't touched the section/method in question, it gets a "free ride"; it's something we'll keep an eye on to ask why its score goes up (if in fact it later does), but for now, we have bigger fish to shoot.

## Flay

Flay is usually pretty quiet unless it detects duplicate code or other similarly "tortured" constructs.  To add Flay support, we add the Gem to our `Gemfile:`

```ruby
  gem 'flay', '2.10.0'
```

and rebundle. To add Flay to our `Rakefile`, we include two more files:

```ruby
require 'flay'
require 'flay_task'
```

and then add the task definition:

```ruby
FlayTask.new do |t|
  t.verbose = true
  t.dirs = %w(apps lib)
end
```

Finally, we change our `default` task:

```ruby
task default: [:test, :flog, :flay]
```

Running `bin/rake` at this point then gives us a comforting report:

```
$ bin/rake
Run options: --seed 28320

# Running:

..................S

Finished in 0.076855s, 247.2186 runs/s, 494.4371 assertions/s.

19 runs, 38 assertions, 0 failures, 0 errors, 1 skips

You have skipped tests. Run with --verbose for details.
    25.5: flog total
     3.2: flog/method average

     6.2: Web::Application::configure#production apps/web/application.rb:295-323
     5.4: Web::Controllers::Books::Create#call apps/web/controllers/books/create.rb:15-19
     3.6: Web::Controllers::Books::Create#redirect_on_success apps/web/controllers/books/create.rb:28-29
     2.4: Web::Controllers::Books::Index#call apps/web/controllers/books/index.rb:8-9
Total score (lower is better) = 0
$
```

## Reek

Reek is easy to set up in its default state, and supports configuring or suppressing specific warnings either through use of a configuration file (for projectwide settings), or by inline comments (to disable specific warnings for individual methods). Reek is *comprehensive*; so much so that every project this writer has seen that uses Reek uses one or both of those suppression mechanisms to silence warnings the team choose not to address. (A common occurence, even in projects that acknowledge the value of Reek, is commit messages or comments that contain variations of "Shut *up*, Reek". 😀

Add the Gem to the `Gemfile`:

```ruby
  gem 'reek', '4.7.3'
```

and rebundle. Add `require 'rake/reek/task'` to the files `require`d by our `Rakefile`, and then the task block

```ruby
Reek::Rake::Task.new do |t|
  t.config_file = 'config.reek'
  t.source_files = '{apps,lib}/**/*.rb'
  t.reek_opts = '--sort-by smelliness --no-progress  -s'
end
```

followed by `touch config.reek` to create an empty "config" file. Run `bin/rake` to ensure that you haven't broken anything else.

Now for the moment of truth:

```
$ bin/rake reek
apps/web/controllers/home/index.rb -- 2 warnings:
  apps/web/controllers/home/index.rb:3: IrresponsibleModule: Web::Controllers::Home::Index has no descriptive comment [https://github.com/troessner/reek/blob/master/docs/Irresponsible-Module.md]
  apps/web/controllers/home/index.rb:6: UnusedParameters: Web::Controllers::Home::Index#call has unused parameter 'params' [https://github.com/troessner/reek/blob/master/docs/Unused-Parameters.md]
apps/web/controllers/books/new.rb -- 2 warnings:
  apps/web/controllers/books/new.rb:2: IrresponsibleModule: Web::Controllers::Books::New has no descriptive comment [https://github.com/troessner/reek/blob/master/docs/Irresponsible-Module.md]
  apps/web/controllers/books/new.rb:5: UnusedParameters: Web::Controllers::Books::New#call has unused parameter 'params' [https://github.com/troessner/reek/blob/master/docs/Unused-Parameters.md]
apps/web/controllers/books/index.rb -- 2 warnings:
  apps/web/controllers/books/index.rb:3: IrresponsibleModule: Web::Controllers::Books::Index has no descriptive comment [https://github.com/troessner/reek/blob/master/docs/Irresponsible-Module.md]
  apps/web/controllers/books/index.rb:8: UnusedParameters: Web::Controllers::Books::Index#call has unused parameter 'params' [https://github.com/troessner/reek/blob/master/docs/Unused-Parameters.md]
apps/web/views/home/index.rb -- 1 warning:
  apps/web/views/home/index.rb:3: IrresponsibleModule: Web::Views::Home::Index has no descriptive comment [https://github.com/troessner/reek/blob/master/docs/Irresponsible-Module.md]
apps/web/views/books/new.rb -- 1 warning:
  apps/web/views/books/new.rb:2: IrresponsibleModule: Web::Views::Books::New has no descriptive comment [https://github.com/troessner/reek/blob/master/docs/Irresponsible-Module.md]
apps/web/views/books/index.rb -- 1 warning:
  apps/web/views/books/index.rb:2: IrresponsibleModule: Web::Views::Books::Index has no descriptive comment [https://github.com/troessner/reek/blob/master/docs/Irresponsible-Module.md]
apps/web/views/books/create.rb -- 1 warning:
  apps/web/views/books/create.rb:3: IrresponsibleModule: Web::Views::Books::Create has no descriptive comment [https://github.com/troessner/reek/blob/master/docs/Irresponsible-Module.md]
apps/web/views/application_layout.rb -- 1 warning:
  apps/web/views/application_layout.rb:3: IrresponsibleModule: Web::Views::ApplicationLayout has no descriptive comment [https://github.com/troessner/reek/blob/master/docs/Irresponsible-Module.md]
apps/web/application.rb -- 1 warning:
  apps/web/application.rb:5: IrresponsibleModule: Web::Application has no descriptive comment [https://github.com/troessner/reek/blob/master/docs/Irresponsible-Module.md]
lib/bookshelf/entities/book.rb -- 1 warning:
  lib/bookshelf/entities/book.rb:1: IrresponsibleModule: Book has no descriptive comment [https://github.com/troessner/reek/blob/master/docs/Irresponsible-Module.md]
lib/bookshelf.rb -- 1 warning:
  lib/bookshelf.rb:1: IrresponsibleModule: Bookshelf has no descriptive comment [https://github.com/troessner/reek/blob/master/docs/Irresponsible-Module.md]
lib/bookshelf/repositories/book_repository.rb -- 1 warning:
  lib/bookshelf/repositories/book_repository.rb:1: IrresponsibleModule: BookRepository has no descriptive comment [https://github.com/troessner/reek/blob/master/docs/Irresponsible-Module.md]
apps/web/controllers/books/create.rb -- 1 warning:
  apps/web/controllers/books/create.rb:3: IrresponsibleModule: Web::Controllers::Books::Create has no descriptive comment [https://github.com/troessner/reek/blob/master/docs/Irresponsible-Module.md]
16 total warnings


!!! Reek has found smells - exiting!
```

Well, that was more exciting than we expected. Lots of complaints about "[*Irresponsible Module*"](https://github.com/troessner/reek/blob/master/docs/Irresponsible-Module.md) which, on visiting the link, we see is a complaint that there is no explanatory comment at the beginning of a class or module. Fine; we should add those to new classes/modules we create, but our existing code is *tiny*; let's shut that off.

Edit the `config.reek` file, with the contents

```ruby

IrresponsibleModule:
  enabled: false
```

and try again.

```
$ bin/rake reek
apps/web/controllers/home/index.rb -- 1 warning:
  apps/web/controllers/home/index.rb:6: UnusedParameters: Web::Controllers::Home::Index#call has unused parameter 'params' [https://github.com/troessner/reek/blob/master/docs/Unused-Parameters.md]
apps/web/controllers/books/new.rb -- 1 warning:
  apps/web/controllers/books/new.rb:5: UnusedParameters: Web::Controllers::Books::New#call has unused parameter 'params' [https://github.com/troessner/reek/blob/master/docs/Unused-Parameters.md]
apps/web/controllers/books/index.rb -- 1 warning:
  apps/web/controllers/books/index.rb:8: UnusedParameters: Web::Controllers::Books::Index#call has unused parameter 'params' [https://github.com/troessner/reek/blob/master/docs/Unused-Parameters.md]
3 total warnings


!!! Reek has found smells - exiting!
```

With the noise out of the way, we see that all that remains is three occurrences of [the same](https://github.com/troessner/reek/blob/master/docs/Unused-Parameters.md) code smell: action classes generated include a `#call` method that by convention takes a `params` parameter which would include any parameters passed to the controller action. Since those three actions *don't* take any parameters, the generated method signature triggers a complaint.

Let's fix all three in the traditional Ruby manner of prepending an underscore to the name of a parameter which must be specified, but isn't actually used. Here's an example for `Web::Controllers::Home::Index`:

```ruby

module Web::Controllers::Home
  class Index
    include Web::Action

    def call(_params)
    end
  end
end
```

After making the changes, running `bin/rake reek` is uneventful:

```
$ bin/rake reek
0 total warnings
$
```

Update the `Rakefile` again so that our `default` task is defined as `[:test, :flog, :flay, :reek]` and move on.

## RuboCop

We've put off RuboCop to be the last of these tools added to our Rake setup because it's by far the most complex and changing over time, requiring either configuration or code changes as it seeks to more fully track the (itself evolving) community [*Ruby Style Guide*](https://github.com/bbatsov/ruby-style-guide).

The current version of RuboCop as this is written is 0.52.0, which may well be out-of-date by the time you read this; adjust your changes accordingly.  First, we add the Gem to the `Gemfile`:

```ruby
  gem 'rubocop', '0.52.0'
```

Then, as usual, we add the Rake task to the `Rakefile`:

```ruby
RuboCop::RakeTask.new(:rubocop) do |task|
  task.patterns = [
    'apps/**/*.rb',
    'lib/**/*.rb',
    'spec/**/*.rb'
  ]
  task.formatters = ['simple', 'd']
  task.fail_on_error = true
  # task.options << '--rails'
  task.options << '--config=.rubocop.yml'
  task.options << '--display-cop-names'
end
```

Remember to run `touch .rubocop.yml` to create an empty configuration file, as mentioned in the Rake task.

And, finally, we can run Rake's default task (to make sure nothing broke) and then the new `rubocop` task:

```
$ bin/rake
Run options: --seed 25359

# Running:

.......S...........

Finished in 0.194825s, 97.5234 runs/s, 195.0467 assertions/s.

19 runs, 38 assertions, 0 failures, 0 errors, 1 skips

You have skipped tests. Run with --verbose for details.
    25.5: flog total
     3.2: flog/method average

     6.2: Web::Application::configure#production apps/web/application.rb:295-323
     5.4: Web::Controllers::Books::Create#call apps/web/controllers/books/create.rb:15-19
     3.6: Web::Controllers::Books::Create#redirect_on_success apps/web/controllers/books/create.rb:28-29
     2.4: Web::Controllers::Books::Index#call apps/web/controllers/books/index.rb:8-9
Total score (lower is better) = 0
0 total warnings
$ bin/rake rubocop
Running RuboCop...
== apps/web/application.rb ==
C:  5:  3: Style/Documentation: Missing top-level class documentation comment.
C:  6:  5: Metrics/BlockLength: Block has too many lines. [40/25]
C: 21: 21: Style/WordArray: Use %w or %W for an array of words.
C:236: 40: Style/PercentLiteralDelimiters: %-literals should be delimited by ( and ).
== apps/web/config/routes.rb ==
C:  8: 25: Style/SymbolArray: Use %i or %I for an array of symbols.
== apps/web/controllers/books/create.rb ==
C:  2:  8: Style/ClassAndModuleChildren: Use nested module/class definitions instead of compact style.
C:  3:  3: Style/Documentation: Missing top-level class documentation comment.
== apps/web/controllers/books/index.rb ==
C:  2:  8: Style/ClassAndModuleChildren: Use nested module/class definitions instead of compact style.
C:  3:  3: Style/Documentation: Missing top-level class documentation comment.
== apps/web/controllers/books/new.rb ==
C:  1:  8: Style/ClassAndModuleChildren: Use nested module/class definitions instead of compact style.
C:  2:  3: Style/Documentation: Missing top-level class documentation comment.
C:  5:  5: Style/EmptyMethod: Put empty method definitions on a single line.
== apps/web/controllers/home/index.rb ==
C:  2:  8: Style/ClassAndModuleChildren: Use nested module/class definitions instead of compact style.
C:  3:  3: Style/Documentation: Missing top-level class documentation comment.
C:  6:  5: Style/EmptyMethod: Put empty method definitions on a single line.
== apps/web/views/application_layout.rb ==
C:  3:  5: Style/Documentation: Missing top-level class documentation comment.
== apps/web/views/books/create.rb ==
C:  2:  8: Style/ClassAndModuleChildren: Use nested module/class definitions instead of compact style.
C:  3:  3: Style/Documentation: Missing top-level class documentation comment.
== apps/web/views/books/index.rb ==
C:  1:  8: Style/ClassAndModuleChildren: Use nested module/class definitions instead of compact style.
C:  2:  3: Style/Documentation: Missing top-level class documentation comment.
== apps/web/views/books/new.rb ==
C:  1:  8: Style/ClassAndModuleChildren: Use nested module/class definitions instead of compact style.
C:  2:  3: Style/Documentation: Missing top-level class documentation comment.
== apps/web/views/home/index.rb ==
C:  2:  8: Style/ClassAndModuleChildren: Use nested module/class definitions instead of compact style.
C:  3:  3: Style/Documentation: Missing top-level class documentation comment.
== lib/bookshelf.rb ==
C:  1:  1: Style/Documentation: Missing top-level module documentation comment.
== spec/features_helper.rb ==
C:  9:  7: Style/ClassAndModuleChildren: Use nested module/class definitions instead of compact style.
== spec/web/controllers/books/create_spec.rb ==
C:  4:  1: Metrics/BlockLength: Block has too many lines. [32/25]
C: 13: 81: Metrics/LineLength: Line is too long. [82/80]
== spec/web/views/application_layout_spec.rb ==
C:  1:  9: Style/StringLiterals: Prefer single-quoted strings when you don't need string interpolation or special symbols.
C:  6: 81: Metrics/LineLength: Line is too long. [90/80]
== spec/web/views/books/create_spec.rb ==
C:  5: 81: Metrics/LineLength: Line is too long. [92/80]
== spec/web/views/books/index_spec.rb ==
C:  6: 81: Metrics/LineLength: Line is too long. [91/80]
C: 16: 81: Metrics/LineLength: Line is too long. [81/80]
C: 22: 81: Metrics/LineLength: Line is too long. [85/80]
C: 32: 81: Metrics/LineLength: Line is too long. [81/80]
== spec/web/views/books/new_spec.rb ==
C:  5: 81: Metrics/LineLength: Line is too long. [118/80]
C:  7: 81: Metrics/LineLength: Line is too long. [89/80]

28 files inspected, 37 offenses detected

Cops disabled line ranges:

RuboCop failed!
$
```

Hmmm. Let's see what RuboCop's "auto-correct" does to/for us:

```
$ bin/rubocop -a apps lib spec
Inspecting 28 files
CCCCCCCCCCCC..C...C.....CCCC

Offenses:

apps/web/application.rb:5:3: C: Style/Documentation: Missing top-level class documentation comment.
  class Application < Hanami::Application
  ^^^^^
apps/web/application.rb:6:5: C: Metrics/BlockLength: Block has too many lines. [40/25]
    configure do ...
    ^^^^^^^^^^^^
apps/web/application.rb:21:21: C: [Corrected] Style/WordArray: Use %w or %W for an array of words.
      load_paths << [ ...
                    ^
apps/web/application.rb:22:1: C: [Corrected] Layout/IndentArray: Use 2 spaces for indentation in an array, relative to the start of the line where the left square bracket is.
controllers 
^^^^^^^^^^^
apps/web/application.rb:22:12: C: [Corrected] Layout/TrailingWhitespace: Trailing whitespace detected.
controllers 
           ^
apps/web/application.rb:23:1: C: [Corrected] Layout/AlignArray: Align the elements of an array literal if they span more than one line.
views
^^^^^
apps/web/application.rb:23:6: C: [Corrected] Layout/MultilineArrayBraceLayout: Closing array brace must be on the line after the last array element when opening brace is on a separate line from the first array element.
views]
     ^
apps/web/application.rb:24:1: C: [Corrected] Layout/IndentArray: Indent the right bracket the same as the start of the line where the left bracket is.
]
^
apps/web/application.rb:236:40: C: [Corrected] Style/PercentLiteralDelimiters: %-literals should be delimited by ( and ).
      security.content_security_policy %{ ...
                                       ^^
apps/web/config/routes.rb:8:25: C: [Corrected] Style/SymbolArray: Use %i or %I for an array of symbols.
resources :books, only: [:index, :new, :create]
                        ^^^^^^^^^^^^^^^^^^^^^^^
apps/web/controllers/books/create.rb:2:8: C: Style/ClassAndModuleChildren: Use nested module/class definitions instead of compact style.
module Web::Controllers::Books
       ^^^^^^^^^^^^^^^^^^^^^^^
apps/web/controllers/books/create.rb:3:3: C: Style/Documentation: Missing top-level class documentation comment.
  class Create
  ^^^^^
apps/web/controllers/books/index.rb:2:8: C: Style/ClassAndModuleChildren: Use nested module/class definitions instead of compact style.
module Web::Controllers::Books
       ^^^^^^^^^^^^^^^^^^^^^^^
apps/web/controllers/books/index.rb:3:3: C: Style/Documentation: Missing top-level class documentation comment.
  class Index
  ^^^^^
apps/web/controllers/books/new.rb:1:8: C: Style/ClassAndModuleChildren: Use nested module/class definitions instead of compact style.
module Web::Controllers::Books
       ^^^^^^^^^^^^^^^^^^^^^^^
apps/web/controllers/books/new.rb:2:3: C: Style/Documentation: Missing top-level class documentation comment.
  class New
  ^^^^^
apps/web/controllers/books/new.rb:5:5: C: [Corrected] Style/EmptyMethod: Put empty method definitions on a single line.
    def call(_params) ...
    ^^^^^^^^^^^^^^^^^
apps/web/controllers/home/index.rb:2:8: C: Style/ClassAndModuleChildren: Use nested module/class definitions instead of compact style.
module Web::Controllers::Home
       ^^^^^^^^^^^^^^^^^^^^^^
apps/web/controllers/home/index.rb:3:3: C: Style/Documentation: Missing top-level class documentation comment.
  class Index
  ^^^^^
apps/web/controllers/home/index.rb:6:5: C: [Corrected] Style/EmptyMethod: Put empty method definitions on a single line.
    def call(_params) ...
    ^^^^^^^^^^^^^^^^^
apps/web/views/application_layout.rb:3:5: C: Style/Documentation: Missing top-level class documentation comment.
    class ApplicationLayout
    ^^^^^
apps/web/views/books/create.rb:2:8: C: Style/ClassAndModuleChildren: Use nested module/class definitions instead of compact style.
module Web::Views::Books
       ^^^^^^^^^^^^^^^^^
apps/web/views/books/create.rb:3:3: C: Style/Documentation: Missing top-level class documentation comment.
  class Create
  ^^^^^
apps/web/views/books/index.rb:1:8: C: Style/ClassAndModuleChildren: Use nested module/class definitions instead of compact style.
module Web::Views::Books
       ^^^^^^^^^^^^^^^^^
apps/web/views/books/index.rb:2:3: C: Style/Documentation: Missing top-level class documentation comment.
  class Index
  ^^^^^
apps/web/views/books/new.rb:1:8: C: Style/ClassAndModuleChildren: Use nested module/class definitions instead of compact style.
module Web::Views::Books
       ^^^^^^^^^^^^^^^^^
apps/web/views/books/new.rb:2:3: C: Style/Documentation: Missing top-level class documentation comment.
  class New
  ^^^^^
apps/web/views/home/index.rb:2:8: C: Style/ClassAndModuleChildren: Use nested module/class definitions instead of compact style.
module Web::Views::Home
       ^^^^^^^^^^^^^^^^
apps/web/views/home/index.rb:3:3: C: Style/Documentation: Missing top-level class documentation comment.
  class Index
  ^^^^^
lib/bookshelf.rb:1:1: C: Style/Documentation: Missing top-level module documentation comment.
module Bookshelf
^^^^^^
spec/features_helper.rb:9:7: C: Style/ClassAndModuleChildren: Use nested module/class definitions instead of compact style.
class MiniTest::Spec
      ^^^^^^^^^^^^^^
spec/web/controllers/books/create_spec.rb:4:1: C: Metrics/BlockLength: Block has too many lines. [32/25]
describe Web::Controllers::Books::Create do ...
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
spec/web/controllers/books/create_spec.rb:13:81: C: Metrics/LineLength: Line is too long. [82/80]
    let(:params) { Hash[book: { title: 'Confident Ruby', author: 'Avdi Grimm' }] }
                                                                                ^^
spec/web/views/application_layout_spec.rb:1:9: C: [Corrected] Style/StringLiterals: Prefer single-quoted strings when you don't need string interpolation or special symbols.
require "spec_helper"
        ^^^^^^^^^^^^^
spec/web/views/application_layout_spec.rb:6:81: C: Metrics/LineLength: Line is too long. [90/80]
  let(:template) { Hanami::View::Template.new('apps/web/templates/application.html.erb') }
                                                                                ^^^^^^^^^^
spec/web/views/books/create_spec.rb:5:81: C: Metrics/LineLength: Line is too long. [92/80]
  let(:template)  { Hanami::View::Template.new('apps/web/templates/books/create.html.erb') }
                                                                                ^^^^^^^^^^^^
spec/web/views/books/index_spec.rb:6:81: C: Metrics/LineLength: Line is too long. [91/80]
  let(:template)  { Hanami::View::Template.new('apps/web/templates/books/index.html.erb') }
                                                                                ^^^^^^^^^^^
spec/web/views/books/index_spec.rb:16:81: C: Metrics/LineLength: Line is too long. [81/80]
      rendered.must_include('<p class="placeholder">There are no books yet.</p>')
                                                                                ^
spec/web/views/books/index_spec.rb:22:81: C: Metrics/LineLength: Line is too long. [85/80]
    let(:book2)     { Book.new(title: 'Domain Driven Design', author: 'Eric Evans') }
                                                                                ^^^^^
spec/web/views/books/index_spec.rb:32:81: C: Metrics/LineLength: Line is too long. [81/80]
      rendered.wont_include('<p class="placeholder">There are no books yet.</p>')
                                                                                ^
spec/web/views/books/new_spec.rb:5:81: C: Metrics/LineLength: Line is too long. [118/80]
  let(:params)    { OpenStruct.new(valid?: false, error_messages: ['Title must be filled', 'Author must be filled']) }
                                                                                ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
spec/web/views/books/new_spec.rb:7:81: C: Metrics/LineLength: Line is too long. [89/80]
  let(:template)  { Hanami::View::Template.new('apps/web/templates/books/new.html.erb') }
                                                                                ^^^^^^^^^

28 files inspected, 42 offenses detected, 11 offenses corrected
$
```

11 of 42 offences were auto-corrected; not bad. Running `bin/rake` again tells us that nothing got (detectably) broken along the way.  The remaining offences fall into a limited number of categories:

* [`Style/Documentation`](https://rubocop.readthedocs.io/en/latest/cops_style/#styledocumentation): a class didn't have a "top-level module documentation comment", just as with Reek above;
* [`Metrics/LineLength`](https://rubocop.readthedocs.io/en/latest/cops_metrics/#metricslinelength): a line of code exceeded 80 character positions, and RuboCop auto-correct doesn't support splitting those specific lines;
* [`Style/FrozenStringLiteralComment`](https://rubocop.readthedocs.io/en/latest/cops_style/#stylefrozenstringliteralcomment): a file parsed by a Ruby 2.2 or later interpreter does not have a `# frozen_string_literal: true` line, instructing the MRI interpreter to interpret strings as always "frozen", in conformance with planned Ruby 3.0 behaviour;
* [`Style/ClassAndModuleChildren`](https://rubocop.readthedocs.io/en/latest/cops_style/#styleclassandmodulechildren): RuboCop flags "compact style" module/class definitions, in preference to nested module/class definitions. The Style Guide has [a good explanation](https://github.com/bbatsov/ruby-style-guide#namespace-definition) of the rationale, which links to a [good discussion of constant lookup in Ruby](https://cirw.in/blog/constant-lookup.html).

There is also one occurrence of [`Metrics/BlockLength`](https://rubocop.readthedocs.io/en/latest/cops_metrics/#metricsblocklength) in an auto-generated file.

Fortunately, RuboCop supports [configuration](https://rubocop.readthedocs.io/en/latest/configuration/) using a configuration file and/or in-line comments; this will be useful here.

First off, we'll use a configuration file to disable the `Style/Documentation` cop globally. This, and the equivalent configuration for Reek, should be filed as an issue on a production-destined, as-generated codebase.

Secondly, we'll add a directive to the configuration file to disable the `Metrics/BlockLength` cop on the auto-generated file (`apps/web/application.rb`).

Third, we'll disable the `Style/FrozenStringLiteralComment` cop in the configuration file, with the same documented warning as for `Style/Documentation`.

Next, we'll reformat the too-long lines, which are *exclusively* in specs.

Finally, we'll reformat the source files (and `spec/features_helper`) that use the "compact" module/class notation, ensuring that other cops (e.g., line length) are kept happy in that process.

### Configuration File

Our configuration file, saved in `.rubocop.yml` in the project top-level directory, is as follows:

```yaml

AllCops:
  TargetRubyVersion: 2.4

# This is *temporary*; when we fix it, remember to update Reek config as well.
Style/Documentation:
  Exclude:
    - apps/**/*.rb
    - lib/bookshelf.rb
    - spec/features_helper.rb

# This is also *temporary*; Real Code&trade; sources should include the comment.
Style/FrozenStringLiteralComment:
  Enabled: false

# It's likely that we'll eventually broaden this to include *all* specs.
Metrics/BlockLength:
  Exclude:
    - apps/web/application.rb
    - spec/**/*.rb
```

### Reformatting Too-Long Lines in Specs

Now, we need to edit each of the (spec) files which RuboCop marked as having too-long lines. We'll use the current `spec/web/controllers/books/create_spec.rb` as an example, whose Line 13 currently reads:

```ruby
    let(:params) { Hash[book: { title: 'Confident Ruby', author: 'Avdi Grimm' }] }
```

Changing that to

```ruby
    let(:params) do
      Hash[book: { title: 'Confident Ruby', author: 'Avdi Grimm' }]
    end
```

remedies the RuboCop offence without affecting the function of the spec file. Modify the remaining line-length violations similarly.

### Using Properly-Nested Namespaces

We presently have nine files (eight under `apps/web` and one as `spec/features_helper.rb`) with the "compact" module definition which, as noted earlier, is disallowed by the style guide enforced by RuboCop.

The change required for `spec/features_helper.rb` is simple and useful as an example. The existing code reopens the `MiniTest::Spec` class like so:

```ruby
class MiniTest::Spec
  include Capybara::DSL
end
```

Changing that to

```ruby
module MiniTest
  class Spec
    include Capybara::DSL
  end
end
```

leaves specs running successfully, as verified by `bin/rake`, and removes the `spec/features_helper.rb` file from those complained about by `bin/rake rubocop`.

Make similar changes to the eight remaining files, being careful not to introduce new violations such as excessive line length.


