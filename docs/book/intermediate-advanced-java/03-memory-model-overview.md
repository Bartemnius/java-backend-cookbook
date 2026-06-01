---
layout: default
title: Memory Model Overview
parent: Intermediate And Advanced Java
grand_parent: Book
nav_order: 3
---

# Memory Model Overview

## Simple Explanation

Java programs use memory in different ways.

The most important areas to understand are:

```text
Heap      -> objects
Stack     -> method calls and local variables
Metaspace -> class metadata
```

This is the practical memory model most Java backend developers need first.

There is also the Java Memory Model, usually called JMM, which describes how threads see changes made by other threads.

That is a deeper concurrency topic and gets its own section later.

This page is about JVM memory areas.

## Why It Exists

The JVM needs memory for different kinds of data:

- objects created with `new`,
- method calls,
- local variables,
- loaded class definitions,
- static metadata,
- native calls,
- thread execution state.

Understanding this helps you debug:

- memory leaks,
- `OutOfMemoryError`,
- `StackOverflowError`,
- garbage collection pressure,
- too many loaded classes,
- recursive call problems.

## Heap

The heap stores objects.

Example:

```java
User user = new User("anna@example.com");
```

Simplified:

```text
Stack:
  user reference

Heap:
  User object
```

The heap is shared by threads.

Garbage collection mainly works on heap memory.

If too many reachable objects stay on the heap, you can get:

```text
OutOfMemoryError: Java heap space
```

## Stack

Each thread has its own stack.

The stack stores method call frames.

Each method call gets a frame containing local variables and execution state.

Example:

```java
public void first() {
    second();
}

public void second() {
    third();
}

public void third() {
}
```

Simplified stack:

```text
third()
second()
first()
```

When a method finishes, its stack frame is removed.

Too many nested calls can cause:

```text
StackOverflowError
```

This often happens with infinite recursion.

## Metaspace

Metaspace stores class metadata.

This includes information about loaded classes, methods, fields, and related runtime metadata.

Before Java 8, similar data lived in PermGen.

In modern Java, we usually talk about Metaspace.

Too many loaded classes or class loader leaks can cause metaspace problems.

## Static Fields

Static fields belong to the class, not to one object.

Example:

```java
public class Counter {
    static int totalCount;
}
```

The class metadata is associated with metaspace, but static field values for normal object references are stored in memory managed by the JVM and can reference heap objects.

For interviews, do not overcomplicate it.

Better simple answer:

```text
Static fields belong to the class, not an instance. They can keep objects reachable,
so careless static references can cause memory leaks.
```

## Common Interview Questions

### What is stored on the heap?

Objects are stored on the heap.

The heap is shared by threads and managed by garbage collection.

### What is stored on the stack?

Method call frames and local variables.

Each thread has its own stack.

### What is `StackOverflowError`?

It usually means the stack ran out of space, often because of infinite or very deep recursion.

### What is `OutOfMemoryError`?

It means the JVM could not allocate required memory.

Common types include heap space errors and metaspace errors.

### What is Metaspace?

Metaspace stores class metadata in modern Java.

It replaced PermGen from older Java versions.

## Common Mistakes

Mistake 1: Saying all primitives are on the stack.

Local primitive variables are associated with stack frames.

But primitive fields inside objects are part of those heap objects.

Mistake 2: Thinking garbage collection cleans everything.

Garbage collection removes unreachable heap objects.

It cannot remove objects that are still reachable through references.

Mistake 3: Ignoring static references.

Static collections can accidentally keep objects alive forever.

Example:

```java
private static final List<User> USERS = new ArrayList<>();
```

If this grows forever, it can leak memory.

## Mid/Senior Notes

Production memory issues usually require looking beyond simple definitions.

Useful questions:

- Is heap usage growing over time?
- Are objects being retained unexpectedly?
- Are there too many loaded classes?
- Are thread stacks too large or too many threads created?
- Is garbage collection running too often?
- Is the application caching without limits?

Senior-level answer:

```text
I think about JVM memory in terms of heap for objects, stack for thread call frames,
and metaspace for class metadata. For production issues, I care about reachability,
GC pressure, static references, class loader leaks, and unbounded caches.
```

## Quick Summary

Heap stores objects.

Stack stores method call frames and local variables.

Each thread has its own stack.

Metaspace stores class metadata.

`StackOverflowError` often comes from deep recursion.

`OutOfMemoryError` often means heap, metaspace, or native memory pressure.

Static references can keep objects alive.
