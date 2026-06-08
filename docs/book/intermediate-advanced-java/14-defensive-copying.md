---
layout: default
title: Defensive Copying
parent: Intermediate And Advanced Java
grand_parent: Book
nav_order: 14
---

# Defensive Copying

## Simple Explanation

Defensive copying means making a copy of mutable data so outside code cannot change your object's internal state.

Example problem:

```java
public class Team {
    private final List<String> members;

    public Team(List<String> members) {
        this.members = members;
    }

    public List<String> getMembers() {
        return members;
    }
}
```

This looks fine, but it is not safe.

The caller can still modify the list.

## Why It Exists

`final` protects the reference, not the object.

This field cannot point to a different list:

```java
private final List<String> members;
```

But the list itself can still be changed:

```java
members.add("Someone");
```

Defensive copying prevents callers from accidentally or intentionally modifying internal state.

## Basic Code Example

Safer version:

```java
public class Team {
    private final List<String> members;

    public Team(List<String> members) {
        this.members = List.copyOf(members);
    }

    public List<String> getMembers() {
        return List.copyOf(members);
    }
}
```

Now:

- constructor copies input,
- getter returns a copy,
- internal list is protected.

## Constructor Copy

Without constructor copy:

```java
List<String> names = new ArrayList<>();
names.add("Anna");

Team team = new Team(names);
names.add("Tom");
```

If `Team` stores the original list, `team` changes from the outside.

With defensive copy:

```java
this.members = List.copyOf(members);
```

External changes to `names` do not affect the team.

## Getter Copy

Without getter copy:

```java
team.getMembers().add("Tom");
```

The caller modifies internal state.

With `List.copyOf()`:

```java
public List<String> getMembers() {
    return List.copyOf(members);
}
```

The returned list cannot be modified.

## Common Interview Questions

### What is defensive copying?

Good answer:

```text
Defensive copying means copying mutable input or output so callers cannot modify
an object's internal state through shared references.
```

### Why is defensive copying needed if fields are final?

Because `final` prevents reassignment of the reference.

It does not make the referenced object immutable.

### What is the difference between unmodifiable view and copy?

An unmodifiable view blocks modification through that view, but may still reflect changes to the original collection.

A copy is independent from the original collection.

In many cases, `List.copyOf()` is a good simple choice.

### Should you always copy everything?

No.

Copying has cost.

Use it when protecting object state matters.

## Common Mistakes

Mistake 1: Returning internal mutable collections.

Bad:

```java
public List<OrderLine> getLines() {
    return lines;
}
```

Mistake 2: Copying only in getter but not constructor.

The original input list can still modify internal state.

Mistake 3: Confusing shallow copy with deep copy.

`List.copyOf(users)` copies the list structure.

It does not clone every `User` object inside.

If elements are mutable, they can still change.

## Mid/Senior Notes

Defensive copying is part of designing trustworthy objects.

It is especially useful for:

- value objects,
- records with collections,
- domain objects exposing lists,
- API models,
- configuration objects.

Record example:

```java
public record OrderLines(List<OrderLine> lines) {
    public OrderLines {
        lines = List.copyOf(lines);
    }
}
```

This protects the record from external list mutation.

Senior-level answer:

```text
I use defensive copying when a class owns mutable data and should protect its
invariants. I copy inputs in constructors and avoid exposing internal mutable
collections directly. I also remember that collection copies are usually shallow.
```

## Quick Summary

Defensive copying protects internal state.

Copy mutable inputs in constructors.

Do not expose mutable internal collections directly.

`final` is not enough for mutable objects.

`List.copyOf()` is often a good tool.

Copies are usually shallow unless you explicitly deep-copy elements.
