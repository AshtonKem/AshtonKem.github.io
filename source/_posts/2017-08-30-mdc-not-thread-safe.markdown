---
layout: post
title: "MDC and Thread Pools: a Bad Combination"
date: 2017-08-30 06:17
comments: true
categories: java programming logging debugging
---

Well written web services must have clear and easy to follow logs. Without good logs a programmer will be unable to diagnose or reproduce problems that might arise. And while many systems will keep logs in their reverse proxy like Nginx, a well written system will also have application/domain specific logs written within the application itself.

A common goal of application logs is to tie a given log line to a specific request. Because modern servers are multi-threaded, it is possible for multiple requests to be serviced at the same time, resulting in log messages from different requests being intermingled in the log output.

According to all the documentation I have found, the standard way to resolve this is to use the Mapped Diagnostic Context, or [MDC](https://logback.qos.ch/manual/mdc.html). The MDC wraps a thread local hashmap, whose values can be used in your log patterns. On paper you could generate a UUID per request, insert it into the MDC and presto you have a UUID per request in each log line! Since threads are created per request, the MDC will be empty each time.

Except, that's not really how Java programming works these days. Threads are expensive, and so for the sake of efficiency modern Java aims to minimize the number of threads created. This means using non-blocking IO (NIO), or reusing threads via Thread Pools where possible, which avoids creating new threads. As such there are quite a few number of APIs in Java 8 and some modern web servers that make it trivial to make your code non-blocking and use a common thread pool.

So, what happens to our MDC when we reuse threads? Well, we end up with the wrong MDC! Here's some code for Java 8 that shows the problem:

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

This code is using the new `CompletableFuture` class from Java 8, which is *roughly* analogous to Javascript promises. What we do is set the correlation-id, then execute a sequence of asynchronous actions and log the output. If you run this code enough times, you will eventually get the output below:

```
12:13:55.465 [ForkJoinPool.commonPool-worker-1] INFO  correlation-id=correlation-id-one - Step One
12:13:55.466 [ForkJoinPool.commonPool-worker-1] INFO  correlation-id=correlation-id-one - Step Two
12:13:55.466 [ForkJoinPool.commonPool-worker-1] INFO  correlation-id=correlation-id-one - Step Three
12:13:55.467 [ForkJoinPool.commonPool-worker-1] INFO  correlation-id=correlation-id-two - Step One
12:13:55.467 [ForkJoinPool.commonPool-worker-1] INFO  correlation-id=correlation-id-two - Step Two
12:13:55.467 [ForkJoinPool.commonPool-worker-2] INFO  correlation-id=correlation-id-one - Step Three

```


Pay attention to the last line, which should have a correlation-id of correlation-id-two. Instead we have an earlier correlation-id because we happened to reuse a thread from earlier. This is an unmitigated disaster, as now you don't have missing logs, you have misleading logs. In a less-simple scenario it would be absolutely impossible to tell whether or not that correlation-id was correct, and you might end up diagnosing bugs that don't exist within your system because of this.

Java 8 and `CompletableFuture` make this much more plausible by making it very easy to use the `ForkJoinPool`. `CompletableFuture` provides a number of methods to create a new `CompletableFuture`, or to take one `CompletableFuture` and transform it into another. Most of these methods have at least two signatures: `x` and `xAsync`. The former will do all the work on the current thread, the latter will automatically use the `ForkJoinPool` to execute asynchronously. Optionally you can provide your own `Executor`, which might be backed by your own managed thread pool.

From a performance standpoint, this is amazing. I can separate the business logic from the execution strategies completely. With very minor code changes, sometimes only a method name, I can turn synchronous code to asynchronous or back again. But from a logging stand point this is very fragile. Correctly using the MDC depends on staying on the current thread, and Java 8 makes it very easy to hop threads by design. Worse, the above incorrectness is subtle, so your logs could be left incorrect for years without any immediate impact.

## Recommendations

Don't use the MDC, it's too unsafe. Every time you change threads, you will either get an empty MDC (best case scenario), or MDC values from an older request (worst case scenario). At every single point where you might hop threads, it will be necessary to copy the current MDC settings to a local variable, close over it in your next lambda, and set the new thread's MDC to the correct value before logging on the new thread. If any new asynchronous step is introduced without doing the above work, the correct values are lost for the rest of the request resulting in malformed or incorrect logs. Considering that adding "Async" to the end of a method name is enough to hop threads, this is a lot of work.

The easiest solution is to forgo logging request specific identifiers. Remove any reference to the MDC from your code and log configuration, and move on. If your request volume is relatively low, this might be a perfectly reasonable solution for you.

If you must track every single action down to a single request, you will need to pass around an object for each request which will contain any request specific information you want to log. You can setup logging interfaces that require the `Request` object to ensure log uniformity, which means that failing to pass this object will be a compilation error. As a side benefit, these custom logging interfaces would be extremely useful in enforcing consistent log messages, and a good place to inject statistics gathering.

## Addendum

The MDC docs are incorrect, with sections of the documentation explicitly referencing out of date information. I draw your attention to the section titled "MDC and Managed Threads" which says the following:

> A copy of the mapped diagnostic context can not always be inherited by worker threads from the initiating thread. This is the case when java.util.concurrent.Executors is used for thread management. For instance, newCachedThreadPool method creates a ThreadPoolExecutor and like other thread pooling code, it has intricate thread creation logic.


As it turns out, a copy of the mapped diagnostic context will **never** be inherited by a worker thread. [This](https://github.com/qos-ch/logback/commit/aa7d584ecdb1638bfc4c7223f4a5ff92d5ee6273) change guarantees that each thread gets a new, empty MDC. This documentation is correct for older version of logback, but it is not correct anymore. I'm sure the quote above was accidentally left in place, as the correct behavior is mentioned at the top of the document. It's just a good reminder to read documentation carefully, and understand that humans can make mistakes in the docs too.
