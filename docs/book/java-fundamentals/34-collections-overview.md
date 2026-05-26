---
layout: default
title: Collections Overview
parent: Java Fundamentals
grand_parent: Book
nav_order: 34
---

# Collections Overview

## Simple Explanation

Collections are Java objects used to store groups of other objects.

Instead of keeping one value:

```java
String name = "Anna";
```

You can keep many values:

```java
List<String> names = List.of("Anna", "Tom", "Maria");
```

Collections are used everywhere in Java backend development:

- lists of users,
- sets of permissions,
- maps of configuration values,
- query results,
- request items,
- cached data.

## Why It Exists

Real applications almost never work with only one object.

You often need to:

- store many values,
- search values,
- remove duplicates,
- preserve order,
- map one value to another,
- iterate over data,
- sort data.

The Java Collections Framework gives you standard tools for these jobs.

Without collections, you would constantly write your own data structures.

That would be a waste of time and a good way to create bugs.

## Basic Collection Types

The most important collection types are:

```text
List -> ordered collection, allows duplicates
Set  -> unique elements, no duplicates
Map  -> key-value pairs
Queue -> elements processed in order
```

Examples:

```java
List<String> names = new ArrayList<>();
Set<String> roles = new HashSet<>();
Map<Long, User> usersById = new HashMap<>();
Queue<String> tasks = new ArrayDeque<>();
```

Each collection has a different purpose.

Do not choose randomly.

## Collection vs Collections

This is a common naming trap.

`Collection` is an interface.

```java
Collection<String> values;
```

`Collections` is a utility class.

```java
Collections.sort(names);
Collections.unmodifiableList(names);
```

One letter changes the meaning.

## Common Implementations

Common implementations:

```text
ArrayList    -> common List implementation
LinkedList   -> List and Queue implementation
HashSet      -> common Set implementation
LinkedHashSet -> Set that preserves insertion order
TreeSet      -> sorted Set
HashMap      -> common Map implementation
LinkedHashMap -> Map that preserves insertion order
TreeMap      -> sorted Map by key
ArrayDeque   -> common Queue/Deque implementation
```

You do not need to memorize every implementation immediately.

But you should know the main ones and what tradeoffs they represent.

## Common Interview Questions

### What is the Java Collections Framework?

Good answer:

```text
The Java Collections Framework is a set of interfaces and classes for storing and
working with groups of objects, like List, Set, Map, Queue, ArrayList, HashSet,
and HashMap.
```

### What is the difference between `Collection` and `Collections`?

Good answer:

```text
Collection is an interface for groups of elements. Collections is a utility class
with static helper methods like sort, reverse, and unmodifiableList.
```

### Is `Map` a `Collection`?

Technically, `Map` is part of the Collections Framework, but it does not implement the `Collection` interface.

Why?

Because `Map` stores key-value pairs, not single elements.

### Which collection should you use by default?

It depends on what you need:

- need ordered elements and duplicates: `List`,
- need unique elements: `Set`,
- need lookup by key: `Map`,
- need queue behavior: `Queue` or `Deque`.

## Common Mistakes

Mistake 1: Using `List` for everything.

If you need uniqueness, use a `Set`.

If you need lookup by id, use a `Map`.

Mistake 2: Choosing implementation before choosing behavior.

First decide what behavior you need:

```text
ordered?
unique?
key-value?
sorted?
fast lookup?
```

Then choose implementation.

Mistake 3: Exposing mutable collections directly.

Bad:

```java
public List<String> getRoles() {
    return roles;
}
```

The caller can modify your internal list.

Better:

```java
public List<String> getRoles() {
    return List.copyOf(roles);
}
```

## Mid/Senior Notes

Collection choice is a design decision.

It affects:

- correctness,
- performance,
- memory usage,
- API clarity,
- ordering guarantees,
- mutability.

Example:

```java
List<String> roles
```

This says:

```text
Roles can have order and duplicates.
```

But if roles should be unique, this is probably better:

```java
Set<String> roles
```

Types communicate intent.

Also, prefer interface types for variables and fields:

```java
List<String> names = new ArrayList<>();
```

Not:

```java
ArrayList<String> names = new ArrayList<>();
```

This keeps your code less coupled to one implementation.

## Quick Summary

Collections store groups of objects.

`List` stores ordered elements.

`Set` stores unique elements.

`Map` stores key-value pairs.

`Collection` is an interface.

`Collections` is a utility class.

Choose collections based on behavior, not habit.

