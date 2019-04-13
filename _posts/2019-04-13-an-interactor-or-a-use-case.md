---
layout: post
title: "An Interactor or a Use Case?"
description: "A discussion of a simple feature that can be implemented in a Hanami interactor, and why you might want to think differently"
tags: [ruby, clean architecture, hanami, dry-rb, monads, use cases]
categories: [development]
comments: true
fullview: false
---

To give some context, I'm still fairly new to Hanami (very small apps in limited use thus far) but, after _years_ of evolving my architectural thinking to (initially independently) approximate "Uncle" Bob Martin's [Clean Architecture](https://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html) first in Rails, then Sinatra, followed by [Roda](https://roda.jeremyevans.net/), I've been completely sold on [Hanami](https://www.hanamirb.org/) for over a year now.

I'd previously worked with interactors before seeing Hanami's implementation, most notably and repeatedly Aaron Lasseigne's (initially OrgSync's) [ActiveInteraction](https://github.com/AaronLasseigne/active_interaction), but they always felt a bit "weird" in Rails; you're building and enforcing boundaries in a system expressly designed to obliterate them in the belief that doing so enhances developer happiness and (initial) productivity. It's much less of an issue with other frameworks, particularly Hanami, which for the most part (as noted above) encourages Clean Architecture.

It's easy to get sloppy, though, writing [Hanami interactors](https://www.rubydoc.info/gems/hanami-utils/Hanami/Interactor/Interface). Like most functional(-inspired) tools, the main entrypoint is a `#call` method, which can be skipped entirely if an optional private `#valid?` method returns `false`.

## Description of the Example

Let's look at a (rather simplified) example. For an app I'm working on, I implemented a `Web::Controller::Session` module with three [controller-action classes](https://guides.hanamirb.org/actions/overview/) to serve endpoints: `Session::New` presents a bog-standard login form (unless there already is a current user) which `Session::Create` uses to authenticate a user (which our app calls a "Member"). `Session::Delete`, obviously enough, is used to sign out the current user. Using the [CryptIdent](https://github.com/jdickey/crypt_ident) authentication library (which, as its author, I have a rather biased opinion of), I wrote code for these actions.

Here, we'll look at the evolution of the `Session::Create` controller action and the domain logic it exercises, to illustrate how one might use interactors for such a task, and why one might reach for an alternative instead.

## Baseline Zero: All Logic in Controller Action

Here's the code for the first whack of the `Session::Create` controller action:

```ruby
# frozen_string_literal: true

module Web
  module Controllers
    module Session
      class Create
        include Web::Action
        include Web::RequireGuest # prevents execution if a Member has been Signed In
        include Hanami::Action::Session

        params do
          required(:member).schema do
            required(:name).filled(:str?)
            required(:password).filled(:str?)
          end
        end

        # rubocop:disable Metrics/AbcSize, Metrics/MethodLength, Style/GuardClause, Metrics/LineLength
        def call(params)
          if params.valid?
            member = UserRepository.new.find_member(params.dig(:member, :name))
            sign_in(member, params.dig(:member, :password)) do |result|
              result.success do |user:|
                @current_user = user
                flash[:info] = 'Signed in successfully'
              end
              result.failure do
                @current_user = CryptIdent.config.guest_user
                flash[:error] = 'Invalid Member Name or Password'
              end
            end
            session[:current_user] = @current_user
            redirect_to Web.routes.root_path
          end
        end # #call
        # rubocop:enable Metrics/AbcSize, Metrics/MethodLength, Style/GuardClause, Metrics/LineLength
      end
    end
  end
end
```

The [`CryptIdent#sign_in`](https://www.rubydoc.info/github/jdickey/crypt_ident/CryptIdent#sign_in-instance_method) method is implemented using dry-monads' [`Result` monads](https://dry-rb.org/gems/dry-monads/1.0/result/) with dry-matcher's [`Result` matcher](https://dry-rb.org/gems/dry-matcher/result-matcher/), which blew me away by how that let me structure and think clearly about my code actions. Everything* either resulted in a Success or a Failure which (optionally) conveyed specific data items along with the result, and dealing with those in a uniform way was one less thing to have to think about. The resulting code isn't as bad as some we've all maintained recently, but it's hardly clean code, as demonstrated by the number of RuboCop cops disabled for the `#call` method.

## Iteration One: Extracting an Interactor

This really clearly lends itself to an interactor, with the non-controller-specific code extracted:

```ruby
# frozen_string_literal: true
# lib/the_app/interactors/sign_in_member.rb

require 'hanami/interactor'

class SignInValidator
  include Hanami::Validations

  validations do
    required(:name) { filled? & str? }
    required(:password) { filled? & str? }
  end
end # class SignInValidator

class SignInMember
  include Hanami::Interactor

  expose :current_user

  # We extend CryptIdent at runtime, rather than including it at parse time.
  # This lets Hanami's setup of everything under `lib` complete before we try to
  # access a `UserRepository`. We could include CryptIdent from a CAC, because
  # by the time the delivery mechanism(s) is/are loaded, the app logic (on which
  # they depend) is already finalised. Depending on cross-component domain logic
  # initialisation can get tricky.
  def initialize(repo: UserRepository.new)
    @repo = repo
    extend CryptIdent
  end

  def call(params)
    sign_in(member, params[:password]) do |result|
      result.success do |user:|
        @current_user = user
      end
      result.failure do
        @current_user = repo.guest_user
        error! 'Invalid Member Name or Password'
      end
    end
  end

  private

  attr_reader :member, :repo

  def find_member(params)
    @member = repo.find_member(params[:name])
    !member.nil?
  end

  def validate_params(params)
    result = SignInValidator.new(params).validate
    result.errors.each do |attrib, messages|
      messages.each do |message|
        error([attrib.to_s.capitalize, message].join(' '))
      end
    end
    yield params if result.success?
  end

  def valid?(params)
    validate_params(params) { |inner_params| find_member(inner_params) }
  end
end
```

This felt good to initially get working so quickly, but it has a few rather obvious problems, particularly centering around `#valid?`. That method, and `#validate_params`, both know too much about other bits of the code. The `#find_member` method introduces an instance variable so that `#call` won't have to re-query the persistence layer to find the user (`member`) to pass into `#sign_in` at line 32. The block nesting within `#valid?` will become unwieldy if additional validity checks are added in future (e.g., a suspended or banned user won't be deleted from the system, but will be prevented from signing in). Adding more checks will either make for deeper nesting or introduce a maze of explicit, imperative if/then/else "tentative programming" that is *so* easy to get wrong. There's got to be a better way.

At least the controller code has been cleaned up a bit:

```ruby
# frozen_string_literal: true
# apps/web/controllers/session/create.rb

module Web
  module Controllers
    module Session
      class Create
        # ...

        def call(params) # rubocop:disable Metrics/MethodLength
          result = SignInMember.new.call(params[:member])
          if result.success?
            @current_user = result.current_user # exposed from Web::CommonAuthn
            flash[:info] = 'Signed in successfully'
          else
            @current_user = CryptIdent.config.guest_user
            flash[:error] = 'Invalid Member Name or Password'
          end
          session[:current_user] = @current_user
          redirect_to Web.routes.root_path
        end # #call

        # ...

      end # class Web::Controllers::Session::Create
    end
  end
end
```

Everything is controller-specific handling of the result from calling the interactor: setting flash messages and session data, redirecting (and, not shown, validating incoming parameters).

I'm happy enough with the controller code; but why does the interactor feel wrong?

## Interlude: Doubt, Dissatisfaction, Discovery

One of the habits that I'd fallen into with (other) interactors was to encapsulate any bit of reusable domain logic in its own interactor, such as a `ConfirmValidPassword` interactor that was used both when resetting a lost password and setting a new one for a presently-signed-in user. That, in turn, led to a subtle but important change in how I thought of interactors: rather than exclusively as complete encapsulations of domain logic separate from controller actions, they were now that *plus* any assorted bits of supporting logic. My `lib/the_app/interactors` folder was on track to becoming the kind of universal junk drawer that `lib` so typically is in a Rails project. That wasn't what I wanted; I couldn't point someone at my `interactors` folder and have them be able to tell at a glance what were implementations of logic to serve a specific controller action, versus what were general-purpose supporting components.

What I wanted was something more akin to Jacobson [use cases](https://www.ivarjacobson.com/sites/default/files/field_iji_file/article/use-case_2_0_jan11.pdf). Those "tell a story" revolving around a sequence of steps taken in response to a *trigger* event by an *actor* who initiates the action. They should obviously reuse truly common code, but it should be immediately obvious both in terms of the project layout and in terms of the use case content itself what are shared components and what are not.

I had been on the [`hanami/chat`](https://gitter.im/hanami/chat) channel in Gitter for some time. A few days ago, I asked

> Has anyone tried using dry-rb Result matchers (or, perhaps better, dry-transaction's `step` sequencing) in/with Hanami interactors? How did that go? I'm thinking of this as I look at one of my own "higher-order" interactors that uses two others, calling first one and then, if that was successful, passing its result in as input to the second. What I wind up with is awfully procedural, and I'd like to clean it up a bit. I've used Result matchers before, and I know that Hanami and dry-rb are supposed to be more thoroughly integrated going forward, but it seems like interactors, in their current incarnation, are somewhat orthogonal to the dry-rb way of structuring things.

[Viet (Drake) Tran](https://github.com/tiev) stepped up and pointed out that

> Interactor and dry-rb result are two implementations of the same concept. So I think you should follow only one of them. And to use dry-rb result with more elegant syntax, do-notation is the best [https://dry-rb.org/gems/dry-monads/1.0/do-notation/](https://dry-rb.org/gems/dry-monads/1.0/do-notation/)

He was right. Let me show you what I mean.

## Iteration Two: A Different Use-Case Approach, and Enlightenment

I reworked the guts of the previous interactor into this:

```ruby
# frozen_string_literal: true
# lib/the_app/use_cases/sign_in_member.rb

require 'dry/monads/result'
require 'dry/monads/do'

module UseCases
  # Use case implementation for signing in a Member. Makes use of the CryptIdent
  # authentication library, and is implemented using dry-monads' 'do notation`.
  # See https://dry-rb.org/gems/dry-monads/1.0/do-notation/ for a discussion.
  class SignInMember
    include Dry::Monads::Result::Mixin
    include Dry::Monads::Do.for(:call)

    def initialize(repo: UserRepository.new)
      extend CryptIdent
      @repo = repo
    end

    def call(params)
      values = yield validate(params[:member])
      member = yield find_member(values)
      current_user = yield authenticate_member(member, values[:password])

      Success(current_user)
    end

    private

    attr_reader :repo

    def authenticate_member(member, password)
      sign_in(member, password) do |result|
        result.success do |user:|
          Success(user)
        end
        result.failure do |code:|
          Failure(sign_in: [code.to_s])
        end
      end
    end

    def find_member(values)
      member = repo.find_member(values[:name])
      Failure(member: ['Name or password not valid']) unless member

      Success(member)
    end

    def validate(params)
      result = SignInValidator.new(params).validate
      return Failure(result.errors) if result.failure?

      Success(result.to_h)
    end

    class SignInValidator
      include Hanami::Validations

      validations do
        required(:name) { filled? & str? }
        required(:password) { filled? & str? }
      end
    end # class UseCases::SignInMember::SignInValidator
  end # class UseCases::SignInMember
end
```

Notice how each step (`#validate`, `#find_member`, and `#authenticate_member`) is self-contained; the only bit of state introduced in the entire class is a Repository assigned to in `#initialize` and read from in `#find_member`; this allows a test double to be injected rather than using the default `UserRepository.new` instance. Each of the three "step" methods, as well as `#call`, make success or failure unavoidably obvious, and the beauty of "do notation" is that a Failure reported by any method `yield`ed to from `#call` causes `#call` to return that Failure in lieu of executing any further steps.

So what does this do to the interactor and the controller action? The code that would remain in an interactor becomes so trivial that it can safely be moved into the controller action directly:

```ruby
# frozen_string_literal: true
# apps/web/controllers/session/new.rb

module Web
  module Controllers
    module Session
      class Create
        include Web::Action
        include Web::RequireGuest
        include Hanami::Action::Session

        params do
          required(:member).schema do
            required(:name).filled(:str?)
            required(:password).filled(:str?)
          end
        end

        def call(params)
          result = SignInMember.new.call(params)
          @current_user = result.value_or(CryptIdent.config.guest_user)
          session[:current_user] = @current_user
          flash_message_for(result)
          redirect_to Web.routes.root_path
        end # #call

        private

        def flash_message_for(result)
          if result.success?
            flash[:info] = 'Signed in successfully'
          else
            flash[:error] = 'Invalid Member Name or Password'
          end
        end
      end
    end
  end
end
```

There are only two bits of code here that know or even care if the result was successful or not. One, obviously, is in `#flash_message_for`; the other is at line 21, where `@current_user` is assigned the value from the successful result of `SignInMember` or, on failure, the "guest user". That line is also the _only_ line of code in the class that has any knowledge whatsoever of what a regular Member or a "guest user" *are*. Compare that to the last controller code you worked on. 😀

Thoughts? Anything I could have done better? Disagree with my approach? Let's have a chat about it; leave me a comment here or find me on Gitter in [`hanami/chat`](https://gitter.im/hanami/chat).

Thanks!

