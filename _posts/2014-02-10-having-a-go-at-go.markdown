---
title: Having a Go at Go[lang]
layout: post
image: /assets/images/hans-peter-gauster-252751-unsplash.jpg
headerImage: true
tag:
- Go
- Programming
- Learning
category: blog
author: pete
description: My early look at the Go programming language. Photo by Hans-Peter Gauster on Unsplash
---

[Photo by Hans-Peter Gauster on Unsplash](https://unsplash.com/photos/3y1zF4hIPCg)

*This is an old post that I had knocking around from an older version of this site. For the sake of history here it is! Content likely to be innacurate and dated.*

I've been intrigued by [Golang][gl] for a little while now. I used to enjoy concurrency and parallelism in Java many moons ago during my student days. I've never had enough motivation to pick up C++ or brush up on Java again, so those skills are something that had, until recently, fallen by the wayside.

Seeing that Go has a simple language specification and a powerful standard library made it a prime candidate for some 'C-like' programming.

Web development focuses on single-threaded request-response type interactions and all the languages I've used in the past few years have been dynamically typed. With this in mind, Go provides two things of interest:

* Static typing
* Concurrency

I like static typing, a lot. Type safety allows me to concentrate on the functional code I wish to write instead of the defensive programming and duck typing tactics that I don't. Even though contrary to that statement, duck typing is a very flexible and valid form of writing Go using interfaces to great effect. That aforementioned safety doesn't stop me from littering my code with `log.Println` calls though - I like verbose console output.

I also like that concurrency is baked in to the language. When I was writing Java a lot during University, multi-threaded code was my favourite to work with, it felt the most like 'proper programming'* in a way. Goroutines make for a very convenient and easy concurrency API, with channels providing safe communication between them. Channels are a first-class type in Go, which allows for even very simple programs to make full use of the powerful features on offer without resorting to standard or third-party libraries.

I've been somewhat hoovering up Go talks and resources, mostly introductory material and documentation, although I enjoyed [Rob Pike's][rp] talk asserting that [Concurrency is not Parallelism][plism] which has been a refresher for some of the terminology and approaches of concurrent programming. There's also an excellent episode of [The Changelog][cl100] with Rob and [Andrew Gerran][ag] talking about a lot of the design choices and origins of the Go language.

I like to try out new languages and aspire to be a polyglot where I can. It's fun to have certain accepted standards challenged plus lessons learned from one language can strengthen the design decisions made during application development in any other.

So speaking of which, I wrote a [little status monitor][gh] application in Go. It uses a dynamically-configured number of concurrent workers to test and update sets of services read from a JSON config file. The application serves a (totally unstyled) status page or a JSON response if the correct headers are sent. It features mostly the `log.Println` statements I alluded to earlier, but it was fun to write and taught me a lot about the language idioms along the way.

\* Whatever that means.

[gl]: http://golang.org
[gh]: https://github.com/wadtech/statusmonitor
[plism]: http://vimeo.com/49718712
[cl100]: http://5by5.tv/changelog/100
[rp]: https://twitter.com/rob_pike
[ag]: https://twitter.com/enneff
