---
layout: default
title: Concurrent Collections
parent: Intermediate And Advanced Java
grand_parent: Book
nav_order: 29
---

# Concurrent Collections

## Simple Explanation

Concurrent collections are collections designed to be used safely by multiple threads.

Normal collections like `ArrayList` and `HashMap` are not safe for concurrent modification.

Concurrent collections provide safer alternatives.

Examples:

- `ConcurrentHashMap`
- `CopyOnWriteArrayList`
- `BlockingQueue`
- `ConcurrentLinkedQueue`

Simple idea:

```text
Use collections designed for concurrent access instead of manually locking normal collections everywhere.
```

## Why It Exists

Backend applications often have shared data structures:

- caches
- task queues
- active sessions
- in-memory registries
- metrics
- background processing buffers

If multiple threads modify a normal collection at the same time, bad things can happen:

- lost updates
- inconsistent reads
- `ConcurrentModificationException`
- corrupted internal state
- broken business rules

Concurrent collections exist to handle common concurrent access patterns more safely and efficiently.

## HashMap Is Not ConcurrentHashMap

`HashMap` is not thread-safe.

Bad:

```java
Map<String, Integer> counts = new HashMap<>();

counts.put("java", counts.getOrDefault("java", 0) + 1);
```

If many threads run this code, updates can be lost.

Switching to `ConcurrentHashMap` helps, but only if you use it correctly.

Better:

```java
ConcurrentHashMap<String, Integer> counts = new ConcurrentHashMap<>();

counts.merge("java", 1, Integer::sum);
```

`merge()` performs the update atomically for that key.

The method choice matters.

## ConcurrentHashMap

`ConcurrentHashMap` is a thread-safe map designed for concurrent access.

Basic example:

```java
ConcurrentHashMap<String, String> users = new ConcurrentHashMap<>();

users.put("1", "Alice");
users.putIfAbsent("2", "Bob");

String user = users.get("1");
```

Useful methods include:

- `putIfAbsent`
- `compute`
- `computeIfAbsent`
- `computeIfPresent`
- `merge`

These methods help avoid check-then-act races.

## putIfAbsent

Bad check-then-act:

```java
if (!map.containsKey(key)) {
    map.put(key, value);
}
```

Better:

```java
map.putIfAbsent(key, value);
```

`putIfAbsent()` makes the operation atomic for the map entry.

This is clearer and safer.

## computeIfAbsent

`computeIfAbsent()` creates a value only if the key is missing.

Example:

```java
ConcurrentHashMap<String, List<String>> groups = new ConcurrentHashMap<>();

List<String> admins = groups.computeIfAbsent("admins", key -> new ArrayList<>());
```

Be careful.

The map operation is concurrent, but the value you store may not be thread-safe.

In this example, `ArrayList` is still mutable and not thread-safe.

If multiple threads modify the same list, you still have a problem.

Concurrent collection outside does not magically make objects inside thread-safe.

## CopyOnWriteArrayList

`CopyOnWriteArrayList` is a list optimized for many reads and few writes.

When you modify it, it creates a new internal copy.

Example:

```java
CopyOnWriteArrayList<String> listeners = new CopyOnWriteArrayList<>();

listeners.add("email-listener");

for (String listener : listeners) {
    System.out.println(listener);
}
```

It can be useful for listener lists or configuration snapshots.

It is usually bad for write-heavy workloads.

Copying on every write can become expensive.

## BlockingQueue

`BlockingQueue` is useful for producer-consumer patterns.

One thread produces work.

Another thread consumes work.

Example:

```java
BlockingQueue<String> queue = new LinkedBlockingQueue<>();

queue.put("task-1");

String task = queue.take();
```

`put()` can wait if the queue is full.

`take()` waits if the queue is empty.

This makes it useful for coordination between threads.

For real systems, prefer bounded queues when you need backpressure.

Unbounded queues can hide overload until memory becomes the problem.

## ConcurrentLinkedQueue

`ConcurrentLinkedQueue` is a non-blocking thread-safe queue.

Example:

```java
ConcurrentLinkedQueue<String> queue = new ConcurrentLinkedQueue<>();

queue.offer("task");
String task = queue.poll();
```

It does not block when empty.

`poll()` returns `null` if no element is available.

Use it when non-blocking queue behavior fits the problem.

Use `BlockingQueue` when consumers should wait for work.

## Synchronized Wrappers

Java also has synchronized collection wrappers:

```java
List<String> list = Collections.synchronizedList(new ArrayList<>());
```

These wrappers synchronize individual method calls.

They are older and sometimes useful.

But they are not always the best choice.

Iteration still requires external synchronization:

```java
synchronized (list) {
    for (String item : list) {
        System.out.println(item);
    }
}
```

For many modern use cases, collections from `java.util.concurrent` are better.

## Thread-Safe Collection Does Not Mean Thread-Safe Workflow

This is the most important point.

A concurrent collection protects its own internal structure.

It does not automatically protect your whole business operation.

Example:

```java
if (stock.get(productId) > 0) {
    stock.put(productId, stock.get(productId) - 1);
}
```

Even if `stock` is a `ConcurrentHashMap`, this workflow is not safe.

The full operation is:

```text
check stock and decrement if available
```

That entire operation must be atomic.

You may need `compute()`, a lock, an atomic value, or a database transaction.

## Common Interview Questions

### What are concurrent collections?

Concurrent collections are collection implementations designed for safe and efficient access by multiple threads.

Examples include `ConcurrentHashMap`, `CopyOnWriteArrayList`, `BlockingQueue`, and `ConcurrentLinkedQueue`.

### Why not just use `HashMap` with multiple threads?

`HashMap` is not thread-safe.

Concurrent modification can cause lost updates, inconsistent behavior, and in older or unlucky cases even internal corruption.

Use proper synchronization or a concurrent map.

### What is `ConcurrentHashMap` good for?

It is good for concurrent key-value access, especially when multiple threads read and update different keys.

It also provides atomic methods such as `putIfAbsent`, `compute`, and `merge`.

### When would you use `CopyOnWriteArrayList`?

Use it when reads are very frequent and writes are rare.

Common examples include listener lists and mostly stable configuration lists.

Do not use it for write-heavy workloads.

### What is `BlockingQueue` used for?

`BlockingQueue` is used for producer-consumer communication.

Producers put items into the queue.

Consumers take items from it and can wait when no item is available.

### Does `ConcurrentHashMap` make compound business logic thread-safe?

No.

It makes map operations thread-safe.

You still need to protect larger workflows and invariants.

Use atomic map methods, locks, atomics, or database transactions depending on the case.

## Common Mistakes

### Using get-then-put instead of atomic map methods

Bad:

```java
map.put(key, map.get(key) + 1);
```

Better:

```java
map.merge(key, 1, Integer::sum);
```

Use the atomic operations the collection gives you.

### Storing unsafe mutable values

A `ConcurrentHashMap<String, ArrayList<String>>` does not make each `ArrayList` safe.

The map is concurrent.

The values may still be unsafe.

### Choosing CopyOnWriteArrayList for frequent writes

Writes copy the underlying array.

That can be expensive.

Use it only when the read-heavy pattern is real.

### Using unbounded queues by default

Unbounded queues can accumulate work forever.

That hides backpressure and can lead to memory problems.

Backend systems need a plan for overload.

## Mid/Senior Notes

At junior level, know that normal collections are usually not safe for concurrent modification.

At mid level, know common concurrent collections and their use cases.

At senior level, think about workload and invariants:

- Is this read-heavy or write-heavy?
- Are updates independent per key?
- Do values inside the collection need their own protection?
- Is queue growth bounded?
- What is the backpressure strategy?
- Does this protect only JVM memory or also database state?
- Is the collection hiding a missing architectural boundary?

Concurrent collections are tools.

They do not replace thinking about ownership, capacity, and correctness.

## When To Use It / When Not To Use It

Use concurrent collections when:

- multiple threads access the same collection
- the collection's atomic operations match your workflow
- you need a producer-consumer queue
- you want to avoid manual locking for common patterns

Be careful when:

- operations span multiple collection calls
- values stored inside are mutable
- business invariants involve more than one key or object
- queues may grow without bounds
- database consistency is also involved

## Quick Summary

Concurrent collections are designed for multi-threaded access.

`ConcurrentHashMap` is useful for concurrent maps and atomic per-key operations.

`CopyOnWriteArrayList` is useful for many reads and rare writes.

`BlockingQueue` is useful for producer-consumer patterns.

Thread-safe collections do not automatically make whole workflows thread-safe.

Protect the full invariant, not just the data structure.

