---
layout: default
title: Garbage Collection Basics
parent: Intermediate And Advanced Java
grand_parent: Book
nav_order: 4
---

# Garbage Collection Basics

## Simple Explanation

Garbage collection is automatic memory cleanup.

In Java, you create objects:

```java
User user = new User("anna@example.com");
```

But you do not manually free them.

The garbage collector removes objects that are no longer reachable.

Simple rule:

```text
If no live code can reach an object anymore, it can be garbage collected.
```

## Why It Exists

Manual memory management is error-prone.

In languages without automatic garbage collection, developers often need to allocate and free memory manually.

That can lead to:

- memory leaks,
- use-after-free bugs,
- double-free bugs,
- crashes,
- security issues.

Java avoids many of these problems by letting the JVM manage memory automatically.

But automatic does not mean magic.

You can still create memory leaks by keeping references to objects you no longer need.

## Reachability

Garbage collection is based on reachability.

Example:

```java
public void process() {
    User user = new User("anna@example.com");
    System.out.println(user.email());
}
```

After `process()` finishes, the local variable `user` disappears.

If nothing else references that `User`, the object becomes eligible for garbage collection.

Eligible does not mean immediately removed.

It means the garbage collector may remove it when it runs.

## Memory Leak In Java

Java can still have memory leaks.

Example:

```java
public class UserCache {
    private static final Map<Long, User> USERS = new HashMap<>();

    public static void add(User user) {
        USERS.put(user.id(), user);
    }
}
```

If this map grows forever and never removes old users, those users stay reachable.

The garbage collector cannot remove them.

That is a memory leak.

## Generational Idea

Many garbage collectors use a generational idea:

```text
Most objects die young.
Some objects live longer.
```

Temporary objects are created all the time:

```java
String message = "User " + id + " logged in";
```

Many short-lived objects disappear quickly.

The JVM can optimize garbage collection by treating young and old objects differently.

You do not need every internal detail for a basic interview, but the intuition matters.

## Stop-The-World

Some garbage collection work may pause application threads.

This is called a stop-the-world pause.

During such a pause, application code temporarily stops while the JVM does GC work.

Modern garbage collectors try to keep pauses small, but pauses can still matter in low-latency systems.

## Common Interview Questions

### What is garbage collection?

Good answer:

```text
Garbage collection is automatic memory management. The JVM finds objects that are
no longer reachable and frees their memory so it can be reused.
```

### Can Java have memory leaks?

Yes.

Good answer:

```text
Java can have memory leaks when objects are no longer needed but are still reachable,
for example through static collections, unbounded caches, listeners, or ThreadLocals.
The garbage collector cannot remove reachable objects.
```

### When is an object eligible for garbage collection?

When it is no longer reachable from live code.

Example:

```java
user = null;
```

If no other references exist, the previous object may become eligible for GC.

### Does calling `System.gc()` force garbage collection?

No.

It suggests that the JVM should run garbage collection, but the JVM is not required to do it immediately.

In normal application code, you should not rely on `System.gc()`.

### What is a stop-the-world pause?

A pause where application threads are temporarily stopped while the JVM performs some garbage collection work.

## Common Mistakes

Mistake 1: Thinking Java cannot leak memory.

It can.

Reachable but unused objects are still retained.

Mistake 2: Thinking setting everything to `null` is good style.

Usually, it is unnecessary.

Let variables go out of scope naturally.

Mistake 3: Calling `System.gc()` manually.

This is usually a bad idea in application code.

Mistake 4: Ignoring unbounded caches.

Caches need limits or eviction strategies.

Otherwise they become memory leaks with a nicer name.

## Mid/Senior Notes

Garbage collection matters for production behavior.

Symptoms of GC problems:

- high memory usage,
- frequent GC cycles,
- long pauses,
- latency spikes,
- low throughput,
- `OutOfMemoryError`.

Common causes:

- unbounded caches,
- too many temporary objects,
- large object graphs,
- static references,
- listener leaks,
- ThreadLocal leaks,
- loading too much data into memory.

Senior-level answer:

```text
I treat GC as automatic but not free. I try to avoid unnecessary object retention,
unbounded caches, and loading huge datasets into memory. If production has memory
or latency issues, I look at heap usage, GC logs, allocation rate, and object
retention.
```

## Quick Summary

Garbage collection removes unreachable objects.

Reachable objects cannot be collected.

Java can still have memory leaks.

Objects become eligible for GC when no live references can reach them.

Some GC work can cause stop-the-world pauses.

Do not rely on `System.gc()`.

Unbounded caches are a common source of memory leaks.
