---
layout: default
title: Iterators
parent: Java Fundamentals
grand_parent: Book
nav_order: 40
---

# Iterators

## Simple Explanation

An iterator is an object that lets you go through a collection one element at a time.

Example:

```java
Iterator<String> iterator = names.iterator();

while (iterator.hasNext()) {
    String name = iterator.next();
    System.out.println(name);
}
```

Simple meaning:

```text
hasNext() -> is there another element?
next()    -> give me the next element
```

## Why It Exists

Different collections store data differently.

An `ArrayList` uses an array.

A `HashSet` uses hash buckets.

A `TreeSet` uses a tree.

You should not need to know internal details just to loop over elements.

Iterator gives a common way to traverse collections.

## Basic Code Example

```java
List<String> names = new ArrayList<>();

names.add("Anna");
names.add("Tom");
names.add("Maria");

Iterator<String> iterator = names.iterator();

while (iterator.hasNext()) {
    String name = iterator.next();
    System.out.println(name);
}
```

Enhanced for-loop uses an iterator behind the scenes:

```java
for (String name : names) {
    System.out.println(name);
}
```

This is cleaner for simple iteration.

## Removing Elements Safely

Iterator has a `remove()` method.

This is useful when you want to remove elements while iterating.

Correct:

```java
Iterator<String> iterator = names.iterator();

while (iterator.hasNext()) {
    String name = iterator.next();

    if (name.startsWith("A")) {
        iterator.remove();
    }
}
```

Wrong:

```java
for (String name : names) {
    if (name.startsWith("A")) {
        names.remove(name);
    }
}
```

This can throw `ConcurrentModificationException`.

## Fail-Fast Behavior

Many Java collection iterators are fail-fast.

This means they try to detect structural changes to the collection while iteration is happening.

Example:

```java
for (String name : names) {
    names.add("New name"); // can throw ConcurrentModificationException
}
```

This does not mean the collection is thread-safe.

It means the iterator noticed that the collection was modified unexpectedly.

## Common Interview Questions

### What is an iterator?

Good answer:

```text
An iterator is an object used to traverse a collection one element at a time.
It provides methods like hasNext and next, and can remove elements safely during
iteration using iterator.remove().
```

### What is `ConcurrentModificationException`?

It is an exception thrown when a collection is structurally modified while it is being iterated in an unsafe way.

Example:

```java
for (String name : names) {
    names.remove(name);
}
```

This can fail because the collection is modified directly during iteration.

### How do you remove elements while iterating?

Use `Iterator.remove()`:

```java
Iterator<String> iterator = names.iterator();

while (iterator.hasNext()) {
    if (iterator.next().isBlank()) {
        iterator.remove();
    }
}
```

Or use higher-level methods when appropriate:

```java
names.removeIf(String::isBlank);
```

### Is fail-fast behavior guaranteed?

No.

Fail-fast behavior is best-effort.

You should not write code that depends on `ConcurrentModificationException` for correctness.

## Common Mistakes

Mistake 1: Modifying a collection directly inside foreach.

Bad:

```java
for (User user : users) {
    users.remove(user);
}
```

Use `removeIf()` or an iterator.

Mistake 2: Calling `next()` without checking `hasNext()`.

This can throw `NoSuchElementException`.

Mistake 3: Thinking `ConcurrentModificationException` only means multithreading.

It can happen in a single thread too.

It means concurrent with iteration, not necessarily multiple threads.

## Mid/Senior Notes

In modern Java, you often use:

```java
removeIf()
streams
forEach()
```

instead of manual iterators.

But iterators still matter because:

- enhanced for-loop uses them,
- fail-fast behavior appears in interviews,
- safe removal depends on them,
- collection internals rely on them.

For removal, this is usually clear:

```java
users.removeIf(user -> !user.isActive());
```

But for complex iteration with conditional behavior, an explicit iterator can still be the cleanest option.

Senior-level answer:

```text
I usually use enhanced for-loops or removeIf for readability, but I understand
that iterators are the mechanism underneath and that Iterator.remove is the safe
way to remove elements during manual iteration.
```

## Quick Summary

Iterators traverse collections.

`hasNext()` checks for another element.

`next()` returns the next element.

Use `Iterator.remove()` or `removeIf()` to remove safely while iterating.

`ConcurrentModificationException` can happen even in a single thread.

Fail-fast behavior is best-effort, not a correctness mechanism.

