---
layout: post
title: "The Primacy of the Build Tool"
date: 2014-03-31 13:55
comments: true
categories:
---

No programming language stands alone. Besides the compiler, every
programming language includes an ecosystem of libraries, build tools,
analyzers, debuggers, and other utilities. Languages often rise and fall
depending on the quality of these tools and libraries.

For every language there needs to be one central item upon which every
other tool depends. In most languages, this is the compiler or
interpreter. Your Rails project is entirely dependent on the version of
Ruby provided by the current environment, and similarly Maven depends on
the version of javac and java available on the path.

Unfortunately, this makes our code more fragile and dependent on the
machine it was first created on. Someone cloning your code from a
different machine must take care to ensure that their development
environment is close to the original authors, and deployment must ship
the correct compilers and interpeters for production to work well. We
have created tools to help enforce the requirements of the code, but
they are fragile and make upgrading dependencies a pain, as anyone who
has had to fight with RVM can attest.

The one exception to this I have found is Clojure. Clojure inverts the
normal order making the build tool the central item, with the compiler
provided by the project definition file.

``` clojure
(defproject foo "0.1.0-SNAPSHOT"
  :description "FIXME: write description"
  :url "http://example.com/FIXME"
  :license {:name "All Rights Reserved."}
  :dependencies [[org.clojure/clojure "1.5.1"]
                 [org.clojure/clojure-contrib "1.2.0"]
                 [clj-time "0.6.0"]]
  :profiles {:dev {:dependencies [[midje "1.5.1"]]}})
```


The beauty of this change is that it makes setup trivial for another
developer. All they need is the same build tool, and it will deal with
the correct versions of both the compiler and any libraries for the
project. Have other projects that depend on different versions of the
compiler? The build tool only cares about the dependencies in front of
it, and will call the correct version from the correct project.

This also makes upgrading trivial. Want to try Clojure 1.6.0? Change
"1.5.1" to "1.6.0" in the above snippet. Want to write a library that
supports multiple versions of the compiler? The build tool supports
profiles which allow you to swap out compilers trivially because it's
just a dependency.


``` clojure
:profiles {:1.3 {:dependencies [[org.clojure/clojure "1.3.0"]]}
           :1.4 {:dependencies [[org.clojure/clojure "1.4.0-beta1"]]}}
```

Deployment gets easier as well. If you're deploying an uberjar, the core
libraries you tested against are also shipped to production in the same
jar. No need to upgrade your deployment scripts when a new version of
Clojure comes out, as everything is included automatically.

There is one catch to this wonderfulness, which is that Clojure depends
on the JVM, and the build tool cannot change the JVM around. But Clojure
has very simple requirements, Java 1.6 or greater, which
makes it simple to deploy anywhere.
