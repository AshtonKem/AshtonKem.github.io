---
layout: post
title: "jQuery Might Not Be a Monad, But it's Close"
date: 2014-01-13 06:28
comments: true
categories: [programming]
---

There is a joke in the Haskell community that every developer must write a Monad tutorial at least once. Now that I myself am beginning to understand them, it's my turn.

Before digging in too deep, it's very important to understand that the way Haskell uses Monads is inescapably tied to Haskell's method system. Haskell doesn't have objects in the traditional OOP sense, instead it has data types (which might be generic or not) and value constructors. Each type must have 1 or more constructors, and these can be exported separately if you wish to make the internals private. Functions do not belong to data types, but instead use polymorphism to work correctly. The main way that this happens is via a typeclass, similar to a Java interface, which a type may implement. Because of this flexibility it is very easy for this statically typed language to overload the Monad methods safely.

The main use of Monads is for programming glue and for helping out the compiler. The IO Monad is used for interacting with IO and letting the compiler know a function is not *pure*. The Maybe Monad is used to chain together computations that may be Null in a safe way. At a very high level all of these Monads provide two functions: the ability to take a "normal" value and make a Monad out of it, and the ability to take functions that take normal values and return Monads and chain them together.

A coworker of mine pointed out that that is very similar to what jQuery is trying to achieve with its method chaining. The ability to turn a regular DOM element into a jQuery object with the $() operator. And the Monad chaining operator feels very similar to how jQuery allows for you to chain methods together.

The main difference here is that jQuery methods all work within a jQuery object, so we aren't binding methods that take regular (DOM) elements and return Monadic (jQuery) objects, but the effect is very similar.

And with that, it's time we took a look at a real Monad. Haskell doesn't have a Null object like Java, so insead one is constructed using its type system. Below is the declaration for the Maybe type, used in Haskell to denote values which may be Null.

``` haskell
data  Maybe a  =  Nothing | Just a
  deriving (Eq, Ord)
```

In Haskell variables are lower cased, while types and constructors must be capitalized. So this creates a polymorphic (generic) type called Maybe with two constructors. So if we are writing a function that returns a Maybe Int, we could return Nothing (our equivalent of null) or Just 1 (or any other number). Anyone who wishes to use the result of a Maybe computation must explicitly deal with the Nothing case, or the compiler will complain, and may use pattern matching to pull the concrete value out of the Just constructor.

The "deriving (Eq, Ord)" is just to know the compiler to automatically define the equality (== and /=) and ordering (<, <=, >, >=) methods. There are a few typeclasses that the compiler can derive, such as the ones that allow for conversion to and from strings (Show and Read). Most people use these for simple types like Maybe, but will hand implenemnt something when working with more complex types.

But what if we want to chain together 3 functions that return Maybe Int? We will end up with this monstrocity:

``` haskell

routine :: Maybe int
routine = case functionZero 1 of
    Nothing -> Nothing
    Just int1 -> case functionOne int1 of
        Nothing -> Nothing
        Just int2 -> case functionTwo int2 of
            Nothing -> Nothing
            Just int3 -> functionThree int3
```

Every time we call one of our functions we must check and see if the result is Nothing before continuing. Obviously reworking this section to work with other functions would be a huge pain, so we can use the Maybe Monad to help us out.

In the case of maybe we will need to write two functions: return which will create a Maybe object, and >>= which will let us chain our functions together. Lets take a look at the Monad declaration for Maybe, shall we?

``` haskell

instance Monad Maybe where
    return x = Just x
    a >>= f = case a of
        Nothing -> Nothing
        Just x  -> f x
    fail _ = Nothing
```

I've borrowed and simplified the definition from [Learn You a Haskell For Great Good!](http://learnyouahaskell.com/a-fistful-of-monads#getting-our-feet-wet-with-maybe) because it's much easier to read than the official version. You should definitely buy their book, it's excellent.

So we get 3 functions: the unfortunately named return, >>= which is called bind, and fail. Return is easy, to turn a value into a Monadic value of type Maybe is to wrap it in the just constructor. The bind function checks the value passed into it, Nothing values are passed on, and any Just values are unwrapped and passed to the given function. Using these functions, the above work could have been written like this:

``` haskell
routine :: Maybe int
routine = (return 1) >>= functionZero >>= functionOne >>= functionTwo >>= functionThree

```

Which is starting to feel a bit like jQuery method chaining. Beyond the Maybe Monad there are all kinds of neat tricks that we can pull off with monads. There exist monads to fake the appearance of mutable state (something Haskell doesn't have), Monads to allow functions to be run with implicit environment variables, Monads to allow pure functions to output a log easily, the list goes on and on.

For further reading I strongly suggest the website/book [Learn You a Haskell For Great Good!](http://learnyouahaskell.com), which is my favorite resource for teaching people functional programming.
