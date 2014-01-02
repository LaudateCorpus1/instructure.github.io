---
layout: post
title: "Faster I18n Backend for Ruby Written in C"
author: "Jon Jensen"
date: 2014-01-07 07:32
comments: true
published: true
categories:
- Ruby
- C
- I18n
---

Every so often, we like to do a hack(fest|stravaganza|nado) here at
Instructure. It's an opportunity for engineers to spend several days
building something cool to improve [Canvas](https://github.com/instructure/canvas-lms).

For our inaugural hackthing back in May, I worked on the problem of having
lots of objects in memory in Ruby 1.9. Ruby uses a mark-and-sweep garbage
collector, so the more Ruby objects you have in memory, the longer each GC
run will take. This can be a significant contributor to slow page loads in
a large Rails app like Canvas.

<!--more-->

At the time, Canvas was localized in 7 languages (we're now up to 15). We
use the excellent [I18n gem](https://github.com/svenfuchs/i18n) with a
[few](https://github.com/instructure/canvas-lms/blob/stable/config/initializers/i18n.rb)
[handy](https://github.com/instructure/canvas-lms/blob/stable/lib/tasks/i18n.rake)
[extensions](https://github.com/instructure/canvas-lms/tree/stable/lib/i18n_extraction)
of our own. While I18n's default in-memory backend is plenty fast, it
comes at the cost of having all of those strings in memory. Every new
language you add or feature you implement makes the GC problem even worse.

Because localization strings should be static for the lifetime of a Rails
process, there is no reason the garbage collector needs to know about them
at all. But we still want to keep them in memory for optimal speed.

Enter [I18nema](https://github.com/instructure/i18nema), a fast I18n
backend that unstops the garbage collector and gets everything running
quickly and smoothly :).

## What Is an I18nema?

At its core, I18nema is a Ruby C extension that moves I18n's translations
out of Ruby-land and into C structs. It also includes a handful of other
optimizations to I18n, leading to some nice all around speedups.

## How Is It Administered?

In your Gemfile, do

```ruby
gem 'i18nema'
```

and then put something like this in an initializer:

```ruby
I18n.backend = I18nema::Backend.new
```

You can still pull in additional I18n features (e.g. `I18n::Backend::Fallbacks`).
Refer to [the README](https://github.com/instructure/i18nema/blob/master/README.md)
for more information.

## How Will It Improve My Quality of Life?

### More Get-Up-and-Go

I18nema loads translations into memory much more quickly—**over 4x!**—
making for much faster Rails startup time. While this is just a one time
hit, it's very noticeable when you're waiting on it (e.g. console, specs).
In Canvas, I18nema brings it down to a little over a second (from almost
6).

### Minimal Blockage

Because there are fewer Ruby objects, the periodic GC runs are
proportionally faster with I18nema. That means faster page loads for your
users.

How much faster is a question of how many translations you have versus how
many other Ruby objects. Applications that are localized in more languages
should see a bigger boost (since the translations make up a bigger share
of the original ObjectSpace).

For example, Canvas is translated into fifteen languages, and I18nema
reduces both (startup) ObjectSpace and GC runtime by **about 15%**. As more
languages are added, that number should only increase.

I18nema also moves I18n's normalized_key_cache into C structs. This key
cache grows over time (it eventually holds a key/value for every
translation key used in the app), so that's another area where I18nema is
nicer on ObjectSpace than vanilla I18n.

### More Pep in Your Step

I18nema speeds up `translate` calls, getting the right text to your users
more quickly.

Simple lookups (i.e. no options or interpolation) take a bit **over 15%** less
time.

Lookups with options see slightly bigger gains (**over 20%** less time), in
part due to some speedups on the Ruby side of things (I18n uses
`Hash#except`, which is quite slow when you have a long list of
arguments).

## Is I18nema Right for Me?

I18nema is not for everyone.

If you're still on Ruby 1.8, you should not use I18nema. Although I18nema
has been tested on Ruby 1.9, 2.0, and 2.1, it has only been benchmarked
on 1.9. You may find it less effective on 2.1, due to its [dramatic GC improvements](http://tmm1.net/ruby21-rgengc/).

When using I18nema, you should make sure that your translation files are
UTF-8. I18nema cannot be used with `.rb` translation files (only `.yml`)

Refer to [the fine print](https://github.com/instructure/i18nema/blob/master/README.md)
for more information and benchmarks.
