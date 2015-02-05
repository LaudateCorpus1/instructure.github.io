---
layout: post
title: "Just Follow the Types"
date: 2015-02-05 14:11
comments: true
author: Derrick Hathaway
categories: 
- iOS
- Swift
- Functional Programming
---
As Mac and iOS craftsmen we've been given a new tool. In some ways it looks a lot like the old tool, a tool we've been using for many years now. In many ways it is very different. This tool is, of course, the Swift programming language.

A strict type system is one thing that differentiates Swift from Objective-C.<!-- more --> This type system, along with other related features like generic types and custom operators, has started a movement among developers to adopt some techniques that Swift's predecessor made difficult or impossible. Some of these language features and techniques come from the language Haskell, one of the many languages Chris Lattner [drew inspiration from in creating Swift](http://nondot.org/sabre/).

{% img right half https://farm3.static.flickr.com/2658/4293485922_7474ef04ce.jpg 375 500 "An old rusty tool." "An old rusty tool." %}

As I try to learn and adopt these new techniques I feel a bit like a woodworker who's been given a new tool, one with new features that I don't completely understand. One trick I've learned is that with such a strong type system comes certain affordances.

Let's look at an example.

`Optional` has a method called `map`. Now if you've used `map` functions that are common for collection types you might be able to intuit what this map function does. An `Optional` isn't an array, however, so it might still be a little confusing. But if you take a moment to inspect the type of `map` you will see the trail of breadcrumbs.
```
func map<U>(f: (T) -> U) -> U?
```

Here we can see that map takes a single function from `(T)->U` as a parameter and returns an `U?` (which is just nice syntax for `Optional<U>`) as a result. Remember that `T` is the generic parameter type of the `Optional` on which you are calling `map`. This effectively allows us to take an object wrapped up in an `Optional` [context](http://adit.io/posts/2013-04-17-functors,_applicatives,_and_monads_in_pictures.html) and apply a function to it resulting in an `Optional` object of the given function's return type.

Higher order functions like `map` are common in functional programming languages. For example, Haskell defines a function `fmap` that performs the same operation as `Optional.map`. Haskell programmers use this function so often that they've even given it an operator: `<$>`, and many Swift programmers are [following suit](https://github.com/typelift/swiftz) (though, due to some limitations on special characters in Swift, most implementations seem to have settled on `<^>` for that paricular operation). 

When confronted with new symbols and [unfamiliar techniques for solving problems](http://chris.eidhof.nl/posts/json-parsing-in-swift.html) in a strictly typed language like Swift, I find it helps to follow the types.


Photo by [Seán A. O'Hara](https://www.flickr.com/photos/hortulus_aptus/). Licensed under [CC BY 2.0](https://creativecommons.org/licenses/by/2.0/).

{% render_partial _authors/derrick.html %}


