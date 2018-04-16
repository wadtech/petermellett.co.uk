---
title: Injectable Angular
layout: post
image: /assets/images/clint-adair-68588-unsplash.jpg
headerImage: true
tag:
- Angular
- Asset Delivery
category: blog
author: pete
description: Supplying a third party with JavaScript code they can inject in their site is rife with pitfalls. Here's a couple I learned about Angular. Photo by Clint Adair on Unsplash
---

[Photo by Clint Adair on Unsplash](https://unsplash.com/photos/BW0vK-FA3eg)

For a recent project I needed to be able to deliver an angular application as a stand-alone widget that could be dropped in to an existing website.

The danger here is that the third party website could be using any number of frameworks, including Angular, that may interfere with the bootstrapping of the code.

In this particular example the third party was using requirejs in their front-end code and the angular libraries are all module-aware.

This caused the application to attempt to load itself through require when in fact it did not need to do so. The application code was not expecting this and anything that would normally be bound to window - `angular` in particular - was not present at runtime.

This caused some headache until a solution presented itself.

Wrapping the application in an `iife` to give it its own scope while redefining `define` so that for the parse and execute time of the code it would not be present.

A pre and post snippet files were added to the build that opened and subsequently closed the iife were added to the gulp build on the project.

This meant that code my colleagues and I had integrated elsewhere did not need to change its knowledge of the widget in order to continue operating correctly, but the new sites that *would* be affected by angular would be safe from intefering with, or being interfered by the widget.

A similar approach was used for sites that pre-included jQuery, which is used to enable the fuller jQuery features over jqLite in Angular 1.x.

pre.js is the first file to be concatenated during build. It wraps all the code in an iife and reassigns `define` so it can't be picked up by any of the application's modules.

<script src="https://gist.github.com/wadtech/17dded6c64e44e4cdcb3ecfd4b42a8d2.js?file=pre.js"></script>

post.js is the last file to be concatenated in the build. It re-assigns the original `define` to where it was and closes the iife.

<script src="https://gist.github.com/wadtech/17dded6c64e44e4cdcb3ecfd4b42a8d2.js?file=post.js"></script>

Additionally I considered another problem:

**What if angular has already been bootstrapped?**

In this situation angular will refuse to be included again with `WARNING: Tried to Load Angular More Than Once` which may (or may not) be a problem.

For this eventuality I decided that a version check should suffice to ensure that the detected version was equivalent to the bundled version. In that case I could use the existing angular version and not bother to attempt to run the bundled one.

In addition a check to ensure that, if the booted version of angular is new enough, it has been loaded with jQuery instead of jqLite. If not then `angular.element` can be extended.

pre-angular.js is added before the main angular library is included. This way it can detect an already-bootstrapped angular and take evasive action. The widget has a hard minimum supported version number so it has to fail if this cannot be met and a new copy of angular cannot be loaded to run it.

<script src="https://gist.github.com/wadtech/17dded6c64e44e4cdcb3ecfd4b42a8d2.js?file=pre-angular.js"></script>

post-angular.js then comes after angular.js itself. It simply closes the iife from pre-angular but does not do any cleanup of its own.

<script src="https://gist.github.com/wadtech/17dded6c64e44e4cdcb3ecfd4b42a8d2.js?file=post-angular.js"></script>

For a more visual guide. Here's how the logical blocks end up post-build:

![Diagram representing the above text](/assets/images/angular-wrapped.svg){: .centered-image}

*note: I am no graphic designer!*

At last the build provides a distributable, injectable application that is isolated from it's runtime context without resorting to iframes! Phew!

I've got some years' experience with Angular now, and this is the first time I've not had any control over the environment- beyond the browser that is!
