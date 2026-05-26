---
layout: default
title: ArrayList Vs LinkedList
parent: Java Fundamentals
grand_parent: Book
nav_order: 36
---

# ArrayList Vs LinkedList

## Simple Explanation

`ArrayList` and `LinkedList` are both implementations of `List`.

They can both store ordered elements and allow duplicates.

But internally they work differently.

Simple version:

```text
ArrayList  -> backed by an array
LinkedList -> backed by nodes linked together
```

In most normal Java application code, `ArrayList` is the better default.

## Why It Exists

Different list implementations optimize different operations.

`ArrayList` is good at:

- fast access by index,
- iteration,
- appending at the end,
- memory locality.

`LinkedList` is theoretically good at:

- adding/removing at the beginning or middle when you already have the node,
- queue/deque-like operations.

But in real Java code, `LinkedList` is often slower than people expect because each element is a separate node object and memory locality is worse.

## ArrayList

`ArrayList` uses an internal array.

Example:

```java
List<String> names = new ArrayList<>();

names.add("Anna");
names.add("Tom");
names.add("Maria");

System.out.println(names.get(1)); // Tom
```

Access by index is fast:

```java
names.get(100);
```

Why?

Because arrays can jump directly to an index.

When an `ArrayList` runs out of internal space, it creates a larger array and copies elements.

That resize is more expensive, but it does not happen on every add.

## LinkedList

`LinkedList` stores elements as nodes.

Each node points to the previous and next node.

Simple mental model:

```text
Anna <-> Tom <-> Maria
```

Example:

```java
List<String> names = new LinkedList<>();

names.add("Anna");
names.add("Tom");
names.add("Maria");
```

Getting by index is slower:

```java
names.get(100);
```

Why?

Because the list must walk through nodes to reach that position.

## Common Interview Questions

### What is the difference between `ArrayList` and `LinkedList`?

Good answer:

```text
ArrayList is backed by an array, so indexed access and iteration are usually fast.
LinkedList is backed by linked nodes, so indexed access is slower and it uses more
memory per element. In most application code, ArrayList is the better default.
```

### Which one is faster?

It depends on the operation, but `ArrayList` is usually faster for typical application use:

- reading by index,
- iterating,
- appending,
- general business collections.

`LinkedList` can be useful for queue/deque operations, but `ArrayDeque` is often better for that.

### Why is `LinkedList` not used as often?

Because it has overhead:

- each element needs a node object,
- worse CPU cache locality,
- slower indexed access,
- more memory usage.

The theoretical Big O story does not always match real performance.

### What happens when `ArrayList` grows?

It creates a larger internal array and copies existing elements.

This resize is occasional, not every time.

## Common Mistakes

Mistake 1: Saying `LinkedList` is always better for insertions.

That is too simplistic.

To insert in the middle, you first need to find the position. Finding it is often O(n).

Mistake 2: Choosing based only on Big O tables.

Big O ignores constants, memory locality, object allocation, and CPU cache behavior.

Real performance can be different.

Mistake 3: Using `LinkedList` as the default list.

In most business code, use `ArrayList` unless you have a specific reason not to.

## Mid/Senior Notes

The honest practical answer:

```text
Use ArrayList by default.
```

That is not dogma. It is experience.

`ArrayList` is simple, memory-efficient, and fast for most real workloads.

`LinkedList` is useful much less often than beginner tutorials suggest.

For queue behavior, prefer:

```java
Deque<Task> tasks = new ArrayDeque<>();
```

Instead of:

```java
Queue<Task> tasks = new LinkedList<>();
```

`ArrayDeque` is usually a better queue/deque implementation than `LinkedList`.

Senior-level answer:

```text
ArrayList is my default List implementation. I only use LinkedList when I have a
specific measured reason or a true linked-list use case. Big O alone is not enough;
memory layout and real access patterns matter.
```

## Quick Summary

`ArrayList` is backed by an array.

`LinkedList` is backed by linked nodes.

`ArrayList` is usually the better default.

`LinkedList` has more memory overhead and slower indexed access.

For queues, consider `ArrayDeque`.

