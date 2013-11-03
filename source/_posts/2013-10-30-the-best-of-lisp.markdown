---
layout: post
title: "The Best of Lisp™"
date: 2013-11-01 20:04
comments: true
categories: [programming]
---

Edit: Dan Benjamin himself informed me that I missed the sarcasm boat
here. I'm leaving the relevant bits, and cutting out the now irrelevant
criticism.

I'm slowly working my way through the [Back to Work](http://5by5.tv/b2w)
podcast. I'm way behind, so please excuse that this post references an
episode over 2 years old at this point.

[Episode 11](http://5by5.tv/b2w/11) was about "future proofing your
passion", among other things. In it, Merlin mentions that one thing you
can do as a low risk investment in a programming career is to maintain
current on new languages. He mentions Scala, Erlang, and Lisp as a
few. With an <del>uncharacteristic lack of self-awareness</del> unusual
amount of sarcasm, Dan expresses pretty much disdain and lack of
interest in anything other than Ruby, even going as far as saying "Ruby
took the best of Lisp".

While Dan may have been joking, it is probably worth examining the
relationship between Ruby and Lisp. As someone who has worked in Ruby
and Lisp professionally, I feel uniquely qualified to talk about their
relative strengths. While there are a ton of cases where Ruby clearly
learned and copied from Lisp, there is also a laundry list of areas
where Ruby failed to learn from Lisp's successes and failures.

## Functions


Being a functional language, functions are pretty important to Common
Lisp. Ruby thankfully took some of the highlights from CL during its
development, like a heavy emphasis on using map & filter over iterative
loops, but it missed some important things.

CL only has one kind of function, the function. There are some nice
syntax features for the way you define them, and a few extra features
for how they're called, but they're all just functions. Some might be
anonymous, while others might be bound to a symbol. Sometimes you call
them by name, or with funcall or apply if you need to pass them
around. Sometimes they're a multimethod from CLOS which dispatches
depending on the class of the argument, but at the end of the day
they're all just functions which behave the same. Ruby picked up on the
idea of using different syntax for some functions (think Blocks), but
for some reason included Procs and Lambdas as well. Most Rubyists I've
met express confusion over what the exact differences are, and always
say "Just use one kind and one kind only".

Common Lisp also has much better syntax for parameters. In particular it
provides for required and optional positional parameters, enumerated
keyword parameters, and extra parameters to be collected into a
list. Ruby learned well by also allowing default parameters to be
evaluated at call time, but it ended up with a way worse
syntax. Particularly bad is the way Ruby handles extra keyword
arguments, simply dumping them into a lisp, optionally merging it on top
of your default arguments hash if needed. CL provides a way for default
values to be defined for keyword arguments in the method header, which
is far cleaner than the Ruby way.


## DSLs

Ruby often gets compared to Common Lisp in its ability to produce Domain
Specific Languages, or DSLs. Both blocks and Ruby's special dispatch
mechanisms are used and abused by programs such as Chef to create an
easy interface between high level concepts and low level details.

And Ruby is pretty good at DSLs, I would estimate that it can hit a good
60-80% of the DSL cases fairly easily. But especially once you step
outside the bounds of configuration DSLs like Chef, Common Lisp truly
shines. In Ruby you can't really add new syntax or special characters,
and you can't easily force it to recompile a given section of code to
behave in a specific way.

But you can do that in Lisp. Lisp's macros are so powerful, many
languages are first prototyped as a large section of Lisp
macros. Clojure was originally implemented in a few hundred lines of
Lisp macros. This involved adding new meanings to symbols that don't
exist in CL, like { and }. But with Lisp's reader and regular macros,
this is perfectly reasonable.

## Fragmentation

One of the biggest weaknesses of Common Lisp was its weak standard. The
standard did not specify a lot of common behavior, such as
threading. The result was a proliferation of both libraries that were
implementation specific, and libraries designed to bridge the gap, like
[Bordeaux Threads](http://common-lisp.net/project/bordeaux-threads/). At
this point every library and application is so set in its ways, the
chances of the standard being unified and fixed is almost none.

Yet somehow Ruby ended up with no spec at all, only a reference
implementation. It's so bad that the Rubinius people proudly proclaim
that they created 20,000 specifications to as closely as possible match
MRI, the reference implementation.  While this is a laudable effort, I
think the general opinion is that only the reference implementation is
to be trusted, so most libraries assume that you're not using Rubinius,
MacRuby, or anything else. This is sad, because it means that the Ruby
community is very unlikely to use anything other than MRI, no matter how
good a competing runtime might be.

## The Takeaway

I love Ruby. It's a good language. But if you want truly mind opening
programming moments, Lisp is the only way to go. No other language can
give you the sense of "I can do *anything*" quite like it. I highly
recommend every Rubyist at least try Common Lisp for a bit, to at least
understand where their language of choice came from.
