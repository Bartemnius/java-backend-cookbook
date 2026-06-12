---
layout: default
title: Virtual Threads
parent: Intermediate And Advanced Java
grand_parent: Book
nav_order: 31
---

# Virtual Threads

## Simple Explanation

Virtual threads are lightweight Java threads.

They are still `java.lang.Thread` instances.

But they are not tied to one operating system thread for their whole lifetime.

Simple idea:

```text
Platform thread = expensive thread backed by an OS thread
Virtual thread  = cheap Java thread managed by the JDK
```

Virtual threads became a final feature in Java 21.

They are part of Project Loom.

They make it practical to write simple blocking code while supporting many concurrent tasks.

## Why It Exists

Traditional Java backend applications often use one platform thread per request.

This style is easy to understand:

```text
one request -> one thread -> normal blocking code
```

The problem is that platform threads are expensive.

They are backed by operating system threads.

You cannot create hundreds of thousands of them safely in a normal application.

So backend developers often use thread pools.

Thread pools help reuse expensive platform threads.

But they also limit concurrency.

If all pool threads are waiting for I/O, new work must wait.

Virtual threads solve a different problem:

They let you have many more concurrent tasks without needing many OS threads.

## Platform Threads vs Virtual Threads

Platform threads are traditional Java threads backed by OS threads.

Virtual threads are managed by the JDK and scheduled onto a smaller number of platform threads.

The platform thread that runs a virtual thread at a given moment is called a carrier thread.

A virtual thread can unmount from its carrier when it blocks on many JDK blocking operations.

Then the carrier can run another virtual thread.

This is why virtual threads are useful for blocking I/O.

The code looks blocking.

The OS thread does not need to stay blocked for the whole wait.

## Basic Example

You can start a virtual thread directly:

```java
Thread thread = Thread.startVirtualThread(() -> {
    System.out.println("Running in a virtual thread");
});

thread.join();
```

You can also use the builder API:

```java
Thread thread = Thread.ofVirtual()
        .name("request-handler")
        .start(() -> {
            System.out.println("Handling request");
        });

thread.join();
```

These examples are useful for learning.

In application code, you will often use an executor.

## Virtual Thread Per Task Executor

Java provides an executor that creates a new virtual thread for each submitted task.

```java
try (ExecutorService executor = Executors.newVirtualThreadPerTaskExecutor()) {
    Future<String> first = executor.submit(() -> fetchUser());
    Future<String> second = executor.submit(() -> fetchOrders());

    String result = first.get() + second.get();
}
```

The executor is closed at the end of the try-with-resources block.

This is different from a traditional fixed thread pool.

With virtual threads, creating a new thread per task is expected.

## Do Not Pool Virtual Threads

This is a major point.

Do not create a fixed pool of virtual threads.

Thread pools exist mainly because platform threads are expensive.

Virtual threads are cheap.

Pooling them usually defeats the point.

Bad idea:

```java
// Do not do this as a default pattern
ExecutorService executor = Executors.newFixedThreadPool(100, Thread.ofVirtual().factory());
```

Better:

```java
try (ExecutorService executor = Executors.newVirtualThreadPerTaskExecutor()) {
    executor.submit(() -> callExternalService());
}
```

If you need to limit access to a scarce resource, do not limit it by pooling virtual threads.

Use a proper limiter, such as a `Semaphore`, connection pool, rate limiter, or backpressure mechanism.

## Good Use Case: Blocking I/O

Virtual threads are useful when you have many concurrent tasks that spend most of their time waiting.

Examples:

- HTTP calls
- database queries
- file I/O
- calls to external APIs
- request-per-thread server code

Example:

```java
try (ExecutorService executor = Executors.newVirtualThreadPerTaskExecutor()) {
    List<Future<String>> futures = urls.stream()
            .map(url -> executor.submit(() -> fetch(url)))
            .toList();

    for (Future<String> future : futures) {
        System.out.println(future.get());
    }
}
```

This code is blocking and straightforward.

With virtual threads, that can be fine.

The goal is to keep simple sequential code while scaling concurrency.

## Bad Use Case: CPU-Bound Work

Virtual threads do not make CPU work faster.

If your task is CPU-heavy, the limit is CPU cores.

Examples:

- image processing
- compression
- encryption
- large calculations
- parsing huge files

Creating 100,000 virtual threads for CPU-heavy work does not create 100,000 CPU cores.

It can make scheduling overhead worse.

For CPU-bound work, use a bounded executor sized around available CPU capacity.

Virtual threads help with concurrency.

They do not magically improve raw computation speed.

## Blocking Is Not Always Free

Virtual threads make many blocking operations scale better.

But this does not mean blocking is free.

You still must think about:

- database connection pool size
- external API rate limits
- memory usage
- backpressure
- timeouts
- retries
- cancellation

If your database pool has 20 connections, then 10,000 virtual threads cannot all run database queries at once.

Most of them will wait.

That may be fine.

But it must be intentional.

## ThreadLocal Caution

Virtual threads support `ThreadLocal`.

But be careful.

With platform thread pools, some code stores expensive reusable objects in `ThreadLocal`.

With virtual-thread-per-task style, there may be a huge number of virtual threads.

Creating expensive per-thread state for each virtual thread can hurt performance and memory usage.

Do not use `ThreadLocal` as a hidden cache without thinking.

Prefer explicit ownership and scoped data where possible.

## Pinning

Pinning means a virtual thread cannot unmount from its carrier during some blocking operation.

When a virtual thread is pinned, it holds onto the carrier platform thread.

This reduces the scalability benefit.

Depending on the JDK version and blocking operation, blocking inside `synchronized` code can reduce the scalability benefit of virtual threads.

The practical advice is simple:

Avoid holding monitors while doing slow blocking work.

This was already good advice with platform threads.

Virtual threads make the cost easier to notice.

## Virtual Threads And Spring

Modern Java frameworks can use virtual threads to handle requests.

In a backend application, the framework may create one virtual thread per request.

That lets controller or service code stay simple and blocking:

```java
User user = userClient.fetchUser(id);
Orders orders = orderClient.fetchOrders(id);
```

This can be easier to read than reactive chains.

But virtual threads do not remove the need for:

- timeouts
- connection pool sizing
- transaction boundaries
- cancellation
- observability
- thread safety

They simplify the threading model.

They do not simplify every distributed systems problem.

## Common Interview Questions

### What are virtual threads?

Virtual threads are lightweight Java threads managed by the JDK.

They are instances of `java.lang.Thread`, but they are not tied to one OS thread for their entire lifetime.

They became a final feature in Java 21.

### Why were virtual threads introduced?

They were introduced to let server applications scale the simple thread-per-request style without requiring huge numbers of platform threads.

They make blocking I/O code more scalable.

### Are virtual threads faster than platform threads?

Not in the sense of executing CPU instructions faster.

They improve scalability and throughput for many concurrent waiting tasks.

They do not make CPU-bound work faster.

### Should virtual threads be pooled?

No, not as a normal pattern.

Virtual threads are cheap and are meant to be created per task.

Use semaphores, connection pools, or rate limiters to limit scarce resources.

### When should you use virtual threads?

Use them for large numbers of concurrent blocking tasks, especially I/O-heavy backend workloads.

They fit request-per-thread server code and external service/database calls.

### When should you not use virtual threads?

Do not expect them to improve CPU-bound work.

Also be careful when code depends heavily on `ThreadLocal`, holds locks during blocking work, or lacks timeouts and backpressure.

### Are virtual threads the same as reactive programming?

No.

Reactive programming uses a different async style.

Virtual threads let you keep straightforward blocking code while scaling concurrency better.

They can reduce the need for reactive code in many typical backend workloads.

## Common Mistakes

### Pooling virtual threads

This is the big one.

If you are pooling virtual threads because "threads should be pooled", you are applying an old platform-thread habit to a different tool.

Limit resources directly.

Do not pool cheap virtual threads to pretend you are controlling the database.

### Expecting CPU-bound speedups

Virtual threads are not magic CPU accelerators.

If the CPU is saturated, more threads usually do not help.

### Ignoring resource limits

Virtual threads let you create more concurrent tasks.

They do not give you more database connections, more memory, or unlimited external API capacity.

### Forgetting timeouts

A virtual thread waiting forever is still a task waiting forever.

Use timeouts.

Use cancellation.

Use backpressure.

### Treating virtual threads as a replacement for thread safety

Virtual threads are still threads.

They share memory.

Race conditions, deadlocks, visibility problems, and unsafe shared state still exist.

## Mid/Senior Notes

At junior level, know that virtual threads are lightweight threads introduced as a final feature in Java 21.

At mid level, understand why they help I/O-bound server workloads and why they should not be pooled.

At senior level, think about system design:

- What resource is actually limited?
- Are database and HTTP client pools sized correctly?
- Are timeouts enforced?
- Does the framework manage virtual threads for requests?
- Does any code rely on expensive `ThreadLocal` state?
- Is blocking inside locks avoided?
- Is observability ready for many more threads?
- Is reactive code still needed for this use case?

Virtual threads are a strong tool.

But if your system has bad timeouts, overloaded databases, and unclear ownership, virtual threads will not save it.

They may just let you reach the next bottleneck faster.

## When To Use It / When Not To Use It

Use virtual threads when:

- you have many concurrent I/O-bound tasks
- you want simple blocking code
- thread-per-request style fits the application
- platform thread pools are limiting throughput
- your libraries work well with blocking JDK APIs

Be careful or avoid them when:

- work is CPU-bound
- resource limits are not controlled
- code uses heavy `ThreadLocal` caches
- code blocks while holding locks
- the team does not understand timeout and backpressure behavior

## Quick Summary

Virtual threads are lightweight Java threads finalized in Java 21.

They preserve the simple thread-per-request programming model.

They are useful for many concurrent I/O-bound tasks.

They are not faster CPU threads.

Do not pool virtual threads.

Use resource limits directly with semaphores, connection pools, rate limiters, and backpressure.

Virtual threads are still threads, so normal concurrency rules still apply.
