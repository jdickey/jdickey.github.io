---
layout: post
title: "Yak Shaving Sometimes Needs Chainsaws"
description: "After far too long in the wilderness, a project is subjected to reality"
tags: [development, guaranteed failure, agility, deployment, language, yak shaving, effectiveness]
categories: [Development]
comments: true
fullview: false

---

Metaphorically, of course; no actual animals (yaks, infestors, or executives) were harmed in the creation of this blog post. Thanks to [Kelly Sutton](https://kellysutton.com), whose posts are nearly always good reading, and whose post [_A Framework for Making Technology-Product Decisions_](https://kellysutton.com/2019/10/23/framework-product-and-technology.html) in particular got me thinking about several herds of yaks that have dominated one of our product efforts since its inception.

In terms of [the framework](https://kellysutton.com/2019/10/23/framework-product-and-technology.html#the-framework), this project (hereafter, The Product) has spent its entire existence alternating between *Executing* and *Replacing*, in the view of its most senior stakeholder, and firmly anchored *Out on a Limb* in reality. Part of this has been due to the immaturity and questionable interoperability of the available/known tools with The Vision™ for The Product, which was not helped by both The Visionary and the "lead" (sole) developer being far more confident in Web 1.0 back-end/static-front-end dichotomy when what was actually needed essentially requires a dynamic front end and a slimmer, probably API-encapsulated back end. Being focussed on maximising hours of effort to utterly, ridiculously, *catastrophically* destructive levels caused The Product to fail, although nobody associated with it at the time recognised that. For well over three years, the developer was putting in 90+-hour-weeks doing epic battle with the most immediately-obvious blocking tactical issue; the long-promised help, including from the "technical CEO" that was promised in exchange for that developer not walking out six months into The Burnout Inferno, never arrived. (Sili Valley Syndrome dies *hard*, no matter the geographical distance between the patient and coastal California.)

Worse still, all this effort and time failed to yield a deployed, demoable system. Instead, a dozen or so [bounded contexts](https://en.wikipedia.org/wiki/Domain-driven_design#Bounded_context) forming the _basis_ of demoable features were developed, usually multiple times. (This is as close as The Product got to *Replacing*, in Sutton's framework, except that the framework use implies replacing bits of an existing, working system.)

### Lessons Painfully Relearned

**Lesson One:** *Never* attempt a would-be commercial project with a team below the minimum that can be reasonably expected to achieve the mission within the allotted time with 30 hours or so of actual development work per developer per week.

If you're working 50 hours a week in a creative role, let alone 90+, you're making *far less* real progress toward your goal than if you're putting in a solid, well-rested, collaborative ~30-35. This has been known to industry and academia for nearly a century now; ignoring it should be treated as any other form of management malfeasance.

**Lesson Two:** If you can't deploy The Product, no matter how incomplete it is, right *now*, then how do you know you'll *ever* be able to?

> [It] **doesn't matter** how great your idea is if you can't build and deploy it to users. &mdash; [Nikki Lee](https://twitter.com/nkkl/) on Twitter; [January, 2018](https://twitter.com/nkkl/status/952995627842994176).

Yes, emphasis added; Twitter doesn't do Markdown. Still, something every PM, dev, and manager in a development org and its C-suite needs to get through their individual and collective skulls as regularly as needed for it to stick.

One of the greatest trends in software for the last decade or so has been [DevOps](https://en.wikipedia.org/wiki/DevOps): the combination of software development with cross-function IT operations and deployment to shorten delivery cycles and improved quality, since bugs can be found and fixed more quickly, and sub-par approaches re-evaluated *in the context of* working code. Also, by automating deployment tasks, you empower your team to deploy "whenever we're ready to put something new up there", rather than waiting for periodic, scheduled "big bang" deploys (which carry higher risk).

*Even if all you have so far is "Hello, World", prove that you can deploy __that__ and then flesh out your Obviously Valuable Software.* A sure sign of Sili Valley Syndrome is talk of software patents and other such frippery before you can even demonstrate that you have anything *worth* protecting.

**Lesson Two (Corollary):** If the automated testing and deploment of The Product (from Lesson Two, above) requires more than one action to be taken manually, then its deployment *will* eventually fail. You can, in a pinch, *start out with* a fully-documented process involving multiple manual actions. However, an issue with the highest priority your issue-tracking system supports **should/must** be opened *and remain open* until you're down to that one manual action; ideally something as idiot-proof as clicking a button. If you make two consecutive deployments without any progress towards fully automating the test and deployment processes, consider *that* a serious defect in your process.

**Lesson Three:** Following on from the above, use the most mature, well-supported (aka [*boring*](https://tqdev.com/2018-the-boring-software-manifesto)) tools for development and deployment that can achieve your purpose.

Yes, this means that your deployment capabilities should ~~influence~~filter development choices such as language and tech stack. You may (think you) know what you *want* your development and deployment stacks to look like in The Glorious Future™ well enough to comfortably invest time and resources in getting yourself closer to that ideal; unless you're *confident* you can get from here to there in a reasonably-fixed amount of time *based on both research and experience*, don't turn that investment into a blocking, functionally bottomless time sink.

### Ready, Fire...Oh, Go Ahead and Aim

Looking back at [the framework](https://kellysutton.com/2019/10/23/framework-product-and-technology.html#the-framework) with which this post started, how best to get from here to there with respect to The Product?

#### If you're not doing CI-automated testing, fix that first

If you're not testing (close enough to everything), you *can't* know what you have, and refactoring is much harder *and riskier* than it needs to be. If you're not reliably testing on every commit by default, then sooner or later, serious, or merely embarrassing, defects will creep in, not to be caught before the Big Demo for the people you only get one shot at impressing. Don't take that shot with a footgun.

#### Take a cue from [Tattoo](https://en.wikipedia.org/wiki/Fantasy_Island):

> Deploy, boss, deploy!

Even if that deployment is completely manual at first, document it (and recovery from failures experienced along the way) and incrementally automate it as time goes on. You may have goals of single-button automation using [Ansible](https://www.ansible.com/), [Kubernetes](https://kubernetes.io/), or other such tools, but give Braintree's [Runbook](https://github.com/braintree/runbook) a look as a "baby step" along the way.

Remember, even if you don't have a "real app" yet, but you have some idea of what the first few versions of your "real app" might look like (language, database, other services, etc), make it deploy or go home.

#### Re-evaluate how The Product uses, or attempts to use, the latest Shiny New (or New-to-You) Tools.

Do this regardless of overall/longer-term expected value to The Product.

For example, Docker for staging/production deployment is *highly likely* to be a Good Thing, as it virtually guarantees repeatability, but can you adequately and reliably deploy without it? Is there *sufficient benefit* to build a set of Docker images before CI testing against them, or can you comfortably defer building those images until after CI testing blesses their contents?

Another consideration is team size. Docker and Kubernetes have been shown both in direct experience and in the literature to be highly challenging to individuals or small teams. On the one hand, learning a stack such as Docker, Kubernetes, and Helm is daunting, to say the least, for small teams lacking experience with the stack. On the other hand, "buying into" a provider platform such as AWS or Azure can feel risky because the team don't fully understand the stack well enough to assure stakeholders that they understand the risks and tradeoffs involved in such platforms &mdash; especially when such platforms often feel designed to sell consultant billable hours.

Hence, deployment and DevOps in general feel risky and uncertain, often leading to the preservation of manual processes far beyond what is strictly necessary, risking brittle, inefficient deployments.

#### Iterate early and frequently, keeping feature or bugfix branches as short in duration as practical

If your feature branch lasts more than a few days (let alone a few *months*, as has too often been the case), you're Doing It Wrong. Exploratory R&D is a necessary thing; just don't confuse it with feature development. The *whole point* of agile development is to demonstrate value early, incrementally working towards the highest-value "next feature" that can be deployed and demonstrated. (Remember, if you can't deploy, it's *awfully* hard to credibly demonstrate.)

#### Use a coherent, established `git` branching model.

Vincent Driessen's [original model](https://nvie.com/posts/a-successful-git-branching-model/) has gained significant use over the last decade, largely because it provides an understandable, easy-to-use structure differentiating progress done between formal releases (on the `develop` branch) from those releases (on `master`) which, among other things, provide a near-last-ditch fallback to get to a "known good" point, after which work along `develop` can proceed anew. It also importantly draws distinction between branches dedicated to new features, `release` branches, where work needed to comply with shop standards for formal releases, such as updating documentation, are carried out (along with possible minor bug fixes but no new features), and `hotfix` branches from `master` to provide an emergency fix to a critical problem; `hotfix` branches are merged back into *both* `master` and an existing release branch (or `develop` if no such branch exists).

We presently use Peter van der Does' [`gitflow-avh` variant](https://github.com/petervanderdoes/gitflow-avh), supplemented with manual branching for `research` ("How do we use this tool or component to make development easier or more capable?) and `wip` ("This feature may take a while to figure out a good way to implement properly; let's have the branch indicate this"). You can use van der Does' variant as is; Driessen's [original](https://nvie.com/posts/a-successful-git-branching-model/); or any of the [thousands of forks](https://github.com/nvie/gitflow/network/members) of Driessen's implementation; whatever adequately serves your needs.

Notably, van der Does' [`gitflow-avh`](https://github.com/petervanderdoes/gitflow-avh) fork reifies `support` branches, which (among other uses) are a handy place to have more formalised/non-emergency rework of `hotfix` releases.

Bug fixes are treated by both Driessen's and van der Does' models as though they were features; e.g., a branch such as `feature-1145-fix-broken-twitter-integration` references the hypothetical GitHub Issue #1145 for the repo, where the "broken Twitter integration" bug discussion is recorded.

*However*, in the last couple of years, [GitHub flow](https://guides.github.com/introduction/flow/) and [GitLab flow](https://docs.gitlab.com/ee/topics/gitlab_flow.html#introduction-to-gitlab-flow) have gained significant traction, largely in reaction to the complexity of Driessen's (and variants') flow models. While both GitHub and GitLab have a greater degree of cultural/institutional support for their associated flow models, neither model is intrinsically tied to its namesake service. I recommend at least browsing through the docs for each model and looking for projects that use them before committing to a moswl for your project. Shifting from the Driessen-style models to either GitLab or GitHub flow initially *looks* like it could be tricky, especially for a project with a large number of feature/exploratory branches. It *should* be possible to make the transition simply by ensuring `develop` is up-to-date, with completed feature/etc branches merged, and then merge `develop` into `master` and proceed with your new flow model from there. Less risky, obviously, would simply be to experiment with a small greenfield project. (Isn't that always the case? 🤓)

#### Use different virtual hosts or feature flags to deploy and test one or more branches

[Feature flags](https://featureflags.io/ruby-feature-flags/) and/or different vhosts support deployment and testing of one or more branches in addition to the currently known-working "main" branch. This lets the team hide new code from all but selected users (using feature flags) or physically separate different deployments (using vhosts).

This is part of what's needed to evolve past initial "production" or release-candidate status, and proving the capability, even trivially, early on will yield sufficient rewards (confidence, control, etc) as The Product becomes more featureful to justify a moderate amount of invested time early on, when The Product is most trivially subject to differentiation.

"Why bother", you might reasonably ask. "After all, up to now, we've only worked on one feature at a time". While true, that's also *not* a good thing, especially when multiple new or reworked features have been identified. When work on one feature stalls as mental fatigue sets in, switching off to work on something else, *independently of* the first feature with regard to development, test, CI, and deployment, can keep things moving along even with the historically highly sub-minimal teams doing the actual work.

### Where to Go From Here

Yes, code currently exists for The Product, in an absolutely vestigial, low-value-added state.

**First**, is the existing code an asset or an artefact of learning that can best be set aside and replaced? (The [Concorde effect](https://en.wikipedia.org/wiki/Sunk_cost#Concorde_effect), or sunk-cost fallacy, is not limited to multi-billion-euro hardware efforts.)

**Second**, obviously, perform any [_Ready, Fire&hellip;_](#ready-fireoh-go-ahead-and-aim) tasks that haven't yet been done. (If the outcome of the first step is "cut and redo", then of course *none* of them will have been yet done for this iteration.)

**Third**, list out the known tasks yet to be done, using a tool like [Trello](https://trello.com/), [GitHub project boards](https://help.github.com/en/github/managing-your-work-on-github/about-project-boards), or other such Kanban-type task organiser to list, describe, and prioritise tasks. This will be a "living document" throughout development.

**Fourth**, build a simplest-possible app and get its automated CI testing working and documented. This is a good thing *even if* you have an existing CI setup for an earlier app; how confident are you that you can hand things off to a newly-onboarded dev or to a quasi-technical senior stakeholder and they'll be able to make it Just Work? (*Hint:* This is a great use for [Runbook](https://github.com/braintree/runbook)).

One idea worth considering at this point, if not at "First", above, is the idea of "peak Ruby", which several community thought leaders have placed as circa 2009. This can be interpreted either as "Ruby is a mature, boring language with a mature, stable ecosystem" *or* as "Important segments of what was the circa-2009 Ruby community have moved on to new systems (notably Elixir, Rust, Go and, to a lesser degree, Node.js) for Reasons." Those "Reasons" include, but are not limited to,

* the relative ease of deploying and maintaining container-based applications using a single, compiled artefact (see, e.g., [Docker multi-stage builds](https://docs.docker.com/develop/develop-images/multistage-build/) whicn generally produce smaller final images);
* the fact that Ruby is a single-threaded system unless relatively elaborate steps are taken to work around the [Global Interpreter Lock](https://ablogaboutcode.com/2012/02/06/the-ruby-global-interpreter-lock)), running on ubiquitously multi-core if not multi-processor systems;
* the lack of a native, fluent static-type system in Ruby which is highly useful both for catching bugs prior to runtime, and for expressing data types and interfaces using The Product's [Ubiquitous Language(s)](https://www.martinfowler.com/bliki/UbiquitousLanguage.html), rather than native types, to reduce ambiguity in development and maintenance;
* Ruby may be seen as a reasonably "boring" language now compared to its volatility from 2003-2013 (when Versions 1.8.*x*.*y* and then 1.9.*v*.*w* were current), but breaking changes were introduced again in the 2.7 release (2019) and are planned for Version 3.0 in late 2020. Not all tools in the ecosystem, notably several widely-used Gems, have yet cleanly supported Ruby 2.7. Support for 3.0, presently in prerelease form, is highly uneven;
* and so on.

Recall "Lesson Three", above, and its observation that "your deployment capabilities should filter development choices" rather than the other way around. (This in itself is responsible for *months* of delays in The Product.)

**Fifth** and subsequently, adopt a fixed-length "sprint" development methodology. A new or updated feature should be deployed and demoable every *x* calendar weeks, where *x* is commonly two weeks (rarely, four). Fully implementing a non-trivial feature *may* take multiple sprints; however, the different between The Product at the end of a sprint and its immediately-preceding sprint must be testable and demoable. (Feature flags are handy here, too, for easily allowing sequential or side-by-side demonstrations of the new code.) This will *often* require cutting down the focus of an individual sprint-in-progress from, say, "Implement comparison of different versions of a document, showing changes made and their authors", to "Support presentation of the editing history of a document", followed by "Support display of a selected version of a document, noting the changes since its immediately-preceding version", and then "Show differences between two arbitrary versions of a document, defaulting to the initial and latest versions, including detailed display of its changes and authors over time."

Adopting this or a functionally equivalent development and deployment process will make The Product much less likely to languish for months (or longer) in "the wilderness", wandering aimlessly in search of a hoped-for but poorly-understood sequence of destinations. This, plus a more humane, realistic work-life balance, would significantly increase The Product's odds of success after so mind-bendingly long.

Staffing up to competent levels wouldn't hurt, either.
