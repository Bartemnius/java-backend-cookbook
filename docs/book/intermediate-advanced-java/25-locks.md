---
layout: default
title: Locks
parent: Intermediate And Advanced Java
grand_parent: Book
nav_order: 25
---

# Locks

## Simple Explanation

A lock controls access to shared data.

If one thread owns the lock, other threads must wait before entering the protected code.

Java has built-in monitor locks through `synchronized`.

Java also has explicit lock classes in `java.util.concurrent.locks`.

The most common one is `ReentrantLock`.

Simple idea:

```text
Lock before touching shared state.
Unlock when finished.
```

## Why It Exists

`synchronized` is simple and useful.

But sometimes you need more control.

Explicit locks can support features like:

- trying to acquire a lock without waiting forever
- waiting for a lock with a timeout
- interruptible lock acquisition
- multiple condition queues
- optional fairness policies

These features are useful in more advanced concurrent code.

But they also make code easier to mess up.

If you forget to unlock, your application can get stuck.

## ReentrantLock

`ReentrantLock` is an explicit lock.

Example:

```java
class Counter {
    private final Lock lock = new ReentrantLock();
    private int value;

    public void increment() {
        lock.lock();
        try {
            value++;
        } finally {
            lock.unlock();
        }
    }

    public int getValue() {
        lock.lock();
        try {
            return value;
        } finally {
            lock.unlock();
        }
    }
}
```

The `finally` block is not optional.

If an exception happens and you do not unlock, the lock may remain held forever.

That can block every other thread waiting for it.

## Why `finally` Matters

Bad:

```java
lock.lock();
doWork();
lock.unlock();
```

If `doWork()` throws an exception, `unlock()` is never called.

Better:

```java
lock.lock();
try {
    doWork();
} finally {
    lock.unlock();
}
```

This is the standard pattern.

If someone writes explicit lock code without `finally`, challenge it immediately.

That is not a small style issue.

It is a correctness issue.

## tryLock

`tryLock()` tries to acquire the lock without waiting forever.

Example:

```java
if (lock.tryLock()) {
    try {
        doWork();
    } finally {
        lock.unlock();
    }
} else {
    System.out.println("Could not acquire lock");
}
```

This can help avoid waiting indefinitely.

There is also a timed version:

```java
if (lock.tryLock(1, TimeUnit.SECONDS)) {
    try {
        doWork();
    } finally {
        lock.unlock();
    }
}
```

Timed lock acquisition can be useful when you would rather fail or retry than block forever.

## lockInterruptibly

`lockInterruptibly()` waits for the lock but allows interruption.

Example:

```java
lock.lockInterruptibly();
try {
    doWork();
} finally {
    lock.unlock();
}
```

This matters when a thread should be able to respond to cancellation while waiting for a lock.

With plain `lock()`, waiting for the lock is not interrupted in the same way.

## Reentrant Means Reusable By Same Thread

`ReentrantLock` is reentrant.

This means the same thread can acquire the same lock multiple times.

But it must unlock the same number of times.

Example:

```java
lock.lock();
try {
    lock.lock();
    try {
        doWork();
    } finally {
        lock.unlock();
    }
} finally {
    lock.unlock();
}
```

This is legal.

But nested locking can become hard to read.

Do not make locking more complicated than necessary.

## ReadWriteLock

`ReadWriteLock` separates read access from write access.

Many threads can hold the read lock at the same time.

Only one thread can hold the write lock.

Example:

```java
class Settings {
    private final ReadWriteLock lock = new ReentrantReadWriteLock();
    private final Map<String, String> values = new HashMap<>();

    public String get(String key) {
        lock.readLock().lock();
        try {
            return values.get(key);
        } finally {
            lock.readLock().unlock();
        }
    }

    public void put(String key, String value) {
        lock.writeLock().lock();
        try {
            values.put(key, value);
        } finally {
            lock.writeLock().unlock();
        }
    }
}
```

This can help when reads are frequent and writes are rare.

But it is not automatically faster.

Measure before assuming.

For many cases, a concurrent collection or immutable snapshot is simpler.

## Conditions

`Condition` is a more flexible alternative to `wait()` and `notify()`.

It works with explicit locks.

Example:

```java
class SimpleSignal {
    private final Lock lock = new ReentrantLock();
    private final Condition ready = lock.newCondition();
    private boolean hasValue;

    public void awaitValue() throws InterruptedException {
        lock.lock();
        try {
            while (!hasValue) {
                ready.await();
            }
        } finally {
            lock.unlock();
        }
    }

    public void signalValue() {
        lock.lock();
        try {
            hasValue = true;
            ready.signalAll();
        } finally {
            lock.unlock();
        }
    }
}
```

Notice the `while`, not `if`.

Waiting conditions should be checked in a loop because of spurious wakeups and because another thread may change the condition before this thread continues.

## Locks vs synchronized

| Feature | synchronized | ReentrantLock |
| --- | --- | --- |
| Basic mutual exclusion | Yes | Yes |
| Automatic unlock on block exit | Yes | No |
| Try lock | No | Yes |
| Timed lock wait | No | Yes |
| Interruptible lock wait | Limited | Yes |
| Multiple conditions | No | Yes |
| Simplicity | Higher | Lower |

If you do not need advanced features, `synchronized` is often enough.

Explicit locks are not "more professional" by default.

They are just more flexible and easier to misuse.

## Common Interview Questions

### What is a lock?

A lock is a synchronization mechanism that controls access to shared resources.

Only threads that acquire the lock may enter the protected critical section.

### What is `ReentrantLock`?

`ReentrantLock` is an explicit lock implementation from `java.util.concurrent.locks`.

It supports manual lock and unlock, try-lock, timed lock acquisition, interruptible acquisition, and conditions.

### Why must `unlock()` be in a `finally` block?

Because code inside the critical section may throw an exception.

The `finally` block ensures the lock is released even when something fails.

### What is the difference between `synchronized` and `ReentrantLock`?

Both can provide mutual exclusion.

`synchronized` is simpler and automatically releases the monitor when leaving the block.

`ReentrantLock` is explicit and supports more advanced features like `tryLock()`, timed waits, interruptible lock acquisition, and multiple conditions.

### What is `ReadWriteLock`?

`ReadWriteLock` separates read and write locking.

Multiple readers can access the protected data at the same time, but writers need exclusive access.

It can help when reads are frequent and writes are rare.

## Common Mistakes

### Forgetting to unlock

This is the big one.

Always use `try/finally`.

No excuses.

### Using explicit locks when synchronized is enough

More advanced syntax does not mean better code.

If `synchronized` solves the problem clearly, use it.

### Holding locks during slow I/O

Do not hold a lock while calling a database, HTTP API, or slow file operation unless the design absolutely requires it.

This can destroy throughput.

### Assuming ReadWriteLock is always faster

It is not.

It has overhead.

It helps only for certain read-heavy workloads.

For simple cases, it can be worse or just harder to read.

### Waiting with `if` instead of `while`

When using conditions, check the condition in a loop.

Threads can wake up even when the condition is not truly satisfied.

## Mid/Senior Notes

At junior level, know the basic lock/unlock idea.

At mid level, understand `try/finally`, `tryLock()`, and when explicit locks are useful.

At senior level, think about system behavior:

- Is the lock protecting a clear invariant?
- Is lock ordering defined?
- Can this deadlock?
- Can a timeout avoid waiting forever?
- Does the lock create a bottleneck?
- Can the design avoid shared mutable state?
- Would a queue, actor-like ownership, immutable snapshot, or concurrent collection be simpler?

Locks should protect a specific rule.

If nobody can say what invariant a lock protects, the code is already suspicious.

## When To Use It / When Not To Use It

Use explicit locks when:

- you need `tryLock()`
- you need timed lock acquisition
- you need interruptible lock acquisition
- you need multiple conditions
- you need read/write locking

Prefer simpler tools when:

- `synchronized` is enough
- a concurrent collection solves the problem
- immutable data avoids locking
- the code would become hard to reason about

## Quick Summary

Locks protect critical sections.

`ReentrantLock` gives more control than `synchronized`.

Always unlock in a `finally` block.

`tryLock()` can avoid waiting forever.

`ReadWriteLock` can help read-heavy workloads, but it is not automatically faster.

Explicit locks are powerful, but they demand discipline.

