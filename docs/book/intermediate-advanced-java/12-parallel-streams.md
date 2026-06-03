---
layout: default
title: Parallel Streams
parent: Intermediate And Advanced Java
grand_parent: Book
nav_order: 12
---

# Parallel Streams

## Simple Explanation

A parallel stream processes data using multiple threads.

Example:

```java
List<Result> results = items.parallelStream()
        .map(this::process)
        .toList();
```

This looks like a simple way to make code faster.

Sometimes it helps.

Sometimes it makes things worse.

Parallel streams are not magic performance buttons.

## Why It Exists

Some operations can be split into independent pieces.

Example:

```text
Process 1 million independent numbers.
```

If each item can be processed independently, multiple CPU cores may help.

Parallel streams make it easy to express that kind of parallel processing.

But not all work is good for parallel streams.

## How It Works

Parallel streams use the ForkJoinPool common pool by default.

This means your parallel stream shares threads with other code that also uses the common pool.

That matters because blocking operations can hurt unrelated work.

Example:

```java
items.parallelStream()
        .map(item -> callRemoteApi(item))
        .toList();
```

This is risky.

Remote API calls are blocking IO.

Parallel streams are usually better for CPU-bound work than blocking IO.

## Good Use Case

Potentially reasonable:

```java
long count = numbers.parallelStream()
        .filter(this::isPrime)
        .count();
```

Why?

- CPU-bound,
- independent elements,
- large enough dataset,
- no shared mutable state,
- no blocking IO.

Even then, measure.

## Bad Use Case

Risky:

```java
List<UserData> data = users.parallelStream()
        .map(user -> externalClient.fetchData(user.getId()))
        .toList();
```

Why risky?

- blocking IO,
- shared common pool,
- possible rate limit issues,
- harder error handling,
- harder tracing/debugging,
- no clear timeout/backpressure model.

For IO concurrency, use explicit async/concurrency tools instead.

## Shared Mutable State Problem

Bad:

```java
List<String> emails = new ArrayList<>();

users.parallelStream()
        .forEach(user -> emails.add(user.getEmail()));
```

This is unsafe.

Multiple threads modify the same `ArrayList`.

Better:

```java
List<String> emails = users.parallelStream()
        .map(User::getEmail)
        .toList();
```

Avoid shared mutable state.

## Common Interview Questions

### What is a parallel stream?

Good answer:

```text
A parallel stream processes stream elements using multiple threads, usually from
the ForkJoinPool common pool. It can help for CPU-bound independent work, but it
is not automatically faster and can be risky with blocking IO or shared mutable state.
```

### Are parallel streams always faster?

No.

They have overhead.

For small datasets or cheap operations, they can be slower.

### Why are parallel streams risky with IO?

Because blocking IO can occupy common pool threads and interfere with other work.

Also, IO usually needs explicit timeouts, limits, retries, and backpressure.

### What is the common pool?

The ForkJoinPool common pool is a shared pool used by parallel streams by default.

This shared nature is one reason to be careful.

## Common Mistakes

Mistake 1: Adding `.parallel()` because code is slow.

That is guessing.

Measure first.

Mistake 2: Using parallel streams with shared mutable state.

This can create race conditions.

Mistake 3: Using parallel streams for remote API/database calls.

Usually use explicit concurrency tools instead.

Mistake 4: Forgetting ordering.

Parallel processing can affect ordering and performance expectations.

If order matters, be careful.

## Mid/Senior Notes

Parallel streams are good interview material because they reveal maturity.

A weak answer:

```text
Use parallelStream to make streams faster.
```

A better answer:

```text
Parallel streams can help for large CPU-bound independent tasks, but they have
overhead and use the common ForkJoinPool by default. I avoid them for blocking IO,
shared mutable state, and code that needs careful control over timeouts or concurrency.
```

For backend services, explicit concurrency is often clearer:

- `ExecutorService`,
- `CompletableFuture`,
- virtual threads,
- reactive APIs where appropriate,
- message queues,
- batch processing frameworks.

Senior-level rule:

```text
Do not use parallel streams as a generic performance fix.
```

## Quick Summary

Parallel streams process elements on multiple threads.

They usually use the ForkJoinPool common pool.

They may help for large CPU-bound independent work.

They are risky with blocking IO and shared mutable state.

They are not always faster.

Measure before using them.
