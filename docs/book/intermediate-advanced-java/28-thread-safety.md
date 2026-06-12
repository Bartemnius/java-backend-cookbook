---
layout: default
title: Thread Safety
parent: Intermediate And Advanced Java
grand_parent: Book
nav_order: 28
---

# Thread Safety

## Simple Explanation

Code is thread-safe if it works correctly when used by multiple threads at the same time.

That sounds simple.

It is not.

Thread safety means the code keeps its rules even when operations overlap.

Simple idea:

```text
Multiple threads can use this code without breaking its state or behavior.
```

## Why It Exists

Backend applications handle many things concurrently.

Examples:

- many HTTP requests
- scheduled jobs
- async tasks
- message consumers
- database callbacks
- cache updates

If shared objects are not thread-safe, one request can corrupt data used by another request.

This can cause bugs that are rare, serious, and hard to reproduce.

## What Makes Code Unsafe

Thread safety problems usually involve shared mutable state.

Shared means multiple threads can access it.

Mutable means it can change.

Examples:

```java
class OrderStats {
    private int totalOrders;

    public void recordOrder() {
        totalOrders++;
    }
}
```

If many threads call `recordOrder()`, updates can be lost.

The class is not thread-safe.

## Thread-Safe Counter

One fix is synchronization:

```java
class OrderStats {
    private int totalOrders;

    public synchronized void recordOrder() {
        totalOrders++;
    }

    public synchronized int getTotalOrders() {
        return totalOrders;
    }
}
```

Another fix is an atomic class:

```java
class OrderStats {
    private final AtomicInteger totalOrders = new AtomicInteger();

    public void recordOrder() {
        totalOrders.incrementAndGet();
    }

    public int getTotalOrders() {
        return totalOrders.get();
    }
}
```

Both can be valid.

The better choice depends on the invariant.

For a simple counter, `AtomicInteger` is usually clearer.

For multiple related fields, a lock may be better.

## Stateless Objects

Stateless objects are naturally thread-safe.

Example:

```java
class PriceCalculator {
    public BigDecimal calculate(BigDecimal net, BigDecimal taxRate) {
        return net.add(net.multiply(taxRate));
    }
}
```

This class has no fields.

It does not store shared mutable state.

Many threads can call it safely.

This is one reason stateless services are common in backend applications.

## Immutable Objects

Immutable objects are also thread-safe when designed correctly.

Example:

```java
public record Money(BigDecimal amount, String currency) {
}
```

The object cannot change after creation.

Many threads can read it safely.

Immutability is one of the best concurrency tools because it removes whole categories of bugs.

## Thread Confinement

Thread confinement means data is used by only one thread.

If only one thread can access the object, no synchronization is needed for that object.

Examples:

- local variables inside a method
- request-scoped objects
- objects owned by one worker thread
- data processed inside one message handler and not shared

Local variables are usually safe because each thread has its own stack.

But the object referenced by a local variable can still be shared if you pass it somewhere else.

Do not oversimplify this.

## Thread-Safe Collections

Some collections are designed for concurrent use.

Examples:

- `ConcurrentHashMap`
- `CopyOnWriteArrayList`
- `BlockingQueue`

But using a thread-safe collection does not automatically make your whole operation thread-safe.

Example:

```java
if (!map.containsKey(key)) {
    map.put(key, value);
}
```

Even with `ConcurrentHashMap`, this check-then-act sequence is not ideal.

Use atomic methods:

```java
map.putIfAbsent(key, value);
```

Thread-safe building blocks still require correct composition.

## Thread Safety Is About Invariants

An invariant is a rule that must always remain true.

Example:

```text
account balance must never become negative
```

Thread safety means concurrent operations cannot break that rule.

This is stronger than "no exception was thrown".

Example:

```java
class Account {
    private BigDecimal balance;

    public void withdraw(BigDecimal amount) {
        if (balance.compareTo(amount) >= 0) {
            balance = balance.subtract(amount);
        }
    }
}
```

This is not thread-safe.

Two threads can both pass the balance check and both withdraw.

The invariant can break.

## Common Strategies

Common ways to achieve thread safety:

- immutability
- stateless design
- thread confinement
- synchronization
- explicit locks
- atomic classes
- concurrent collections
- database transactions
- message queues
- clear ownership of mutable state

There is no single best tool.

The correct tool depends on the state and invariant.

## Common Interview Questions

### What does thread-safe mean?

Thread-safe code behaves correctly when accessed by multiple threads at the same time.

It protects its state and invariants from unsafe concurrent access.

### Is a stateless class thread-safe?

Usually yes, if it does not use shared mutable state through other objects.

A class with no fields and no side effects is naturally safe to call from many threads.

### Are immutable objects thread-safe?

Properly immutable objects are thread-safe because their state cannot change after construction.

They can be shared safely between threads.

### Does using `ConcurrentHashMap` make code thread-safe?

It makes individual map operations thread-safe.

It does not automatically make larger multi-step logic thread-safe.

Use atomic map methods like `putIfAbsent()` or protect the full operation.

### How do you make a class thread-safe?

First identify shared mutable state and invariants.

Then choose an approach such as immutability, confinement, synchronization, atomic classes, or concurrent collections.

Do not blindly add locks without understanding what rule you are protecting.

## Common Mistakes

### Thinking no fields means no problems always

A class can have no fields but still use shared mutable static state, external caches, files, databases, or injected mutable collaborators.

Stateless is safe only if the full behavior is stateless or properly coordinated.

### Assuming thread-safe collections solve everything

They protect collection internals.

They do not automatically protect your business invariant.

### Exposing mutable internal state

Bad:

```java
class Team {
    private final List<String> members = new ArrayList<>();

    public List<String> getMembers() {
        return members;
    }
}
```

External code can modify the list without the class knowing.

This breaks encapsulation and can break thread safety.

### Mixing synchronization strategies

Do not protect the same state sometimes with `synchronized`, sometimes with `ReentrantLock`, and sometimes not at all.

That is chaos.

Use one clear strategy for one piece of state.

## Mid/Senior Notes

At junior level, know shared mutable state is dangerous.

At mid level, understand immutability, confinement, locks, atomics, and concurrent collections.

At senior level, thread safety is about ownership and invariants:

- Who owns this state?
- Can it be immutable?
- Can it be request-scoped?
- What exact invariant must hold?
- Is the invariant local to one JVM or stored in the database?
- Can two requests race through different application instances?
- Is thread safety enough, or do we need distributed consistency?

This is where backend thinking gets real.

A Java object can be thread-safe inside one JVM and still fail when two application instances update the same database row.

Do not stop your reasoning too early.

## When To Use It / When Not To Use It

You need thread safety when objects or data can be accessed by multiple threads.

You may not need synchronization when:

- data is local to one method
- data is request-scoped and not shared
- objects are immutable
- one thread clearly owns the state

But be honest.

Guessing that "probably only one thread uses this" is not engineering.

Verify ownership.

## Quick Summary

Thread-safe code works correctly under concurrent access.

The main danger is shared mutable state.

Stateless and immutable objects are naturally safer.

Thread-safe collections help, but they do not protect whole business workflows automatically.

Always identify the invariant and protect it at the correct level.

