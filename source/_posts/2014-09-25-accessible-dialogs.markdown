---
layout: post
title: "Breaking the Rules: Accessible Dialog Components"
date: 2014-09-25 9:00
comments: true
author: Ryan Florence
categories:
- accessibility
- React
---

Turns out, `role="dialog"` isn't your friend.

## Cursor Modes

When you first start building accessible websites, you learn quickly
that there are different cursor modes when using Windows screen readers,
[Léonie Watson explains them well here][modes].

Certain element roles, like `application` and `dialog` will change the
mode of the cursor. Very often this is not what you want. Yes, It does
allow the developer to implement convenient keyboard events for the
component, but it disables the normal keyboard navigation shortcuts for
screen reader users. For sighted users, that would be a lot like opening
your page up in Internet Explorer 5.5 on a mac.

<!--more-->

## The Problem With ARIA Dialogs

Unlike other components, dialogs and modals usually just contain regular
content like the rest of the website. They don't need special keyboard
navigation implementations, except for handling the escape key to
request to close it.

When you use `role="dialog"` the forms/focus cursor mode is activated
and the screen reader user can't interact with the content in the dialog
like they can with the rest of the website. In most cases, this results
in much of the content in the dialog becoming completely inaccessible to
them.

## The Solution

The solution is to "hide" everything in the application--except the
dialog--from the screen reader. This way the dialog can be navigated
normally and the rest of the app is inaccessible until the dialog closes.

In a nutshell:

1. Don't use `role="dialog"`, to avoid switching cursor modes.
2. When a dialog opens, set `aria-hidden="true"` on the rest of the
   page and focus the content of the modal.
3. When a dialog closes, remove the `aria-hidden` attribute and return
   focus to the element that initiated it.

The easiest thing to do is simply wrap your entire website in a
containing element and then just manage `aria-hidden` on that one
element instead of many.

It would end up looking something like this:

```html
<div id="app"> ... </div>
<div class="dialog"> ... </div>
```

And when the dialog is open:

```html
<div id="app" aria-hidden="true"> ... </div>
<div class="dialog"> ... </div>
```

## But That's Not The Spec!

The spec probably needs to change to support dialogs where forms mode
doesn't make sense. We implemented this in order to get Canvas certified
as an accessible web application. The web moves fast and the specs don't
always keep up.

Until the specs catch up, and all the parties involved implement them,
we choose to focus on the experience of our users (oh, right, and
[getting certified][webaim]).


{% render_partial _authors/ryanf.html %}

  [modes]:http://tink.co.uk/2014/09/understanding-screen-reader-interaction-modes/
  [webaim]:http://webaim.org/services/certification/
