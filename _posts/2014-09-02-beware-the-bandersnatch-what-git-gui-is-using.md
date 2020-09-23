---
layout: post
title: 'Beware the Bandersnatch: What Git GUI Is Using YOU?'
updated: 2015-02-16 02:03:00 SGT
tags: [tumblr, git, collaboration, development, code, software, rant]
categories: [not yet categorised]
comments: true
fullview: false
---

I routinely have open at least one of two Git GUI interfaces; [GitHub for Mac](https://mac.github.com) (aka GH:Mac) and [Atlassian SourceTree]http://www.sourcetreeapp.com). They each have their uses; GitHub for Mac is drop-dead simple, deliberately eschewing the level of detailed features of SourceTree. (For example, I don't think it's possible to cherry-pick commits using GH:Mac; in SourceTree, it's very straightforward.)

Another *major* gotcha (gitcha?) in GH:Mac is when you've added an updated file to staging, and then made further changes to that same file.  Let's say you wind up with something like this:

#### In the Terminal window:

```
$ git status
On branch issue-89
Changes to be committed:
  (use "git reset HEAD &lt;file&gt;..." to unstage)

    new file:   app/services/post_update_validator.rb
    new file:   spec/services/post_update_validator_spec.rb

Changes not staged for commit:
  (use "git add &lt;file&gt;..." to update what will be committed)
  (use "git checkout -- &lt;file&gt;..." to discard changes in working directory)

    modified:   app/services/post_update_validator.rb
    modified:   spec/services/post_update_validator_spec.rb

$
```

#### In GitHub for Mac

<img src="https://31.media.tumblr.com/7e17c44dc6b0a9507c20b48bbb682284/tumblr_inline_nb9i9fMsgS1rdht63.png" alt="GitHub for Mac screenshot" style="width: 100%;"/>

Note that the new files, as staged, are shown, but the changes since staging are not.

#### In SourceTree

<img src="https://31.media.tumblr.com/ce936bd6bd3d5320157980455dc7e86e/tumblr_inline_nb9il68a9l1rdht63.png" alt="SourceTree for Mac screenshot" style="width: 100%;"/>

Each of the two files is listed twice, as was the case at the command line. With GH:Mac, if I were to make the displayed commit, I would *then* be shown the unstaged versions of the files. Likely not what I want.

YHBW.
