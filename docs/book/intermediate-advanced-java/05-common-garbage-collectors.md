---
layout: default
title: Common Garbage Collectors
parent: Intermediate And Advanced Java
grand_parent: Book
nav_order: 5
---

# Common Garbage Collectors

## Simple Explanation

A garbage collector is the part of the JVM that finds unused objects and frees memory.

But there is not only one garbage collector.

The JVM has different garbage collectors optimized for different goals:

```text
throughput
low latency
small heaps
large heaps
short pauses
simple behavior
```

You do not need to memorize every collector flag for most Java backend interviews.

But you should understand that different collectors make different tradeoffs.

## Why It Exists

Different applications care about different things.

A batch job may care mostly about total throughput:

```text
Finish the job as fast as possible.
```

A trading system or user-facing API may care about latency:

```text
Avoid long pauses.
```

A small CLI tool may care about simplicity and low memory usage.

That is why Java offers multiple garbage collectors.

## Common Collectors

### Serial GC

Serial GC is simple and uses a single thread for garbage collection.

It can be useful for small applications or environments with limited resources.

It is usually not the default choice for serious backend services.

### Parallel GC

Parallel GC uses multiple threads for garbage collection.

It focuses on throughput.

This means it tries to maximize total work done by the application over time.

The tradeoff is that pauses can be longer.

### G1 GC

G1 means Garbage-First.

It is a general-purpose garbage collector designed to balance throughput and pause time.

For many modern server applications, G1 is a common default choice.

It divides the heap into regions and tries to collect regions with the most garbage first.

### ZGC

ZGC is designed for very low pause times, even with large heaps.

It does much of its work concurrently with application threads.

It is useful when latency matters a lot.

### Shenandoah

Shenandoah is another low-pause garbage collector.

Like ZGC, it tries to reduce pause times by doing more work concurrently.

Availability can depend on JDK distribution and version.

## Throughput vs Latency

This is the key tradeoff.

Throughput means:

```text
How much useful work does the application complete overall?
```

Latency means:

```text
How long does one operation take?
```

A throughput-focused collector may allow longer pauses if the application completes more total work.

A latency-focused collector tries to avoid long pauses, sometimes with extra CPU overhead.

## Common Interview Questions

### What is the role of a garbage collector?

Good answer:

```text
A garbage collector automatically frees memory used by objects that are no longer
reachable. Different collectors make different tradeoffs between throughput,
latency, pause times, and resource usage.
```

### What is G1 GC?

Good answer:

```text
G1 is a general-purpose garbage collector that divides the heap into regions and
tries to balance throughput with predictable pause times. It is commonly used for
server-side Java applications.
```

### What is the difference between throughput and latency in GC?

Throughput is about total useful work completed over time.

Latency is about response time and pauses.

GC tuning often means choosing the right tradeoff.

### What are ZGC and Shenandoah used for?

They are low-pause collectors useful for applications where latency matters and long GC pauses are unacceptable.

## Common Mistakes

Mistake 1: Thinking one garbage collector is always best.

There is no universal best collector.

It depends on the application.

Mistake 2: Tuning GC before measuring.

Guessing JVM flags is usually a bad idea.

Measure first.

Mistake 3: Thinking low-pause collectors are free.

They reduce pause times, but may use more CPU or have different tradeoffs.

## Mid/Senior Notes

In production, GC choice should be driven by evidence:

- GC logs,
- latency metrics,
- heap usage,
- allocation rate,
- pause times,
- throughput requirements,
- memory limits,
- container environment.

For many backend services, the default collector is good enough.

Do not tune GC because you read a blog post.

Senior-level answer:

```text
I usually start with the JVM defaults unless there is a measured problem. If we
see latency spikes, high allocation rate, or long GC pauses, I inspect GC logs and
metrics before changing collector or flags.
```

## Quick Summary

Different garbage collectors optimize different goals.

Parallel GC focuses on throughput.

G1 is a common general-purpose server collector.

ZGC and Shenandoah focus on low pause times.

There is no universally best collector.

Measure before tuning.
