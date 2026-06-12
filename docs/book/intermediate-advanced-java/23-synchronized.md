---
layout: default
title: synchronized
parent: Intermediate And Advanced Java
grand_parent: Book
nav_order: 23
---

# synchronized

## Simple Explanation

`synchronized` is Java's built-in way to protect code that must be used by only one thread at a time.

It uses a lock connected to an object.

When one thread enters synchronized code, it owns the lock.

Other threads that need the same lock must wait.

Simple idea:

```text
Only one thread at a time may enter this protected section.
```

This protected section is called a critical section.

## Why It Exists

Threads share memory.

That means two threads can read and modify the same object at the same time.

Without coordination, this can break data.

Example:

```java
class Counter {
    private int value;

    public void increment() {
        value++;
    }

    public int getValue() {
        return value;
    }
}
```

`value++` looks like one operation.

It is not.

It is roughly:

1. read current value
2. add one
3. write new value

Two threads can interleave these steps and lose updates.

`synchronized` helps by making sure only one thread executes the protected code at once.

## Synchronized Method

You can synchronize an instance method:

```java
class Counter {
    private int value;

    public synchronized void increment() {
        value++;
    }

    public synchronized int getValue() {
        return value;
    }
}
```

For an instance method, the lock is `this`.

This means only one thread at a time can execute any synchronized instance method on the same object.

Different `Counter` objects have different locks.

## Synchronized Block

You can also synchronize a smaller block of code:

```java
class Counter {
    private final Object lock = new Object();
    private int value;

    public void increment() {
        synchronized (lock) {
            value++;
        }
    }

    public int getValue() {
        synchronized (lock) {
            return value;
        }
    }
}
```

This is often better than synchronizing the whole method.

It makes the lock object explicit.

It also lets you keep the critical section smaller.

Smaller critical sections usually reduce contention.

## Static Synchronized Method

A static synchronized method uses the class object as the lock.

Example:

```java
class GlobalCounter {
    private static int value;

    public static synchronized void increment() {
        value++;
    }
}
```

This locks on `GlobalCounter.class`.

It is not the same lock as an instance synchronized method.

Be careful with static synchronization.

It protects class-level state, not object-level state.

## Visibility

`synchronized` does two important things:

- mutual exclusion
- visibility

Mutual exclusion means only one thread enters the protected section at a time.

Visibility means changes made by one thread become visible to another thread that later acquires the same lock.

This matters because CPUs and the JVM can cache values and reorder operations.

Without a happens-before relationship, one thread may not see another thread's latest writes.

`synchronized` creates that relationship when used consistently with the same lock.

## Lock Object Choice

Lock choice matters.

This is usually good:

```java
private final Object lock = new Object();
```

This is often weak:

```java
synchronized (this) {
    // protected code
}
```

Synchronizing on `this` is not always wrong.

But external code can also synchronize on your object.

That can create accidental lock contention or deadlocks.

A private lock object gives the class better control.

Avoid synchronizing on strings:

```java
synchronized ("LOCK") {
    // bad idea
}
```

String literals are interned and may be shared across unrelated code.

## Reentrancy

Java intrinsic locks are reentrant.

This means a thread that already owns a lock can acquire the same lock again.

Example:

```java
class Service {
    public synchronized void first() {
        second();
    }

    public synchronized void second() {
        System.out.println("Still works");
    }
}
```

The same thread can enter `second()` while already holding the lock from `first()`.

Without reentrancy, this would deadlock itself.

## Basic Code Example

```java
class SafeCounter {
    private int value;

    public synchronized void increment() {
        value++;
    }

    public synchronized int getValue() {
        return value;
    }
}
```

This class protects both reading and writing with the same lock.

That is important.

Synchronizing only writes but not reads can still create visibility problems.

## Common Interview Questions

### What does `synchronized` do?

`synchronized` protects a critical section using a monitor lock.

Only one thread at a time can execute synchronized code guarded by the same lock.

It also provides visibility guarantees between threads that use the same lock.

### What lock does a synchronized instance method use?

It uses `this`, the current object, as the lock.

### What lock does a static synchronized method use?

It uses the class object, for example `MyClass.class`.

### Is `synchronized` only about atomicity?

No.

It provides mutual exclusion and visibility.

Both matter in concurrent code.

### Is `synchronized` reentrant?

Yes.

The same thread can acquire the same monitor lock multiple times.

### Does `synchronized` make an entire object thread-safe?

Not automatically.

Thread safety depends on whether all shared mutable state is protected consistently and whether the class exposes unsafe references.

## Common Mistakes

### Synchronizing only half of the access

Protecting writes but not reads is often broken.

If shared state is guarded by a lock, use the same lock consistently.

### Locking on public objects

Locking on `this`, public fields, or objects passed from outside can expose your lock to unknown code.

That makes deadlocks and contention harder to reason about.

### Making critical sections too large

Do not put slow I/O inside synchronized blocks unless you have a very strong reason.

Holding a lock while calling a database, HTTP service, or file system can block many other threads.

### Thinking `synchronized` solves all concurrency problems

It solves some problems.

It can also create others:

- lock contention
- deadlocks
- poor throughput
- hidden coupling

Good concurrent design often avoids shared mutable state instead of locking everything.

## Mid/Senior Notes

At junior level, know that `synchronized` allows one thread at a time.

At mid level, understand monitor locks, visibility, reentrancy, and consistent lock usage.

At senior level, ask design questions:

- What invariant is protected?
- What lock protects it?
- Is the critical section minimal?
- Could immutable data remove the need for locking?
- Could a concurrent collection solve this more cleanly?
- Could lock ordering prevent deadlocks?
- Is this code holding a lock while doing slow I/O?

Locks are a tool.

They are not architecture.

If the locking strategy is hard to explain, the design is probably too fragile.

## When To Use It / When Not To Use It

Use `synchronized` when:

- shared mutable state needs simple protection
- the critical section is small
- monitor lock behavior is enough
- code clarity matters more than advanced lock features

Avoid or reconsider it when:

- you need timed lock acquisition
- you need interruptible lock acquisition
- you need multiple conditions
- contention is high
- the code holds locks while doing slow external calls

In those cases, `java.util.concurrent` tools may fit better.

## Quick Summary

`synchronized` protects code with a monitor lock.

Instance synchronized methods lock on `this`.

Static synchronized methods lock on the class object.

Synchronized blocks can use explicit lock objects.

`synchronized` provides mutual exclusion and visibility.

Use the same lock consistently for related shared state.

Keep critical sections small.

