---
layout: post
title: "Confessions of a Language Snob"
date: 2014-05-28 22:29
comments: true
categories: [programming]
---

I am a language snob. In particular I fall head over heels for most functional languages, especially MLs and Lisps. Show me the latest and greatest Javascript framework and I will just wish I had immutable data types and a saner method dispatch system. I try to keep quiet about it at work with varying degrees of success, but it's frustrating to work around one language's problems when you know of other solutions.

The difficult reality that few admit is that every language has a weak point. Ruby is slow and the lack of import semantics and proper namespaces makes it difficult to determine what code will run. Python lacks a good lambda and whitespace sensitivity brings new difficulties. Javascript is just pure insanity. Lisp is poorly standardized and tends to have subpar documentation. The list goes on and on. No language is perfect, but it's very easy to focus on the high points of one language while working through the low points of another.

The real bummer about being a language snob is that there's really nothing to be done about it. For any given issue there will always be another language that exceeds in that area, but it's almost always insane and impractical to convert your entire company over to it. So even if you think Go would solve every problem that your Python codebase has, and you know that the downsides wouldn't be insurmountable, the simple reality is that convincing the organization to throw away their perfectly good Python code on a whim is insane at best. And that's assuming that converting to your language wouldn't come with downsides worse than the language you are coming from.

Thankfully, there is an upside to being a language snob. Polyglots have a far more flexible understanding of what a program should do, especially when they're used to a wide range of paradigms. Someone who has done nothing but C or Java programming might have very little context into why mutable state can be so problematic, but someone who knows Clojure or Haskell will know the tradeoffs of mutable vs. immutable state intimately. Each new paradigm a programmer embraces means that they have more internal views on how a particular problem could be solved, a bit like having an experienced team in your head to discuss the merits of various techniques at lightning speed.

While it may be very frustrating to know of better solutions in other languages, there is a benefit to knowing about them. Being a language snob can help you evaluate your choices more effectively and discover solutions and strategies that might not be immediately obvious if you only knew one language. And between being a bit of a snob and attempting to shoe-horn every problem into a one-size fits all paradigm, I'll take being a snob.
