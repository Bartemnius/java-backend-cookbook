---
layout: default
title: volatile
parent: Intermediate And Advanced Java
grand_parent: Book
nav_order: 24
---

# volatile

## Simple Explanation

`volatile` is a Java keyword used for visibility between threads.

When one thread writes to a volatile variable, other threads are guaranteed to see the updated value when they read it.

Simple idea:

```text
volatile makes changes visible across threads.
```

But it does not make compound actions atomic.

That is the part many developers get wrong.

## Why It Exists

Modern CPUs and the JVM optimize heavily.

A thread may cache a value.

Operations may be reordered when the JVM is allowed to do so.

In single-threaded code, this usually does not matter.

In multithreaded code, it can matter a lot.

One thread may change a flag.

Another thread may keep seeing an old value.

`volatile` tells Java:

> Reads and writes of this variable must have special visibility guarantees.

## Basic Example

```java
class Worker {
    private volatile boolean running = true;

    public void run() {
        while (running) {
            // do work
        }
    }

    public void stop() {
        running = false;
    }
}
```

One thread runs the loop.

Another thread calls `stop()`.

Because `running` is volatile, the worker thread can see the updated value.

Without `volatile`, the worker thread may keep reading a cached value.

## Visibility, Not Atomicity

`volatile` does not make this safe:

```java
class Counter {
    private volatile int value;

    public void increment() {
        value++;
    }
}
```

This is still broken with multiple threads.

Why?

Because `value++` is not one operation.

It is:

1. read value
2. add one
3. write value

`volatile` makes each read and write visible.

It does not make the whole read-modify-write sequence atomic.

For counters, use synchronization or atomic classes such as `AtomicInteger`.

## Good Use Case: Stop Flag

Volatile is good for simple state flags.

Example:

```java
class BackgroundTask implements Runnable {
    private volatile boolean stopped;

    @Override
    public void run() {
        while (!stopped) {
            doOneUnitOfWork();
        }
    }

    public void stop() {
        stopped = true;
    }

    private void doOneUnitOfWork() {
        // work here
    }
}
```

This works because the update is simple.

One thread writes the flag.

Another thread reads the flag.

There is no compound update.

## Bad Use Case: Check Then Act

Volatile is not enough for check-then-act logic.

Bad:

```java
class LazyService {
    private volatile Service service;

    public Service getService() {
        if (service == null) {
            service = new Service();
        }
        return service;
    }
}
```

Two threads can both see `service == null`.

Then both can create a new `Service`.

`volatile` helps visibility.

It does not protect the whole check-then-act sequence.

You need synchronization or another safe initialization pattern.

## Happens-Before

A write to a volatile variable happens-before every later read of that same variable.

In practical terms:

If thread A writes to a volatile variable, and thread B later reads that volatile value, thread B can see the effects that happened before the write.

This is why volatile can be used for publication patterns.

But this is an advanced topic.

For most interviews, the key point is:

`volatile` gives visibility guarantees, not mutual exclusion.

## Volatile vs synchronized

| Feature | volatile | synchronized |
| --- | --- | --- |
| Visibility | Yes | Yes |
| Mutual exclusion | No | Yes |
| Compound action safety | No | Yes, if used correctly |
| Locking | No monitor lock | Uses monitor lock |
| Good for | simple flags/state publication | critical sections/invariants |

Do not say "`volatile` is a lightweight synchronized".

That is sloppy.

They solve overlapping but different problems.

## Common Interview Questions

### What does `volatile` do?

`volatile` ensures visibility of reads and writes across threads.

When one thread writes to a volatile variable, other threads reading it can see the latest value according to Java's memory model rules.

### Does `volatile` make operations atomic?

Simple volatile reads and writes are visible and atomic for the variable access itself.

But compound operations like `count++` are not atomic.

### Can volatile fix race conditions?

Sometimes, for very simple visibility-only cases.

But it does not fix race conditions that involve compound actions, invariants, or multiple variables.

### When would you use volatile?

Use it for simple flags, state indicators, and some safe publication patterns.

A common example is a stop flag checked by a background thread.

### What is the difference between `volatile` and `synchronized`?

`volatile` gives visibility without locking.

`synchronized` gives visibility and mutual exclusion.

Use `synchronized` when a group of operations must be protected as one critical section.

## Common Mistakes

### Using volatile for counters

This is a classic bug.

`volatile int count` with `count++` is not thread-safe.

Use `AtomicInteger`, `LongAdder`, or synchronization depending on the case.

### Thinking volatile locks the variable

It does not.

Multiple threads can read and write a volatile variable at the same time.

There is no mutual exclusion.

### Using volatile for multiple related fields

If several fields must stay consistent together, volatile is usually not enough.

You need a lock, immutable holder object, atomic reference, or another design.

### Using volatile to hide poor design

If you are adding volatile because the code "sometimes sees old values", stop and understand the ownership model.

Shared mutable state without a clear plan is weak engineering.

## Mid/Senior Notes

At junior level, know that volatile is about visibility.

At mid level, understand why it does not make `count++` safe.

At senior level, understand happens-before, safe publication, and when not to be clever.

Important production questions:

- Is there exactly one writer or many writers?
- Is the update a simple assignment or a compound action?
- Are multiple fields related by an invariant?
- Would immutability make this safer?
- Would an atomic class express the intent better?
- Would a lock make the code easier to reason about?

Volatile is useful.

It is also easy to misuse because it looks simple.

## When To Use It / When Not To Use It

Use `volatile` when:

- one thread writes a simple flag
- other threads read that flag
- no compound operation is needed
- no group of fields must be updated together

Do not use `volatile` when:

- you need `++`, `--`, or read-modify-write
- multiple variables must remain consistent
- you need exclusive access
- you do not fully understand why visibility is the problem

## Quick Summary

`volatile` gives visibility guarantees.

It does not provide mutual exclusion.

It does not make `count++` thread-safe.

It is useful for simple flags and state publication.

Use `synchronized`, locks, or atomic classes when you need compound actions to be safe.

