---
layout: post
title: "Java Without If"
date: 2017-01-26 19:54
comments: true
categories: programming, java, clojure
---

Over the past year my team has been doing something shocking to a lot of
engineers: we're favoring pure Java over Clojure. We aren't rewriting all our
Clojure code, but we definitely prefer Java for green field projects.

This post is not going to be a compare and contrast between the two, nor am I
going to bash Clojure. Language compare and contrast posts always descend into
flame wars, and it's very easy to confuse the result of hard lessons learned
with the benefits of a new language.

Instead I'd like to highlight a very strange aspect of our new Java development,
and I hope that you're sitting down for this. Except tests, I have fewer than a
dozen ```if``` statements currently committed in our Java codebase.

It would be easy to assume that we're just using Java's method dispatch to
replace if statements; rather than inspecting data and calling if/else on it,
you can use interfaces and count on the implementation to provide the difference
in behavior. But such an explanation is insufficient: objects don't magically
construct themselves from unstructured data, and Clojure is not without its own
dynamic dispatch [facilities](https://clojure.org/reference/multimethods).

Ultimately the real explanation for this strange code design lies in my
colleague's extremely exceptional [Lambda](https://github.com/palatable/lambda)
library. It contains a lot of things that a Haskell/Scala developer would
recognize such as Either types, function utilities, coproducts, etc. etc.

In particular I'd like to draw your eye to the Either type, which has replaced
the vast majority of our explicit ```if``` calls. Either is the logical
successor to the Java
8 [Optional](https://docs.oracle.com/javase/8/docs/api/java/util/Optional.html)
type. Optional represents the presence of a value of type ```T``` with
 ```Optional::of```, or it's absence with ```Optional::empty```. Either on the
other hand is parameterized to two values, L and R, and represents the presence
of either a value of type L, *or* a value of type R.

Why is this a logical extension of ```Optional```? Because while Optional is
used to represent a result that may have no value (replacing a null), Either is
used to represent a result that might have been a failure, replacing a thrown
exception, with convention being that left side values represent failure and
right side values representing success.

So, what does that buy us? Well, consider what Optional gets us in the following code snippets.

```java

String x = Helper.functionOne();
if (x != null) {
  x = Helper.functionTwo(x);
  x = Helper.functionThree(x);
  if (x != null) {
    ...
  }
}
```

```java
Optional<String> x = Helper.functionOne()
                           .map(Helper::functionTwo)
                           .flatMap(Helper::functionThree);

```

Optional gives us the ability to say "if a value exists, apply this function to
it" repeatedly. It also gives us the ability to chain successive calls that
return Optionals together with the ```flatMap``` function, eliminating the need
for unsightly and error prone manual null checks. It's also safe for ```map```
and ```flatMap``` to go from ```Optional<A>``` to ```Optional<B>```, which might
eliminate the need for intermediate variables in your code.

Either give us much of the same, but with the ability to represent **why** the
computation failed with left values, along with the ability to chain together
functions working on an Either type. All of the greatest hits of functional
programming are provided for Either, including ```map```, ```flatMap```, and ```filter```.

As a concrete example, imagine a hypothetical JSON parsing library. Parsing is
tricky business, you're all but guaranteed that a failure will happen at
runtime. So how do you handle it? Previously you had 4 choices.

* return null
* return Optional\<ParsedType\>
* checked exception
* unchecked exception

Null is obviously bad, and unchecked exceptions are also risky. Checked
exceptions guarantee that someone will deal with the issue, but they are
extremely annoying, and might result in disparate and different exception
handlers all over the place. Optional is nice, it's safer than null and marks in
the type signature that failure is an option, but it's a bit lacking on
explaining **why** a failure occurred.

What if instead this library returned ```Either<Exception, JsonNode>```, or even
 ```Either<Set<String>, JsonNode>```? The potential for failure is in the type
signature again, we don't need a try/catch, but if we want the unwrapped
JsonNode we have to deal with the potential for a left value. And any functions
we have that operate only on JsonNode can be passed in using the ```map```
function, making chaining a breeze.

Better still we can write other functions that might fail in this form, such as
JSON validation, so that we can chain them together using ```flatMap```. If the
json parsing has failed, flatMap does nothing (it only works on right values),
replacing the need for successive null checks, nested try/catch blocks, or
complicated state checking during exception handling to return the correct
value.

As a result of all this, you can easily imagine a JSON API endpoint looking
something like this:

``` java
public HttpResponse handle(HttpRequest request) {
    return JsonParser.parse(request.getBody())
                     .flatMap(Validator::validate)
                     .map(ServiceObject::businessLogic)
                     .flatMap(JsonGenerator::generate)
                     .match(l -> HttpResponse.internalServerError(l.getMessage()),
                            r -> HttpResponse.ok(l));
}
```

All the potential failure cases are covered by returning an Either rather than
throwing an exception. The very last step is ```match```, which takes two
functions to unify a potential left or right value to the exact same type, which
in this case is ```HttpResponse```.

### What does this get me?

Well, first off I think it's beautiful. I know that's a subjective call, but the
data flowing neatly from top to bottom without huge nesting if cases and early
return values is very aesthetically pleasing to me.

More functionally it's easier to refactor with the help of the compiler. If I
want to add different return status codes to match different scenarios, the
compiler helps me out a lot more than if I'm adding an extra return case. If I
convert the left side to a HttpResponse early, the compiler will helpfully
remind me that the later flatMap calls cannot change ```Either<HttpResponse,
JsonNode>``` to ```Either<Exception, BusinessObject>```. Such changes are easily
fixed once the compiler has pointed it out, but extremely hard to find on your
own.

But most fundamentally is that we've encoded our code's states in the type
system, not variable states. The potential for JSON parsing to fail is encoded
in its type, not in the potential for a variable to null, or false, or for an
exception to have been thrown. You're leaning on the compiler to tell you if
you've handled the failure cases properly, as the code won't compile otherwise.
Now instead of testing for runtime exceptions you only test to make sure that
your business logic is correct.

If you've ever been interested in what the Haskell or Scala developers have been
talking about with functional type safety, I'd highly recommend taking a look at
Lambda to get a taste of it in Java.
