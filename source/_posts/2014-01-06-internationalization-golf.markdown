---
layout: post
title: "Internationalization Golf"
date: 2014-01-06 22:48
comments: true
categories:  [programming]
---

Martin Grüner had a [fun](http://martingryner.com/software-localization-is-tricky/) article about his experience writing an internationalized app. I thought it would be fun to share my own experiences.

My first job out of college was working on a Common Lisp (CL) web application. The application was only a few years younger than me, and had originally written in CL due to a particularly good HTML/XML library available in CL at the time. Unfortunately in the intervening years the HTML library stopped being state of the art, and the whims of enterprise software engineering had left CL behind for web development, resulting in a serious lack of common programming conveniences.

Right after joining the company, I was informed that the sales person had a potential lead with clients in a Spanish speaking country. The application at this point was English only, but the QA engineer was married to a native Spanish speaker who was willing to help translate the application. All we needed to do was wire the application up for internationalization and localization. I was tasked with picking or creating a library and interspersing it throughout the application. The only criteria was that it both looked good and was capable of displaying different languages to different users depending on their browser's "accept-language" header. So compiling or packaging up a new application with hard-coded languages was not an option.

I eventually decided that all the existing libraries were insufficient and we needed to make our own. I'm still not sure if that was the right choice or not. CL doesn't have the strongest library ecosystem around, but I was also a very young engineer and more susceptible to the "Not Invented Here" syndrome than I am now. Although a quick perusal through the current offerings involves libraries whose home pages are 404s, libraries who are nothing but FFI bindings to a GNU C library, and those whose list of defects includes "no documentation", "undocumented code" and "slow PO parser".

Compounding the issue was CL's format function. CL has an exceedingly powerful formatter that is capable of unwrapping loops and interspersing the correct combination of "," and "and" for a list of strings. This function was used with (reckless) abandon throughout the codebase, something for which I deserve some blame. The lack of dedicated template files compounded the issues; it's a lot easier to reach for format when you're producing HTML the handler-function itself.

There was no way I was going to explain the (non-technical) translator how to deal with format directives like this: "~#[NONE~;~a~;~a and ~a~:;~a, ~a~]~#[~; and ~a~:;, ~a, etc~].", and I didn't want to dig through 500kloc and unroll all the directives. So any translation system I made would need to support at least a subset of the CL formatting directives while hiding them for the translator's sanity.

Worse still, CL's formatter accepts positional arguments only, to my knowledge. Thus there's no particular way to convince the formatter to modify the order of the parameters if the target language has different language structure than English. So my system would need to deal with that.

The final format I settled on would look something like this. The programmer (me) would change (format nil "~a" var) to (jibberish:format "&lt;~a:variable-name&gt;" "Descriptive sentence for translator" :variable-name var). We could then convince our code to print out a file for a language like this:

```
####################################
File: filename.cl
####################################

Original: &lt;variable-name&gt;
Translation: Translation goes here
Note for translator: Descriptive sentence for translator
Original Argument Order: [variable-name]
```

And so on. At runtime the language file would be parsed into a in-memory hash map, which would allow us to replace the format-string with the new one, re-order the argument list according to the translator's needs, and strip the identifiers from the formatting string leaving the raw format directives.


So, how'd I do? Mixed results. The conversion was long and painful, requiring that each format directive and raw string be touched. A huge portion of the "Notes for translator" were either blank due to difficulty and fatigue, or were something along the line "Description, part 1/n" due to multiple calls being joined together in HTML. Changes in formatting calls required that the matching translation entry be hunted down *in every translation file* for the translation to still work.

But technical challenges are usually not a big deal, after all that's a fairly large portion of what engineers do. Probably the worst problem with my system was how it worked for non-technical folk. The first attempt at giving this file to a translator resulted in her helpfully translating all the variable names into spanish, changing "It is &lt;today&gt;" to "que es &lt;hoy&gt;", which resulted in some rather exciting errors. I think my own design sabotaged me in this particular instance, as it required way too much careful explanation to be usable. It was my first encounter with the major difference between using a program whose internals you are familiar and explaining its use to someone else who has never seen anything similar.

I think if I had to do it again, I would've probably spent more time and modified the way the program generated strings instead. I suspect that my custom library avoiding refactoring everything was a case of [false economy](http://en.wikipedia.org/wiki/False_economy), as the time saved up front would have been lost in the time required to train translators and maintain overly fragile translation files. I also learned that if you plan on selling an application in another language market, you need to think about that *before* you start writing code. Internationalization limits some of the choices you can make with your software and design, and it's a lot easier to use that restricted set up front than it is to unwind them after the fact.


As a final irony, while attempting to write this post in Octopress (which uses Jekyll), it crashed several times because the SASS files in Octopress use unicode, which Octopress appears to hate out of the box. You have to change a few environment variables to convince it that Unicode is ok.
