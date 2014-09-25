--- 
layout: post
title: "Integrating Test.Check and Javascript"
date: 2014-09-25 08:41
comments: true
categories: [programming]
---

# Introduction

I was recently on
[The Cognicast](http://blog.cognitect.com/cognicast/ashton-kemerling-064)
with Craig Andera where we discussed using Generative Testing on a large
non-Clojure(script) codebase, in particular Ruby on Rails and
Backbonejs. If you haven't listened to the show yet I highly recommend
it first.

As I promised on the show, I'd like to share how we used Test.Check to
test our Backbone.js code base. Our overall strategy for testing
Javascript is going to be:

1. Compile JS into one file (just like prod).
2. Compile tests into a single file.
3. Combine them in a PhantomJS process.
4. Let the tests do their thing.

While we have been super pleased with the results of Generative Testing,
there have been some hurdles for getting it to work for us. In this post
I'm going to go over how to setup Test.Check to work with your
Javascript app, and how to dodge all the pitfalls I found.

Here are the challenges that lie between us and Generative Testing bliss.

1. Picking the right library
2. Setting up Leiningen & Cljsbuild
3. Dodge PhantomJS issues
4. Avoid mangling your app, and defeating dueling dependencies

# Picking the Right Library

First of all, there are two libraries that exist,
[Test.Check](https://github.com/clojure/test.check) and
[DoubleCheck](https://github.com/cemerick/double-check). Because
Test.Check is an official Clojure library it is Clojure (JVM) only, so I
recommend DoubleCheck (maintained by Chas Emerick) which is capable of
cross compiling to Clojure and Clojurescript.

The only catch with DoubleCheck is that it's not currently possible to
segregate tests via metadata for running in groups. But with the way we
will be running these tests that won't be an issue.

# Setting up Leiningen

First step, install [Leiningen](http://leiningen.org/) and create a
project.clj wherever you Javascript code is. We're going to use
[Cljsbuild](https://github.com/emezeske/lein-cljsbuild) to compile our
testing code for execution. I in put my test code in test/cljs (because
I have clj and cljs based tests), and send the compiled output to
tmp/tracker-cljs.js. Note: this guide *only works for Clojurescript
0.0-2234*, I need to figure out why the latest build of Clojurescript
doesn't work.


I highly recommend you send the output of the compilation process to
either a temporary or gitignored location. The output will be fairly
large, and it will bog down your repository with its size.

I don't want to duplicate the Cljsbuild how-to, so if you don't know how
to make it work, you should check their docs. Our project.clj is
reproduced at the bottom of this post if you have issues.

At this point, we can write the simplest test possible:

```clojure
(ns tracker-cljs.simple-test
  (:require [clojure.test.check :as core]
            [clojure.test.check.generators :as gen]
            [clojure.test.check.properties :as prop]
            [cemerick.cljs.test :as t])
  (:require-macros [clojure.test.check.clojure-test :refer (defspec)]
                   [clojure.test.check.properties :refer (for-all)]
                   [cemerick.cljs.test :refer (is)]))

(defspec simple-test 10
  (for-all [v (gen/such-that gen/not-empty (gen/vector gen/int))]
    (println v)
    true))
```   


# PhantomJS issues.

In order to run the tests, you would typically have a :tests section in
:cljsbuild that looks like this:

```clojure
:test-commands {"unit-tests" ["phantomjs" :runner
                                          "compiled-application.js"
                                          "tmp/compiled-tests.js"]}
```

This will load our JS into the app, along with the tests, and then run
them. But you might notice errors that look like this:

```
SECURITY_ERR: DOM Exception 18: An attempt was made to break through the security policy of the user agent.
```

That means that your app code is trying to access local storage, and
PhantomJS does not like it when you do that without loading a 
webpage. The solution for this is to start a server so we have a page to
visit, and visit it via PhantomJS. So on Tracker we use the following
two bits of code.

generative_runner.js, to visit the actual page:

```
// reusable PhantomJS script for running clojurescript.test tests
// see http://github.com/cemerick/clojurescript.test for more info

var page = require('webpage').create();
page.onResourceError = function(resourceError) {
    page.reason = resourceError.errorString;
    page.reason_url = resourceError.url;
};
var fs = require('fs');
var sys = require('system');
var success;

page.onConsoleMessage = function (x) {
    var line = x.toString();
    if (line !== "[NEWLINE]") {
        sys.stdout.writeLine(line.replace(/\[NEWLINE\]/g, "\n"));
    }
};

page.open('http://localhost:4500', function(status) {
    if (status !== "success") {
        console.log("Couldn't load page");
        phantom.exit(1);
    }
    for (var i = 1; i < sys.args.length; i++) {
        if (fs.exists(sys.args[i])) {
            if (!page.injectJs(sys.args[i])) throw new Error("Failed to inject " + sys.args[i]);
        } else {
            page.evaluateJavaScript("(function () { " + sys.args[i] + ";" + " })");
        }
    }

    page.evaluate(function () {
        cemerick.cljs.test.set_print_fn_BANG_(function(x) {
            console.log(x.replace(/\n/g, "[NEWLINE]")); // since console.log *itself* adds a newline
        });
    });

    success = page.evaluate(function () {
        var results = cemerick.cljs.test.run_all_tests();
        console.log(results);
        return cemerick.cljs.test.successful_QMARK_(results);
    });
    phantom.exit(success ? 0 : 1);
});
```
  
  
And a rake task to stand up a server and run everything. If you don't
use Rails for your Javascript code you might need to use different
commands to compile, but the intention remains. The WEBrick server
provides a blank page for us to visit and run our tests on, which
prevents PhantomJS from raising security errors.

```
require 'fileutils'
require 'webrick'

namespace :test do
  desc "Run ClojureScript generative tests"
  task :generative do
    server = WEBrick::HTTPServer.new({:Port => 4500, :DocumentRoot => ".", :BindAddress => "0.0.0.0"})
    Thread.new do
      server.start
    end
    exitCode = 0

    begin
      Rake::Task['assets:clean'].invoke
      Rake::Task['assets:precompile'].invoke
      exitCode = system "lein cljsbuild test"
    ensure
      server.shutdown
    end
    exit exitCode
  end
end
```

To make all this work together, update project.clj to reference the
generative_runner.js file instead of :runner, and use ```rake
test:generative``` to kick off the run.


# Don't Mangle Your Code

If your application is anything like Tracker, you might use some Google
Closure dependencies without using the entire Closure compiler. And even
if you don't need use Closure, you certainly have functions and classes
in the global namespace that you don't want mangled.

To get around this, I recommend the following settings:

Add ```:libs [ "compiled-application.js" ""]``` to the cljsbuild section
in project.clj. This prevents DoubleCheck compiler errors due to
classpath issues, and it allows the Closure compiler to see everything
that your application provides. So if your tests and applications have
overlapping Closure dependencies you won't get double provide errors.

Secondly I recommend that you only use the simple compilation mode. This
will prevent Closure from mangling global names, which will make
debugging easier and prevent your tests from being able to find the
production code. The space saving and code elimination that advanced
mode provides is more of a problem than a benefit for testing, so it's
not worth fighting to get advanced to work.

You can fiddle with source maps if you wish, but I
haven't had much luck or use for them; simple compiled Clojurescript is
easy to read, and most of the serious errors have come from the 43k
application javascript file, not the test file.

# Have Fun and Make More Tests

Once you have that going, it should be possible to open up and create
increasingly complicated tests. As a teaser and a good example, the
following code caught a tricky JS ordering bug.

```
(ns tracker-cljs.panel-items-test
  (:require [clojure.test.check :as core]
            [clojure.test.check.generators :as gen]
            [clojure.test.check.properties :as prop]
            [cemerick.cljs.test :as t])
  (:require-macros [clojure.test.check.clojure-test :refer (defspec)]
                   [clojure.test.check.properties :refer (for-all)]
                   [cemerick.cljs.test :refer (is)]))

(defn ids [items]
  (if (seq? items)
    (map (fn [item] (.get item :id))
         items)
    (map (fn [item] (.get item :id))
         (.-models items))))

(defn create-models [ids]
  (map (fn [x] (Backbone.Model. (js-obj :id x)))
       ids))


(defspec sort-check 100
  (for-all [v (gen/such-that gen/not-empty (gen/vector gen/int))]
           (let [models (create-models v)
                 sorted (sort v)
                 subject (tracker.PanelItems.)]
             (.reset subject (apply array models))
             (.refresh subject (apply array (sort-by #(.get % :id) models)))
             (is (= sorted
                    (ids subject))))))
```


And the following is our project.clj, with unnecessary details elided
for readability.

```
(defproject generative-testing "0.0.1-SNAPSHOT"
  :plugins [[lein-cljsbuild "1.0.3"]
            [com.cemerick/clojurescript.test "0.2.2"]]
  :dependencies [[org.clojure/clojurescript "0.0-2234"]
                 [com.cemerick/clojurescript.test "0.3.1"]
                 [com.cemerick/double-check "0.5.8-SNAPSHOT"]
                 [org.clojure/clojure "1.5.1"]]
  :cljsbuild {:builds [{:source-paths ["test/cljs"]
                        :compiler {:output-to "tmp/tracker-cljs.js"
                                   :libs [ "public/web/assets/application.js" ""]
                                   :optimizations :simple
                                   :pretty-print true}}]
              :test-commands {"unit-tests" ["phantomjs" "lib/generative_runner.js"
                                            "public/next/assets/next/next.js"
                                            "tmp/tracker-cljs.js"]}})
```

Also, the following function is helpful for converting from Cljs data
structures to pure Javascript ones. It causes some compiler warnings,
but they appear to be harmless.

```
(defn clj->js
  "Recursively transforms ClojureScript maps into Javascript objects,
   other ClojureScript colls into JavaScript arrays, and ClojureScript
   keywords into JavaScript strings."
  [x]
  (cond
    (string? x) x
    (keyword? x) (name x)
    (map? x) (apply js-obj (flatten (map (fn [[key val]] [(clj->js key) (clj->js val)]) x)))
    (coll? x) (apply array (map clj->js x))
    :else x))
```

Good luck, and don't hesitate to reach out to me on Twitter if you have
any questions!
