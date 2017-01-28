---
layout: post
title: "MDC And Thread Pools, Bad Combination"
date: 2017-01-28 12:14
comments: true
categories: java programming logging debugging
---

A common problem in web services is good logging. Logs should be clear, easy to parse, have enough data, and give the reader some sense of context.

That last point is the stickler for me. If I have multiple concurrent web requests, I want to be able to correlate them back to the individual web request that caused that log event to happen, especially if it's correlated with down-stream web and database requests. 

On paper this is easy. Generate a UUID for each request and use the [MDC](https://logback.qos.ch/manual/mdc.html) or equivalent from your library to set this value. Since the MDC is basically a thread-local hashmap, you can access its values in the pattern statement for your logs. Conveniently enough each new thread that's spawned is given its parents' MDC values, making logging a snap!

Except it's not that easy. The documentation and MDC code assumes that one of the following is true for any thread in your system:

1) That it's the sole thread for a request that will get its MDC set first thing
2) That it is spawned by a request thread, and will inherit any request specific MDC data.

What's missing from this list? Worker thread pools!

In modern asynchronous Java, it's common for threads to be created and reused many times, often in ways that you don't have access to. Heck, the built in CompletableFuture<T> will reuse threads from the ForkJoinPool, which you don't have access to!

And what happens if instead of creating new threads your request thread gets an old thread from a pool? Well that new thread will inherit any MDC values from a previous request! 

Here's some code that shows the problem:

``` java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.slf4j.MDC;

import java.util.concurrent.CompletableFuture;

public class Main {

    public static final String CORRELATION_ID = "correlation-id";
    private static Logger logger;

    public static CompletableFuture<String> stepOne(String correlationId) {
        return CompletableFuture.supplyAsync(() -> {
            MDC.put(CORRELATION_ID, correlationId);
            logger.info("Step One");
            return "Foo";
        });
    }

    public static CompletableFuture<String> stepTwo(CompletableFuture<String> previous) {
        return previous.thenApplyAsync(x -> {
            logger.info("Step Two");
            return x + "Bar";
        });
    }

    public static CompletableFuture<String> stepThree(CompletableFuture<String> previous) {
        return previous.thenApplyAsync(x -> {
            logger.info("Step Three");
            return x + "Baz";
        });
    }

    public static String doAll(String correlationId) {
        return stepThree(stepTwo(stepOne(correlationId))).join();
    }

    public static void main(String[] args) throws InterruptedException {
        logger = LoggerFactory.getLogger(Main.class);
        doAll("correlation-id-one");
        doAll("correlation-id-two");
    }
}
```

And here's some bad log output.

```
12:13:55.465 [ForkJoinPool.commonPool-worker-1] INFO  correlation-id=correlation-id-one - Step One
12:13:55.466 [ForkJoinPool.commonPool-worker-1] INFO  correlation-id=correlation-id-one - Step Two
12:13:55.466 [ForkJoinPool.commonPool-worker-1] INFO  correlation-id=correlation-id-one - Step Three
12:13:55.467 [ForkJoinPool.commonPool-worker-1] INFO  correlation-id=correlation-id-two - Step One
12:13:55.467 [ForkJoinPool.commonPool-worker-1] INFO  correlation-id=correlation-id-two - Step Two
12:13:55.467 [ForkJoinPool.commonPool-worker-2] INFO  correlation-id=correlation-id-one - Step Three

```

