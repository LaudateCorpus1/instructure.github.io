---
layout: post
title: "Approaching Accessibility Testing"
author: Hannah Bottalla
date: 2014-06-17 16:37
comments: true
categories:
- accessibility
---

When I first started learning about accessibility, I had no idea what I was doing. I am a sighted person, and didn't know if what I was doing was correct. I fed a URL to an online accessibility evaluator, which then told me what elements were missing on the page (ie: an image did not have an alt tag). It wasn't until I met a blind person that I started to understand how people with visual impairments actually use the web. Over the years, I picked up a lot of insight on how to conduct accessibility testing, which I want to share with you.

 <!--more-->

Before I dive into the "tips and tricks", I have to dispell a few myths:

## Accessibility Myths

### Accessibility only affects blind people

Blind people are not the only ones affected by inaccessible websites; they  affect people with impaired motorskills, colorblindness, the elderly, amongst others. Accessibility testing is necessary because we as human beings should all have access to the web. If you and/or your company is not doing accessibility testing, bring it up at the next meeting.

### Assistive technology is super cheap

Many people rely on screenreading software and/or a braille display, and it can get very expensive. The license for JAWS, a popular screenreading software, costs anywhere from $895 USD to $1,095 USD, and another couple hundred dollars if you want to buy the upgrade that comes out once each year. Choosing to skip an upgrade may mean that the latest version of your web browser may not work well, or at all. A braille display, which is a device that displays braille using pins that move up and down (a necessity if you are deafblind), can cost thousands of dollars. And this is only for your computer - you have to pay more money on top of that for your mobile devices. Imagine if you had to pay for all that, and it only worked part of the time. It's like buying a car that only runs on Tuesdays.

### Assistive technology is always up to date

Technology changes so fast that, when we buy the newest smartphone, it becomes outdated months later. In the same way, assistive technology is often behind new web technologies. Screenreaders can't keep up with the fast pace of web development, which includes the new javascript every other week.

### All screenreaders are the same, I only have to test with one

Like browsers, not all screenreading software is alike. Mac OSX has a built in screenreader called "VoiceOver", which you can enable in the Accessibility section of your System Preferences. It also includes a comprehensive guided tutorial. For Windows, two popular options are JAWS and NVDA. NVDA is free and open source, and is available in many languages. For Linux users, Orca, which is also free and open source, is a great option. The screenreader output can greatly vary; when you navigate to a "Close" button on a modal window, you might hear "Button Close Button" from one, but hear "Close Button" from the other. Talk with your users and see what they are using, and if your budget allows, test with as many screenreaders as you can. At Instructure, we test Canvas using the latest version of VoiceOver and JAWS, and are currently evaluating NVDA.

### Accessibility testing is the worst kind of testing

Testing of any kind can be painful, whether it be writing unit tests or doing user experience testing. Accessibility testing is also on that list, but realize the more you test, the more aware you will be when writing your code. You will start to look out for things here and there because you had to spend twenty minutes trying to navigate to a submenu in a previous commit.

### I can just throw my website at an accesibility evaluator and call it good

Evaluators only look at the structure of your site, it doesn't emulate the user experience. We all know that, sometimes, what looks good on paper doesn't work out in real life. The same goes for an evaluator. It will tell you if your headers are out of order, but it won't tell you if your navigation is terrible because you are inconsistent with what keypress you are using for a certain element. You need actual people to test your site out. Which brings me to my next point...

### Only blind people can do accessibility testing

Anyone who learns the software and/or hardware can do accessibility testing. At Instructure, we have our Quality Assurance (QA) engineers do a lot of the testing, and all of them are sighted. If you have questions and would like to ask someone who is blind, there are a lot of resources out there. For those in the education sector, many universities have a disability resource center - reach out to the coordinator there, and ask for permission to get feedback. Your city and/or state may also have a dedicated center. We also have the Internet for a reason, use it!

## Where to start and how to test for accessibility

If you haven't already, run your site through an evaluator. There are many free accessibility evaluators online, where you feed the URL into an evaluator, and it tells you how accessible the page is. Two popular evaluators are [WAVE](http://wave.webaim.org/ "WAVE Evaluator") and [FAE](http://fae.cita.uiuc.edu/ "FAE Evaluator"). If you use FAE, note that it is evaluating in accordance with the Illlinois Information Technology Accessibility Act. Not all evaluators are running against the same guidelines, so find out what it is evaluating against. Many evaluators will use the [W3C Web Content Accessibility Guidelines](http://www.w3.org/TR/WCAG/ "W3C Guidelines"). The evaluator will show you any errors and provide feedback. Do you have redudant title text? Are your forms labeled? These are items that will be pointed out to you, which you can easily fix.

Once you have your baseline, and fixed the structural elements, it's time to move on to testing with real people. Ask for feedback from your users and see what needs the most attention, and start there. Here are a few of the things we do:

1) On each page we check to see if there are headers, a main landmark, and a complementary landmark (if applicable). We also check for to see if there are any lists, forms, and tables on the page, and use the appropriate screenreader key. Many users navigate using these elements to quickly jump to content on a page. It's hard to scroll through an entire page, so make sure headers and landmarks are available for easy access.

2) We try to use the keyboard as much as possible. Keyboard navigation is critical to many users, especially those with physical disabilities. Not everyone can use a trackpad for gestures, but most everyone has a keyboard.

3) We heavily reference the keyboard layout and shortcuts provided by the screenreader we are using. When I first started accessibility testing, I used the tab key to go through the page. I didn't realize there were screenreader users and keyboard only users. If you are unsure of which keys correspond with which action, look up the keyboard layout for your respective screenreader.

4) We look to see if our elements have consistency; not all of the elements on our pages share the same keypress, even if they look the same visually (which, we are working on). For example, don't have a "Submit" button that reads as a link on one page, and as a button on another. Make sure your elements are all the same under the hood.

5) With links and buttons, we always make sure a user can read the link/button text and execute the appropriate action. Sometimes it will only read the link text and it won't open the link. Don't assume something works just because you can navigate to it.

6) We look at a page from top to bottom, then outline all the elements in a spreadsheet. For example, under "main body navigation", I would have "Reads Assignment Title", "Reads Published Button", and etc. Organizing and writing out all the elements and the location make it much easier for other people to read and follow your work.

What we do at Instructure isn't limited to this list; this is merely a springboard for you to start. We also conduct accessibility audits, and actively work with our clients and other vendors. Instructure is also fortunate to have a dedicated accessibility team in addition to engineers who are passionate about accessibility on each individual core team of Canvas.

Tackling accessibility testing may seem like a daunting task, but start with the steps outlined above. Your changes will start out small, and it may seem insignificant, but realize that every effort you make changes the web to be more accesible. Soon, you will be the one teaching others about what you've learned, and how to make an accessible product. We at Canvas hope that one day, we'll be reading a blog post from you.
