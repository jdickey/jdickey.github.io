# jdickey.github.io

Theme based on [`dbyll`](http://dbtek.github.io/dbyll/) by [İsmail Demirbilek](https://github.com/dbtek). See the theme-project link for details.

# Setup

## Prerequisites

1. A currently-supported version of MRI Ruby (as of late March 2017, the current releases of the 2.1.*x* through 2.4.*x* branches);
2. The [`rbenv`](https://github.com/rbenv/rbenv) Ruby environment manager;
3. The [`gemset`](https://github.com/jf/rbenv-gemset) plugin for `rbenv`. The [`ruby-build`](https://github.com/rbenv/ruby-build) plugin is **recommended** but not required.

## Getting Set Up

Simply run the `bin/setup` script in this repository. That will

1. create an `rbenv` Gemset to help keep the system-wide Gem repository pristine;
2. install the Gems that are directly and indirectly depended on. Direct dependency versions are fully specified as of the last time the setup script was updated; and
3. run `bundle install` to bundle Jekyll *locally,* using the Gems that were just installed into the Gemset.

# Blogging Workflows

See the [Jekyll docs](http://jekyllrb.com/docs/home/).

# License

## Source Code

The **supporting source code of this blog, as well as of the Jekyll and Dbyll Gems on which it depends,** are licensed under the [MIT](http://opensource.org/licenses/MIT) license.

## Blog Content

<a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/"><img alt="Creative Commons Licence" style="border-width:0" src="https://i.creativecommons.org/l/by-sa/4.0/88x31.png" /></a><br />Authored blog content is Copyright &copy; 2005-2017 by Jeff Dickey, and is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/">Creative Commons Attribution-ShareAlike 4.0 International License</a>.
