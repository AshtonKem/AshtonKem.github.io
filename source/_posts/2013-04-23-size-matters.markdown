---
layout: post
title: "Size Matters"
date: 2013-04-23 21:54
comments: true
categories: [ programming, languages ]
---

About 6 months ago I started to look for a new language for hobby projects. I wanted something practical, yet fun. I managed to limit the criteria down to:

* Functional
* Good community
* Growing acceptance

This automatically eliminates a lot common languages, like Python, Ruby, and Java. After my Common Lisp job, I knew that a sane compilation system/target were an absolute must, so I looked at Scala first. On the surface Scala seemed a real winner: growing fan base, increasing commercial adoption, functional, and targeting the JVM. But while trying out Scala I realized that I had missed a huge criteria: language size.

There's only so much information that a programmer can keep in their mind at a time. Every programmer needs to split their internal memory in at least 4 different ways, if not more. There are language details to remember, library or API interfaces to recall, domain knowledge to retain, and possibly coordination or social issues to be concerned about. Once this internal memory is exceeded details end up being forgotten, and recalling forgotten information involves an expensive round trip to documentation, code, or the internet.

There's no escaping the need to recall language details, at least until someone invents a syntax free programming languages; similarly it's near impossible to write an application of any size without relying on libraries or APIs. Domain knowledge is often expensive to acquire and beneficial to have. And social issues cannot be resolved by programming language choice alone.

So the real question is, what are you going to skimp on? Are you going to pay the price of memory "cache misses"? Are you going to forget portions of your language, or certain libraries? Or are you going to segregate domain knowledge among the team so that everyone knows their bit only?

The real world answer is probably a bit of each. But I contend that reduction of language features in memory is the least damaging of all the outcomes. I doubt it's controversial to say that domain knowledge should be shared widely among developers, and should be as much in memory as possible; programmers who understand what they're programming for tend to program better on average. Similarly it's hard to eliminate a decently written library: that functionality has to come from somewhere, and it'll either be an external library, an internal one, or duplicated code.

But language reduction is an interesting idea. Programmers long ago got away with previous few features, yet managed some amazing things. Don't believe me? Consider how small the Scheme spec is compared to say, Java. It reduces the need for fancy and hard to learn IDEs, allows the programmer to get more done between looking up function or class definitions, and increases the speed with which an engineer can fully understand a piece of code.

It was actually this feature that finally drove me away from Scala to Clojure. After a few hours of Scala work I was turned off by the size of the language, and got tired of saying "Why did it do that?"
