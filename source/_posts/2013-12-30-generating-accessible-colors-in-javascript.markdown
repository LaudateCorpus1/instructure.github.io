---
layout: post
title: "Generating Accessible Colors in JavaScript"
date: 2013-12-30 14:42
comments: true
categories:
- javascript
- design
- accessibility
author: Braden Anderson
---

We recently released a major upgrade to Canvas's calendar. Although the primary objective was adding an agenda view and other accessibility tools, we also took the opportunity to update its visual design, including the color coding that links events and calendars. We've released this new color code generator as a Bower package at [github/instructure/color-slicer](https://github.com/instructure/color-slicer). Here's why we switched.

<!--more-->

## Objectives

Our old color coding system was pretty simple: we had a list of ten hues, and we assigned them to calendars in order. The drawbacks were that colors would repeat after ten calendars and that the colors used weren't very appealing.

 ![](http://i.imgur.com/6lBEr3V.png)

Another system we considered was [our hash-based color generator](https://github.com/instructure/canvas-lms/blob/stable/app/coffeescripts/util/contextColors.coffee), which creates a color based on an object name or ID. This would avoid repetition, but unlucky users could end up with three slightly different shades of green for their three courses.

For the new system, then, we had three goals:

* Produce colors that look nice and are accessibly readable for text.
* Allow an unlimited number of colors without repetition.
* Keep colors visually distinct, especially for short lists.

## Text colors

The simplest way to generate bright colors would be use HSV and to vary the hue while using 100% saturation and value. For text on a white background, though, this makes yellows almost invisible:

<a href="http://jsfiddle.net/3KX8d/1/"><img src="http://i.imgur.com/kk4ntIE.png" /></a>

Instead, we use the [Lab color space](http://en.wikipedia.org/wiki/Lab_color_space), which adjusts for human sensitivity to green, providing a better measurement of lightness and more evenly distributed hues. This not only looks better, but also helps users with limited vision by meeting [WCAG AA contrast guidelines](http://webaim.org/resources/contrastchecker/).

<a href="http://jsfiddle.net/G9Qmm/1/"><img src="http://i.imgur.com/V5jstLt.png?1" /></a>

## Spacing

An easy way to space the colors would be to divide the hue circle by the number of calendars. We didn't want all of the colors to change whenever a user added a course, though; we wanted to use the same colors for the first three calendars whether there were three or thirty total. To accomplish this, we [progressively divide each gap in half](http://jsfiddle.net/UqSS3/6/): first we use 0°, then 180°, then 90°, then 270°, then 45°, and so on. This way, your first few calendars are always very visually distinct.

Formally, this is essentially an [online](http://en.wikipedia.org/wiki/Online_algorithm) [Thomson problem](http://en.wikipedia.org/wiki/Thomson_problem). We're lucky to only have to solve the one-dimensional version, but the same basic idea could certainly be used to vary hue and value simultaneously if you wanted to generate many distinct colors and didn't care about legibility. The [spiral point heuristic](http://sitemason.vanderbilt.edu/page/hmbADS) would probably work well--we'd love to see someone send us a pull request.

## Results

We're always refining our user experience, but we're happy with the look of our new colors.

 ![](http://i.imgur.com/GLSrwyh.png?1)

We've released [color-slicer](https://github.com/instructure/color-slicer) in UMD format under an MIT license, so it should be easy to add to your projects. I hope you'll find it useful. My favorite part of working at Instructure--better than weekly lunch--is spending most of my time crafting code that we give back to the community. Let us know what you think.
