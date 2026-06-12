---
layout: default
title: Deadlocks
parent: Intermediate And Advanced Java
grand_parent: Book
nav_order: 27
---

# Deadlocks

## Simple Explanation

A deadlock happens when threads wait for each other forever.

Thread A holds one lock and waits for another lock.

Thread B holds the second lock and waits for the first lock.

Neither thread can continue.

Simple idea:

```text
I wait for you.
You wait for me.
Nobody moves.
```

## Why It Exists

Deadlocks happen when code uses multiple locks without a safe locking strategy.

Locks are useful because they protect shared data.

But when several locks are involved, order matters.

If different threads acquire the same locks in different orders, they can trap each other.

## Basic Deadlock Example

```java
Object firstLock = new Object();
Object secondLock = new Object();

Thread first = new Thread(() -> {
    synchronized (firstLock) {
        synchronized (secondLock) {
            System.out.println("First thread done");
        }
    }
});

Thread second = new Thread(() -> {
    synchronized (secondLock) {
        synchronized (firstLock) {
            System.out.println("Second thread done");
        }
    }
});

first.start();
second.start();
```

Possible execution:

```text
Thread A acquires firstLock
Thread B acquires secondLock
Thread A waits for secondLock
Thread B waits for firstLock
```

Now both threads wait forever.

## Necessary Conditions

Deadlocks usually need these conditions:

- mutual exclusion
- hold and wait
- no preemption
- circular wait

You do not need to recite this like a robot.

But you should understand it.

Mutual exclusion means a resource can be held by only one thread at a time.

Hold and wait means a thread holds one resource while waiting for another.

No preemption means a lock cannot be forcibly taken away safely.

Circular wait means thread A waits for B, while B waits for A, directly or indirectly.

## Prevent Deadlocks With Lock Ordering

The most common prevention technique is consistent lock ordering.

If every thread always acquires locks in the same order, circular waiting is much less likely.

Example:

```java
synchronized (firstLock) {
    synchronized (secondLock) {
        doWork();
    }
}
```

Every code path must use the same order:

```text
firstLock -> secondLock
```

Not sometimes this:

```text
secondLock -> firstLock
```

Lock ordering must be a rule, not a suggestion.

## Prevent Deadlocks With tryLock

Explicit locks can use `tryLock()`.

This lets code avoid waiting forever.

Example:

```java
if (firstLock.tryLock(1, TimeUnit.SECONDS)) {
    try {
        if (secondLock.tryLock(1, TimeUnit.SECONDS)) {
            try {
                doWork();
            } finally {
                secondLock.unlock();
            }
        }
    } finally {
        firstLock.unlock();
    }
}
```

This is more complex.

It can be useful when failing or retrying is better than waiting forever.

But do not use complexity as decoration.

If simple lock ordering works, use that.

## Deadlocks In Backend Applications

Deadlocks are not only Java monitor problems.

They also happen in databases.

Example:

Request A:

```text
locks order 1
then locks order 2
```

Request B:

```text
locks order 2
then locks order 1
```

The database may detect a deadlock and abort one transaction.

This is one reason transaction order matters.

Backend developers must understand both JVM locks and database locks.

## How To Detect Deadlocks

In Java, thread dumps can show deadlocks.

Tools and commands depend on the environment, but common options include:

- IDE thread dump tools
- `jcmd`
- `jstack`
- monitoring platforms
- application server diagnostics

A thread dump can show which thread owns a lock and which thread waits for it.

When production is stuck, this is often more useful than staring at source code.

## Common Interview Questions

### What is a deadlock?

A deadlock is a situation where two or more threads wait for each other forever, usually because each holds a lock that another thread needs.

### How can you prevent deadlocks?

Common techniques include consistent lock ordering, keeping critical sections small, avoiding nested locks when possible, using timeouts with `tryLock()`, and reducing shared mutable state.

### How do you debug a Java deadlock?

Take a thread dump and inspect thread states, stack traces, and lock ownership.

Java diagnostic tools can often identify deadlocked threads.

### Can deadlocks happen in databases?

Yes.

Database transactions can deadlock when they lock rows or tables in conflicting orders.

Databases often detect this and abort one transaction.

### Does using synchronized automatically create deadlocks?

No.

`synchronized` itself is not the problem.

Deadlocks come from unsafe locking patterns, especially multiple locks acquired in inconsistent order.

## Common Mistakes

### Acquiring locks in random order

This is the classic deadlock recipe.

If multiple locks are needed, define and follow a consistent order.

### Holding locks too long

Long critical sections increase the chance of contention and deadlock.

Avoid slow I/O while holding locks.

### Ignoring database deadlocks

A backend can be deadlock-free in Java and still deadlock in the database.

Transactions need the same discipline around ordering and scope.

### Thinking timeout is a complete fix

Timeouts can prevent waiting forever.

They do not automatically make the operation correct.

You still need retry, rollback, cleanup, and clear failure behavior.

## Mid/Senior Notes

At junior level, know the two-lock example.

At mid level, understand lock ordering and thread dumps.

At senior level, think about deadlocks across boundaries:

- JVM locks
- database locks
- distributed locks
- message processing
- external service calls
- transaction boundaries

Deadlock prevention is mostly design discipline.

If your code requires many nested locks, stop and question the model.

You may need clearer ownership, smaller critical sections, queues, immutable data, or database-level constraints.

## When To Use It / When Not To Use It

You prevent deadlocks by:

- avoiding unnecessary locks
- avoiding nested locks where possible
- enforcing lock ordering
- keeping critical sections small
- using timeouts when appropriate
- designing rollback and retry behavior

Do not rely on luck.

Concurrent code that "probably will not deadlock" is not good enough.

## Quick Summary

A deadlock happens when threads wait for each other forever.

The classic cause is acquiring multiple locks in inconsistent order.

Consistent lock ordering is the main prevention technique.

`tryLock()` with timeout can help, but it adds complexity.

Thread dumps are important for debugging Java deadlocks.

Database deadlocks matter for backend developers too.

