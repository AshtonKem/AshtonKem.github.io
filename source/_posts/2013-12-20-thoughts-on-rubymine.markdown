---
layout: post
title: "Thoughts on RubyMine"
date: 2013-12-20 21:38
comments: true
categories: [Programming]
---

As part of my new job at Pivotal Labs I've been pair programming almost
every day. The obvious challenge with pair programming, especially in a
popular language like ruby, is in choosing what tools to work with. Vim,
Emacs, RubyMine, TextMate, the choices are various and divisive.

To make peace among the engineers, it makes sense to dictate one set of
tools to make peace among all your employees, and to make provisioning
the machines easier. Pivotal has decided to standardize on RubyMine with
a dark color scheme and a few custom configurations.

# The Good #

RubyMine, being set up for ruby in particular, works very well with
navigating, indenting, and colorizing ruby code. With the exception of
setting a variable as the result of an if expression, I have never seen
RubyMine indent code incorrectly or get confused on coloration.

It also does a decent job of navigating to ruby classes and functions,
something that is quite hard since ruby lacks explicit import
semantics. It definitely makes hunting down odd test harness functions
down a breeze, and has saved me in the past. It also is good at
identifying the view that cooresponds to a controller method, but due to
the way that tracker is laid out, I haven't had a chance to use this
feature often.

And as the cherry on top RubyMine includes a "Textmate like" quick find
feature. In large code bases this will save you about a minute trying to
find a particular file, so ling as you have an idea of what it's called!

# The Bad #

If you only have unique controllers and models, you will absolutely love
the ability to jump to a class definition. Since we use a lot of
similarly named controllers inside namespaces to control API versions,
it often gets confused about what version I want. The quick find feature
sometimes ignores the path if you provide it, which makes copying files
from stacktraces occasionally unreliable.

Javascript and less support is mediocre. No real complaints, but nothing
to set it apart from other environments in my opinion. Maybe my
colleagues who work on the front end would have a more nuanced opinion.

Also sometimes with large files the coloring or error checking can lag
behind. This usually kicks in at files longer than 3000 lines, which is
not unusual for test files. For the most part this is just an annoyance
which doesn't affect editing in any serious way.

It has git integration, which lags behind the offerings from Emacs, Vim,
and Eclipse in my opinion. I end up using the command line instead of
the built in tools.

# The Ugly #

RubyMine is incredibly dim witted when it comes to parenthesis and
quotes. If you wish to put an escaped quote at the end of a string,
RubyMine will let you escape your ending quote, then insert a matching
pair right afterwords when you try and fix the mistake! To add insult to
injury, you must move the cursor before fixing the issue lest RubyMine
delete both of the extra quotes. Very frustrating.

RubyMine is also one of the most memory consuming programs I use. At
least once a week it will grind my machine to a halt due to memory
usage, which is impressive on a machine with 16G in memory.


# Conclusions  #

Out of the box, RubyMine works very well. I think it is a good
compromise for large teams. But if you have time to learn some more
complicated tools, I believe you would be way better off learning a more
customizable editor like Emacs or Vim. It might take some effort, but
these editors can do just as much as their commercial cousins and will
continue to do well no matter what language you decide to use in the
future.
