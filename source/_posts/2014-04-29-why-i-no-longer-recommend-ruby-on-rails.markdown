---
layout: post
title: "Why I no Longer Recommend Ruby on Rails"
date: 2014-04-29 19:16
comments: true
categories: [Programming]
---

# CYA Moment

First off, this is not going to be a post explaining why Ruby on Rails
is awful and that you should change immediately. If you use RoR to good
effect already, then good for you and keep up the good work.

What I am going to do is explain why RoR should not be the automatic
first choice for all kinds of web development by way of the tradeoffs
that it creates.

# The Upsides

Ruby on Rails makes it incredibly easy to get a website up and
running. The scaffolding is simple and easy, and the migration system
has one of the cleaner DSLs I have seen in a long time. Combine that
with the fact that it comes with water-tight rake commands to work with
the basic types of rails and you have a fantastic environment for the
start-up project or the starting engineer.

On top of that, the popularity of RoR makes documentation and examples
plentiful to the point of absurdity. Unfortunately that means that you
have a very hard time picking out what you need on stack overflow past
the 10,000 other questions that are not quite similar enough, but that's
a good problem to have as a language and community.

# The Downsides

Unfortunately we don't spend the majority of our professional lives
creating websites, we spend them maintaining and improving them. So time
to start is always going to be dwarfed by maintenance time on all but
the most throw-away projects. So let's ignore the scaffolding and take a
closer look.

## Ruby

Ruby is an interesting beast. For being a scripting language it is truly
massive. A quick perusal of the core docs exposed 1740 functions!
Compare that with the entire
[Go spec](http://golang.org/ref/spec#Predeclared_identifiers) or
[clojure.core](http://richhickey.github.io/clojure/clojure.core-api.html#clojure.core/syntax-symbol-anchor). Now
obviously this is only a rough way to measure, but as I have mentioned
in [the past](http://ashtonkemerling.com/blog/2013/04/23/size-matters/)
the size of a language is very important. Large languages have a very
large surface area for bugs and changed behaviors, and they occupy
mental space normally reserved for domain and library knowledge in your
engineer.

Also, Ruby is very very slow. And to some degree that is okay for a lot
of products. If speed were everything we would have never changed from C
or Fortran. But with Ruby the path to better performance will be painful
and probably full of rewrites.

## Rails

### The Magic

All of Rails startup ease comes with a very specific cost: tight coupling.
