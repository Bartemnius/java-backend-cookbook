---
layout: default
title: Race Conditions
parent: Intermediate And Advanced Java
grand_parent: Book
nav_order: 26
---

# Race Conditions

## Simple Explanation

A race condition happens when the result of a program depends on the timing of multiple threads.

The code may look correct.

It may pass tests many times.

Then it fails when threads run in a different order.

Simple idea:

```text
The program is racing against thread timing.
Different timing can produce different results.
```

Race conditions are one of the most common and dangerous concurrency bugs.

## Why It Exists

Threads can interleave their operations.

One thread can start an operation.

Another thread can run before the first thread finishes.

If both threads touch the same mutable data, the final result can be wrong.

This is especially dangerous because source code order is not the same as execution order across threads.

The CPU scheduler decides when each thread runs.

Your code does not fully control that timing.

## Basic Broken Example

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

Now imagine two threads call `increment()` at the same time.

`value++` is not one atomic operation.

It is roughly:

1. read `value`
2. add one
3. write the new value

Bad interleaving:

```text
value = 0

Thread A reads 0
Thread B reads 0
Thread A writes 1
Thread B writes 1
```

Expected result: `2`

Actual result: `1`

One update was lost.

This is called a lost update.

## Race Conditions Are Timing Bugs

Race conditions are painful because they may not happen every time.

The same code can work:

- on your machine
- during a demo
- in unit tests
- under light load

Then it fails:

- in production
- under high traffic
- on a different CPU
- after adding logging
- after changing JVM options

This is why "it works on my machine" is a weak argument for concurrent code.

## Check-Then-Act Race

Another common race condition is check-then-act.

Bad:

```java
if (!users.contains(username)) {
    users.add(username);
}
```

Two threads can both check and see that the user is missing.

Then both can add it.

The problem is not the individual check or add.

The problem is that the full operation must be atomic:

```text
check and add if missing
```

If that whole sequence is not protected, there is a race.

## Read-Modify-Write Race

`value++` is a read-modify-write operation.

Other examples:

```java
balance = balance - amount;
items.put(key, items.get(key) + 1);
index = index + 1;
```

These operations read current state, compute a new value, and write it back.

If multiple threads do that at the same time without coordination, updates can be lost.

## Fix With synchronized

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

This makes `increment()` atomic with respect to other synchronized methods using the same lock.

It also gives visibility guarantees.

## Fix With AtomicInteger

For simple counters, atomic classes are often better:

```java
class Counter {
    private final AtomicInteger value = new AtomicInteger();

    public void increment() {
        value.incrementAndGet();
    }

    public int getValue() {
        return value.get();
    }
}
```

`AtomicInteger` provides atomic operations without using `synchronized` directly.

It is good for simple numeric state.

It is not a magic replacement for all locking.

## Fix With Better Design

The best fix is often to avoid shared mutable state.

Examples:

- use immutable objects
- keep data local to one request
- use message queues
- give one thread ownership of a piece of state
- use database constraints and transactions
- use concurrent collections correctly

Do not solve every race condition by throwing locks everywhere.

Sometimes that only hides a poor design.

## Common Interview Questions

### What is a race condition?

A race condition happens when the correctness of code depends on the timing or interleaving of multiple threads.

If different thread scheduling produces different results, the code likely has a race condition.

### Why is `count++` not thread-safe?

Because it is a read-modify-write operation.

Multiple threads can read the same old value and then write the same new value, causing lost updates.

### How can you prevent race conditions?

Common ways include synchronization, locks, atomic classes, concurrent collections, immutability, thread confinement, and database transactions.

The right choice depends on what shared state and invariant must be protected.

### Is volatile enough to fix `count++`?

No.

`volatile` gives visibility guarantees, but `count++` is still not atomic.

Use `AtomicInteger`, `synchronized`, or another proper coordination mechanism.

### Why are race conditions hard to debug?

Because they depend on timing.

Small changes like logging, slower machines, or different load can change the interleaving and hide or reveal the bug.

## Common Mistakes

### Testing only once and assuming it is safe

Concurrency bugs may appear rarely.

A passing test does not prove thread safety unless the design is actually correct.

### Protecting one method but not the full invariant

Thread safety is not just method-level locking.

You must protect the full rule that must remain true.

### Using volatile for compound operations

`volatile` does not make check-then-act or read-modify-write sequences atomic.

### Ignoring database-level races

Race conditions are not only in Java memory.

They also happen with databases:

- two requests update the same row
- duplicate registration happens
- inventory is oversold
- idempotency is missing

Backend developers must think across the whole system, not only inside one JVM.

## Mid/Senior Notes

At junior level, know the counter example.

At mid level, recognize check-then-act and read-modify-write bugs.

At senior level, think in terms of invariants and boundaries:

- What rule must always remain true?
- Where is the shared state?
- Is the state protected in memory, database, or both?
- Can two requests perform the same operation concurrently?
- Is the operation idempotent?
- Do we need optimistic or pessimistic locking?
- Is a unique constraint the real fix?

The senior move is not "add synchronized".

The senior move is to identify the invariant and protect it at the correct layer.

## When To Use It / When Not To Use It

You do not use race conditions.

You prevent them.

Use synchronization tools when shared mutable state is unavoidable.

Prefer designs that reduce sharing:

- immutable data
- request-scoped objects
- message passing
- clear ownership
- database constraints
- atomic operations for simple state

## Quick Summary

A race condition happens when thread timing affects correctness.

`count++` is not thread-safe because it is read-modify-write.

`volatile` does not make compound actions atomic.

Race conditions can be rare and hard to reproduce.

Prevent them by protecting shared state and invariants with the right mechanism.

