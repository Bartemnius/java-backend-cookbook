---
layout: default
title: "List, Set, Map"
parent: Java Fundamentals
grand_parent: Book
nav_order: 35
---

# List, Set, Map

## Simple Explanation

`List`, `Set`, and `Map` are the three collection types every Java developer must understand.

Simple version:

```text
List -> ordered elements, duplicates allowed
Set  -> unique elements
Map  -> key-value pairs
```

Example:

```java
List<String> names = List.of("Anna", "Tom", "Anna");
Set<String> roles = Set.of("ADMIN", "USER");
Map<Long, String> users = Map.of(1L, "Anna", 2L, "Tom");
```

## Why It Exists

Different problems need different data structures.

If you care about order and duplicates, use a `List`.

If you care about uniqueness, use a `Set`.

If you want to find a value by key, use a `Map`.

Using the wrong collection makes code harder to read and often slower or more bug-prone.

## List

A `List` is ordered and allows duplicates.

Example:

```java
List<String> names = new ArrayList<>();

names.add("Anna");
names.add("Tom");
names.add("Anna");

System.out.println(names.get(0)); // Anna
```

Use a `List` when:

- order matters,
- duplicates are allowed,
- you need access by index,
- you process items in sequence.

Common implementation:

```java
List<String> names = new ArrayList<>();
```

## Set

A `Set` stores unique elements.

Example:

```java
Set<String> emails = new HashSet<>();

emails.add("anna@example.com");
emails.add("anna@example.com");

System.out.println(emails.size()); // 1
```

Use a `Set` when:

- duplicates should not exist,
- you need fast membership checks,
- uniqueness is part of the domain.

Common implementation:

```java
Set<String> emails = new HashSet<>();
```

Important: uniqueness depends on `equals()` and `hashCode()`.

For custom objects, broken `equals()` and `hashCode()` can break `Set` behavior.

## Map

A `Map` stores key-value pairs.

Example:

```java
Map<Long, String> usersById = new HashMap<>();

usersById.put(1L, "Anna");
usersById.put(2L, "Tom");

System.out.println(usersById.get(1L)); // Anna
```

Use a `Map` when:

- you need lookup by key,
- you want to group values by id/name/type,
- you want fast access without scanning a whole list.

Common implementation:

```java
Map<Long, User> usersById = new HashMap<>();
```

## Common Interview Questions

### What is the difference between `List`, `Set`, and `Map`?

Good answer:

```text
List stores ordered elements and allows duplicates. Set stores unique elements.
Map stores key-value pairs and lets us look up values by key.
```

### Does `Set` preserve order?

It depends on the implementation.

`HashSet` does not guarantee iteration order.

`LinkedHashSet` preserves insertion order.

`TreeSet` stores elements in sorted order.

### Can a `Map` have duplicate keys?

No.

A map can have only one value for a key.

If you put a value with the same key again, the old value is replaced.

```java
map.put(1L, "Anna");
map.put(1L, "Tom");

System.out.println(map.get(1L)); // Tom
```

### Can a `Map` have duplicate values?

Yes.

Different keys can point to the same value.

## Common Mistakes

Mistake 1: Using a list and manually checking duplicates.

Weak:

```java
if (!emails.contains(email)) {
    emails.add(email);
}
```

Better:

```java
Set<String> emails = new HashSet<>();
emails.add(email);
```

Mistake 2: Using a list for lookup by id.

Weak:

```java
for (User user : users) {
    if (user.getId().equals(id)) {
        return user;
    }
}
```

Better when frequent lookup is needed:

```java
Map<Long, User> usersById = new HashMap<>();
```

Mistake 3: Assuming all sets and maps preserve order.

They do not.

Choose `LinkedHashSet`, `LinkedHashMap`, `TreeSet`, or `TreeMap` when order matters.

## Mid/Senior Notes

The collection type should express intent.

Example:

```java
private List<Role> roles;
```

This allows duplicates.

If duplicates are invalid, use:

```java
private Set<Role> roles;
```

That makes invalid states harder to represent.

For APIs, be careful with mutability:

```java
public List<OrderLine> getLines() {
    return lines;
}
```

This exposes internal state.

Better:

```java
public List<OrderLine> getLines() {
    return List.copyOf(lines);
}
```

Senior-level answer:

```text
I choose List, Set, or Map based on the business meaning first, then performance.
The type should communicate whether order, uniqueness, or key-based lookup matters.
```

## Quick Summary

`List` is ordered and allows duplicates.

`Set` stores unique elements.

`Map` stores key-value pairs.

Order guarantees depend on implementation.

Use the collection type that matches the problem.

