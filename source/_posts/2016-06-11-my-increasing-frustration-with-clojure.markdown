---
layout: post
title: "My Increasing Frustration With Clojure"
date: 2016-06-11 06:24
comments: true
categories: [programming, clojure, ruby]
---

First off, this is not a "I'm quiting in disgust" post. Those are childish and a waste of everyone's time. But this is a post of frustration as I watch something I really like being slowly allowed to get worse.

First off, some history. My first job out of College was in Common Lisp, and I love/hated it. The power it brought and the pain it brought were both one and the same. No modern libraries, no modern build tools (this was before [QuickLisp](https://www.quicklisp.org/beta/)). One on hand, I *loved* working with paredit and Emacs, being able to quickly fly about my code and manipulate it in blocks rather than line by line. On the other, I couldn't help but be envious of those who could actually ask for help from a functioning open source community.

A few years of Python, Ruby, and Javascript later, I found Clojure. And I thought I'd found the solution to literally of the things. Paredit works again? Check. A thriving open source community? You got it. Deploy as a Jar rather than CL's hilarious "dump the state of a running program and call it good" setup? Fuck. Yes.

And beyond the superficial things, there was a lot to love, especially coming from a more recent brush with Ruby on Rails. Clojure makes it very easy to make things [referentially transparent](https://en.wikipedia.org/wiki/Referential_transparency), and it tends to favor explicit calling semantics over convention (or more derisively, "magic"). This means that a Clojure code base will require more plumbing code, but that also means it's possible to navigate to the code that does routing and understand how it works, no more having to search through your framework's codebase just because they do dynamic method creation and method_missing magic. 
 
 As far as I was concerned, the editor was the only weak point for Clojure. Back when I got into Clojure Cursive was still brand new, Emacs really was the only editor that was worth using and even it had some stability and usability issues. But I assumed that continued interest would stabilize Emacs, bring Vim up to speed, and improve Cursive to the point where it would be competitive with Emacs/Vim.
 
 But all was not well, and if I'd paid attention I might have noticed a few places where the core Clojure's teams priorities didn't seem to make much sense to me. And now that I work in Clojure professionally, I really cannot ignore them or remain silent about them any more.
 
 The core Clojure team prefers green field development over improvements and bug fixes to existing code to a degree that deeply worries me. I no longer trust that any issues I find stand a chance of getting fixed, as all the bugs we've posted are either in limbo, or flat out rejected. Multiple members of my team have given up on posting new bugs because they have no faith that it'll help anyone.
 
 These are pretty heavy and vague accusations, so I'm gonna break this down a bit to make it clearer and easier to digest.
 
## Ignorance or Apathy of Underlying Principles
 
 Programming isn't math per-se, especially in a language that's not explicitly based on Category or Type theory. That said a lot of the things that we do are backed or defined by mathematics, and to ignore that is to guarantee bugs. This is most clear in clojure.set which contains functions that are supposed to mirror the definitions created by Set Theory like ```union```, ```difference```, ```intersection```, etc.
 
 And the namespace is completely riddled with bugs. ```union``` returns duplicates if some of the inputs are lists instead of sets *depending on their length*. ```intersection``` will either return nonsense values or throw a ClassCastException if you provide it anything other than sets, again dependent on data.
 
On their own, this is no big deal. Bugs happen, there's really no point in berating people just because they made a mistake. Instead the bug gets fixed as best and as soon as reality allows and we all move on. In fact, for the above bugs there are two possible fixes: raise an IllegalArgumentException if anything other than sets are provided, or coerce lists and vectors to sets before continuing. Both of these approaches are valid due to the fact that this is a dynamic language that defaults to immutable collection semantics; which one you pick is then a matter of how you want to affect your downstream users.

Oh wait, some of these bugs were filed in *2009*, 7 fucking years ago! Here comes the berating. These functions are **tiny**, a simple implementation of ```union``` is one line. And while they're heavily used, they're simple in usage and signature; no need to change a lot of call sites to fix this bug. There are only two reasons to explain why these bugs have not been fix; they either do not understand that this is an issue, or they do not care. 

Actually, their comments on the issues lets us know that they do not understand that this is an issue. Rich Hickey said in 2009 "the fact that these functions happen to work when the second argument is not a set is an implementation artifact and not a promise of the interface". How you define getting the wrong type with nonsense values counts as "working" is beyond me. Is it just because it doesn't throw an Exception? Anyone here prefer bad data instead of exceptions when dealing with functions like this? I doubt it.

## Inconsistency Between Best Practices and Clojure Implementation

Clojure includes a pretty powerful concept called protocols. Basically a protocol is an interface that can be added to classes after the fact, and lets you dispatch to different behavior silently at run time.

This is pretty neat, it lets you abstract over multiple data types and include Java classes in the fun. For example ISeq provides all the methods needed to iterate over a collection and it works with all the Clojure and Java data types. So you can use Clojure's ```map``` function over its own data types and Java's because it depends on the seq interface.

As you can imagine, this is the recommended way to work with things. Rather than having to do ```cond``` or ```if``` logic on various classes, define and use an appropriate protocol and you're good to go!

It sounds like a good theory doesn't it? But Clojure itself doesn't actually do this. Clojure.core contains 89 calls to ```instance?``` in order to check runtime type, instead of the helper methods to check for protocol implementation.

[Here](http://dev.clojure.org/jira/browse/CLJ-1944) is a bug found by my colleague that highlights this issue. List and Vector are both seqs, but ```into``` for a map only accepts vectors, lists causes a ClassCastException. This is kind of nuts because an IllegalArgumentException makes more sense, and there's no practical reason to differentiate between a list of two elements and a vector of two elements. Actually, Clojure considers ```[1 2]``` and ```(list 1 2)``` to be equal, so this really makes no sense

Even more obnoxious, it was closed as wontfix. Apparently a single sentence in the docs is good enough for the Clojure team, as well as a paper-thin argument about performance on a 2 element list. So not only is this just broken in a barely documented and very surprising way, Clojure itself ends up programmed in a way that isn't recommended by the Clojure docs.

This has spread to other projects. [Om](https://github.com/omcljs/om) has a bug where lists aren't acceptable in its data structures, only maps sets and vectors. To say that I was treated pretty [shabbily](https://github.com/omcljs/om/issues/246) by David Nolen on this issue almost goes without saying. Naturally the intro [docs](https://github.com/omcljs/om/wiki/Basic-Tutorial) barely call this out, and the docs [dedicated](https://github.com/omcljs/om/wiki/Cursors) to the troubled component does not mention this at all. To be fair, the [troubleshooting guide](https://github.com/omcljs/om/wiki/Troubleshooting) explains this, but in my opinion that's probably a clue that the bug is common enough that you should find a fix for it.

## Show Stopping Bugs Remain Untouched

There are a shocking number of big, bad bugs hiding in the Clojure Jira, some really old

- [This one](http://dev.clojure.org/jira/browse/CLJ-308) about fixing with-open for Clojure defined stuff provided a patch 5 years ago. 
- [Calling VarArgs Successfully](http://dev.clojure.org/jira/browse/CLJ-440) from 6 years ago.
- [Contains? broken for transient collections](http://dev.clojure.org/jira/browse/CLJ-700) from 5 years ago. But it's due for this version, so cross your fingers!
- [Bizarrely Defensive Response](http://dev.clojure.org/jira/browse/CLJ-1463) for a minor code review.
- [Duelling ClassLoaders](http://dev.clojure.org/jira/browse/CLJ-1741) first discovered last year. This one breaks our editors constantly. Not even assigned.


I could go on, but I feel that I've made my point. Bugs, even major ones are either closed as "wontfix", or are ignored for years despite the pain felt by users. That's not even covering the dismissive and distrustful attitude given in some of the replies.


## Strange Priorities

The Clojure team appears to be super focused on new features, at the exclusion of existing namespaces. The big highlights from the past year or so have been Transit, Transducers, and Spec.

These are okay, I guess. We use transit a bit, and it's kinda cool. But we really don't use 90% of its features, it's basically JSON for us that can convert numbers to BigDecimals.

We have yet to find a place that Transducers would help us. They're neat enough, but the built in lazy sequences are working A-OK for us, so we don't really feel the need to change over.

I'm not holding my breath for Spec. It doesn't fix anything for me that other libraries aren't already providing.

Know what hasn't seen any major improvements in forever? Clojure.test. Clojure.Test is frankly sad. Fixtures are done via some global state, and you can't even setup fixtures to work across the entire test suite. Need a database to run functional tests? Well either you need to override the main test runner (good luck running individual tests now!) or you have to setup each namespace to open and close its own database connection (don't forget, or your DBA will wonder why Emacs has 1000+ database connections). I'm 100% behind the idea that I'll have to write a bit of glue code, but without anywhere to *put* that code I'm kind of screwed.

And then there is the ```is``` function. It's literally the only assertion provided by clojure.test. It's this fancy little macro that grabs its body, evaluates it, then uses the body to produce a human readable message about the failure.

And it's garbage. The fact that [plugins](https://github.com/pjstadig/humane-test-output) exist to make this easier on the eyes should tell you everything you need to know. Oh but don't use that with Emacs/Cider! It'll crash the Cider plugin, which is trying to parse the default output.

Back when I used Emacs, I had a stash on my box that disabled AOT, pedantic checking, and the humane-test-output plugin from my project.clj in order to use Cider. Without that stash applied Cider wouldn't start, couldn't reload code, and would crash when running tests. Now that I use Cursive that's less of an issue, but it's still kind of nuts I had to decide between a working editor and readable output when I ran ```lein test```

Sorry, I didn't even highlight the craziest bit of that last paragraph, did you catch it? I had to disable humane-test-output from my *project.clj*. That's because you install it by injecting some code in project.clj that **redefines** some multi-methods, because there's no plugin architecture. How nuts is that?

Now I might hear you say "You don't have to use clojure.test!", and you're right. But clojure.test has clearly won in the Clojure testing namespace. The only real competitors for clojure.test are [Speclj](https://github.com/slagyr/speclj) and [Midje](https://github.com/marick/Midje). I've literally never met someone in person that's used Speclj, and Midje is super polarizing because it's basically a collection of magic macros. The fact that the second entry for Midje is about CircleCI rewriting from Midje to clojure.test should tell you a lot.

So why don't we have more creature comforts for clojure.test? I'm not really sure. As far as I can tell the change to it was the inclusion of [test.check](https://github.com/clojure/test.check), but that really was nothing more than simple-check getting renamed and transferred to Clojure ownership.

# Okay, Now What?

As I stated before, this isn't a "I'm quitting Clojure!" post. Partly this is because I work in Clojure on a daily basis, and I both like my job and am professional enough to keep working despite my complaints. And partly this is because I do not have a replacement for Clojure in mind for my own personal projects. But off the top of my head, there are the things I'd like to see fixed in the Clojure areas.

- More love for clojure.test.
- No tolerance for bugs that result in bad-data. Built in functions should either work, or throw an understandable exception.
- Friendlier responses in Jira. Someone who has gone to the work to sign up and try to help out should be treated with more respect.
- Fix underlying compiler bugs before adding features. The other way only codifies bad behavior and guarantees that it cannot be fixed.
- Understand that if enough people have the same issue, it's the codes fault and a FAQ entry is **not** good enough.


Basically I want Clojure to be a simple to use language backed by a friendly and active community. What I see now is drifting in the wrong direction, and I'd like to see that corrected.


