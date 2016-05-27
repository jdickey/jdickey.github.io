---
layout: post
title: "Ruby Is Not Haskell. Deal."
description: "Just because a language *can* do a thing, does not make it the *best tool* for that thing."
tags: [ruby, haskell, development, craft, tumblr]
categories: [not yet categorised]
comments: true
fullview: false
---

And that's a Very Good Thing&trade; for *both* language communities. Yes, it's good to know both languages and to use each where they make sense. But They're *Not* The Same Thing. Obvious, yes?

### Introduction

Abe Voelker wrote a [blog post](https://blog.abevoelker.com/you-got-haskell-in-my-ruby-cleaner-ruby-validations-using-either-monad-kleisli-gem/) in which he sang the praises of the [Kleisli](https://github.com/txus/kleisli) Gem to, in effect, let you write Haskell code in your Ruby. He started out by demonstrating a "naive" validation object for validating an uploaded image
according to four criteria, verifying that the image:

1. Has a size of less than one megabyte;
1. Is a valid image format;
1. Is of type PNG, JPEG, or GIF; and
1. Has dimensions of less than 5,000 by 5,000 pixels.

In addition, he states a requirement for "a JSON-friendly version of the validation return value as the website is uploading the image via Ajax".

Fine. Easy enough, without resorting to exotic ["Reese's Pieces"](https://www.youtube.com/watch?v=DJLDF6qZUX0) language concoctions.

### Hasty Rewrite

The Gist copied at the end of this post is what I'd consider a *first draft* of a rewritten `AvatarValidator` class. It's a somewhat more readable version of the original; both are still *massive* SRP violations. They do too much; they have [leaky abstractions](http://www.joelonsoftware.com/articles/LeakyAbstractions.html) through and through. But this is still an improvement:

#### Command Pattern and State Machine

The [original code](https://blog.abevoelker.com/you-got-haskell-in-my-ruby-cleaner-ruby-validations-using-either-monad-kleisli-gem/#version-1-naive-validation-object) made was clearly a classic [command pattern](https://blog.abevoelker.com/you-got-haskell-in-my-ruby-cleaner-ruby-validations-using-either-monad-kleisli-gem/#version-1-naive-validation-object), but implemented in a massive nested-if furball. You had to squint to see that it was implementing a stepwise state machine, with subsequent states being implemented as ever-more-deeply-nested `if` blocks.

[In the first replacement](https://gist.github.com/jdickey/8eaf646fbd754408c6d9), the [`#validate` method](https://gist.github.com/jdickey/8eaf646fbd754408c6d9#file-avatar_validator_v1-rb-L45-L54) (inside the guard clause) is a sequence of calls to `private` methods with intention-revealing names that (should) make it easier to find what you're looking for, or to understand the overall process.

#### Error Detection and Message Output

The [original code](https://blog.abevoelker.com/you-got-haskell-in-my-ruby-cleaner-ruby-validations-using-either-monad-kleisli-gem/#version-1-naive-validation-object) has inline, nested `if` statements whose `else` clauses each build and return literal strings, more than doubling the length of the `#validate` method (lines 10-33) compared with the [new implementation](https://gist.github.com/jdickey/8eaf646fbd754408c6d9#file-avatar_validator_v1-rb-L45-L54). Changes to the original output messages do not originate in a ["single source of truth"](https://en.wikipedia.org/wiki/Single_Source_of_Truth), which complicates and adds risk to change; are you *sure* you've done everything and *only* what you set out to do? (Remember, there are *no tests yet*.)

In the new code, any validation error detected will raise a `RuntimeError` whose message [encodes](https://gist.github.com/jdickey/8eaf646fbd754408c6d9#file-avatar_validator_v1-rb-L86-L90) *an identifier* for the error, along with a single relevant data item. The [handler](https://gist.github.com/jdickey/8eaf646fbd754408c6d9#file-avatar_validator_v1-rb-L52-L53) for that `RuntimeError` calls a [`private` method](https://gist.github.com/jdickey/8eaf646fbd754408c6d9#file-avatar_validator_v1-rb-L75-L84) that centralises all text strings for error messages, making it easy to change later if needed (e.g, for i18n).

#### What's Our Input?

The [original code](https://blog.abevoelker.com/you-got-haskell-in-my-ruby-cleaner-ruby-validations-using-either-monad-kleisli-gem/#version-1-naive-validation-object) passes in an `uploaded_file` parameter, which is assigned to an instance variable (at line 7). That ivar is then used, duck-type style, to find its size (by calling the `#size` method at line 12) and by calling the `path` method *of the return object from `uploaded_file`'s `#tempfile` method* at line 15).

In contrast, the first whack at a replacement `AvatarValidator` has a nested class, [`AvatarValidator::UploadedFile`](https://gist.github.com/jdickey/8eaf646fbd754408c6d9#file-avatar_validator_v1-rb-L7-L38), which reports the `#size` (needed for validation) and produces a [`MiniMagick::Image` instance](https://gist.github.com/jdickey/8eaf646fbd754408c6d9#file-avatar_validator_v1-rb-L16-L18) of the data in the uploaded file ([however that file was specified](https://gist.github.com/jdickey/8eaf646fbd754408c6d9#file-avatar_validator_v1-rb-L28-L37), whether as a blob of data, a filename or an instance of `AvatarValidator::UploadedFile` itself).

### Known Problems

1. There are no tests. Even if there were, it's tedious/dangerous to attempt to test individual validation steps in isolation.
1. The image-dimension validation now separately checks height and width, reporting different error messages when invalid; the [original code](https://blog.abevoelker.com/you-got-haskell-in-my-ruby-cleaner-ruby-validations-using-either-monad-kleisli-gem/#version-1-naive-validation-object) (see lines 18-19) did not.
1. `AvatarValidator` still has repetitive code. The internal validation-step methods ([`#verify_file_size`](https://gist.github.com/jdickey/8eaf646fbd754408c6d9#file-avatar_validator_v1-rb-L96-L99), [`#validate_image_format`](https://gist.github.com/jdickey/8eaf646fbd754408c6d9#file-avatar_validator_v1-rb-L101-L106), [`#verify_allowed_format`](https://gist.github.com/jdickey/8eaf646fbd754408c6d9#file-avatar_validator_v1-rb-L108-L112), and (to a slightly lesser degree) [`#verify_image_dimensions`](https://gist.github.com/jdickey/8eaf646fbd754408c6d9#file-avatar_validator_v1-rb-L114-L119)) each follow the same pattern: bail out if we're valid, or call `#fail_with` if we're not.
1. This code is unloved by [RuboCop](https://github.com/bbatsov/rubocop/), a widely-used Ruby style checker and our shop standard. In particular, the offence triggered by the [`#verify_image_dimensions` method](https://gist.github.com/jdickey/8eaf646fbd754408c6d9#file-avatar_validator_v1-rb-L114-L119), `Assignment Branch Condition size for verify_image_dimensions is too high. [16.16/15]` is one I have learned to take seriously. RuboCop's default trigger value of 15 is in fact higher than what you should see if, say, you were following [Sandi Metz' rules](https://robots.thoughtbot.com/sandi-metz-rules-for-developers).

### Coming Soon

A new iteration of the rewrite will soon address these problems, and produce code that is neither a [straw man](https://en.wikipedia.org/wiki/Straw_man) to provide a convenient excuse to switch languages, nor code that would budge the WTF-O-Meter&reg; at your team's code review.

### The First Draft

<script src="https://gist.github.com/jdickey/8eaf646fbd754408c6d9.js"></script>
