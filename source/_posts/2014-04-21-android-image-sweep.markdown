---
layout: post
title: "ImageSweep : drawable management for Android"
author: "Josh Ruesch"
date: 2014-04-21 07:29
comments: true
categories:
- android
- proguard
- opensource
- open-source
---

Software engineering is full of trade-offs: speed/memory usage, features/bugs, etc.
The list goes on and on. The Android team here at Instructure found an interesting trade-off: developer-time versus application size.
As we get new icons/images from our UI/UX team, we have two choices: bundle all of them into the application or add them as they're needed in our project.
The trade-off becomes relatively straight-forward: either we spend time constantly adding images to our project or the user has to deal with their size when they install our application.

At a high-level, the solution is simple: find all the unused drawables in the project and remove them when you build for release.
Historically that's been a tedious and error-prone process, but not anymore.
I'm proud to announce a new open-source script from Instructure: [ImageSweep](https://github.com/instructure/android-ImageSweep).
It will run through every file in your project and check for references to drawable resources using regular expressions to check for instances of *R.drawable.* and *@drawable/*.
The script will then iterate through the resource folder and delete ALL unused drawable resources.

It's extremely easy to use. Simply run:

        python ImageSweep.py project_src_directory

where `project_src_directory` is the relative or absolute file-path where your source code lives.
Make sure the chosen directory contains *all* of your source code and AndroidManifest.xml, but none of the libraries you've included. The script auto-determines where the project's resource folder is and libraries can potentially break this detection.

We recently added this script to our release process for [Canvas for Android](https://play.google.com/store/apps/details?id=com.instructure.candroid).
In that project, we were able to delete 2,593 files for a total of 72.37 Mbs freed on disk.
This correlated to a *46%* size decrease on the apk itself and *26%* size decrease on the installed application.

To get started, visit the [project homepage](https://github.com/instructure/android-ImageSweep). Take note of the warnings in the README prior to using the script.
