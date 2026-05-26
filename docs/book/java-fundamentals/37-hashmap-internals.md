---
layout: default
title: HashMap Internals
parent: Java Fundamentals
grand_parent: Book
nav_order: 37
---

# HashMap Internals

## Simple Explanation

`HashMap` stores key-value pairs.

Example:

```java
Map<Long, User> usersById = new HashMap<>();

usersById.put(1L, new User("anna@example.com"));
usersById.put(2L, new User("tom@example.com"));

User user = usersById.get(1L);
```

The key is used to find the value quickly.

Simple mental model:

```text
key -> hashCode -> bucket -> value
```

A `HashMap` tries to avoid scanning all entries.

Instead, it uses the key's hash code to decide where to store and find the value.

## Why It Exists

Without a map, looking up a user by id in a list may require scanning:

```java
for (User user : users) {
    if (user.getId().equals(id)) {
        return user;
    }
}
```

That is O(n).

With a `HashMap`, lookup is usually close to O(1):

```java
User user = usersById.get(id);
```

That is why maps are used everywhere:

- cache by id,
- group by key,
- lookup by name,
- count occurrences,
- store configuration,
- index data for faster access.

## Basic Code Example

```java
Map<String, Integer> scores = new HashMap<>();

scores.put("Anna", 10);
scores.put("Tom", 15);

System.out.println(scores.get("Anna")); // 10
```

If you put the same key again, the old value is replaced:

```java
scores.put("Anna", 20);

System.out.println(scores.get("Anna")); // 20
```

Keys are unique.

Values do not have to be unique.

## Buckets

Internally, a `HashMap` has an array of buckets.

Very simplified:

```text
bucket 0 -> entries
bucket 1 -> entries
bucket 2 -> entries
bucket 3 -> entries
```

When you put a key-value pair:

1. Java calls `hashCode()` on the key.
2. The hash is used to choose a bucket.
3. The entry is stored in that bucket.

When you get a value:

1. Java calls `hashCode()` on the lookup key.
2. It finds the bucket.
3. It compares keys using `equals()`.
4. It returns the matching value.

That is why both `hashCode()` and `equals()` matter.

## Collisions

A collision happens when different keys end up in the same bucket.

Example:

```text
key A -> bucket 3
key B -> bucket 3
```

This is normal.

`HashMap` handles collisions by storing multiple entries in the same bucket.

In modern Java, when a bucket becomes too large, entries can be transformed into a tree structure to improve worst-case performance.

You do not need to memorize every threshold for most interviews.

You should understand the idea:

```text
Good hash distribution -> fast map
Many collisions        -> slower map
```

## equals and hashCode

The key rule:

```text
If two keys are equal according to equals(), they must have the same hashCode().
```

Example:

```java
public class UserId {
    private final Long value;

    public UserId(Long value) {
        this.value = value;
    }

    @Override
    public boolean equals(Object other) {
        if (this == other) {
            return true;
        }

        if (!(other instanceof UserId userId)) {
            return false;
        }

        return Objects.equals(value, userId.value);
    }

    @Override
    public int hashCode() {
        return Objects.hash(value);
    }
}
```

If this contract is broken, `HashMap` can fail to find keys that are logically equal.

## Resizing

A `HashMap` has internal capacity.

When it gets too full, it resizes.

That means it creates a larger internal bucket array and redistributes entries.

This costs time, but it does not happen on every insert.

The map grows to keep lookup performance good.

## Common Interview Questions

### How does `HashMap` work internally?

Good answer:

```text
HashMap stores key-value pairs in buckets. It uses the key's hashCode to choose a
bucket, then uses equals to find the exact key inside that bucket. Good hash
distribution gives fast lookups, while many collisions can slow it down.
```

### What happens when two keys have the same hash code?

They may end up in the same bucket.

Then `HashMap` uses `equals()` to find the correct key.

This is called a collision.

### Why must equal objects have the same hash code?

Because `HashMap` uses `hashCode()` to find the bucket first.

If two equal keys have different hash codes, the map may look in the wrong bucket and fail to find the value.

### Can a `HashMap` have `null` keys?

Yes.

`HashMap` allows one `null` key and multiple `null` values.

This is different from some other map implementations, like `ConcurrentHashMap`, which does not allow null keys or values.

### Is `HashMap` thread-safe?

No.

`HashMap` is not thread-safe.

For concurrent access, use proper synchronization or concurrent collections like `ConcurrentHashMap`.

## Common Mistakes

Mistake 1: Using mutable keys.

Bad:

```java
Map<User, String> map = new HashMap<>();
User user = new User("old@example.com");

map.put(user, "data");
user.setEmail("new@example.com");

map.get(user); // may fail if email is used in hashCode
```

If a key's hash changes after insertion, the map may not find it.

Mistake 2: Overriding `equals()` without `hashCode()`.

This breaks the map contract.

Mistake 3: Assuming `HashMap` preserves order.

It does not guarantee iteration order.

Use `LinkedHashMap` for insertion order.

Use `TreeMap` for sorted keys.

Mistake 4: Using `HashMap` from multiple threads without protection.

That can create race conditions and corrupted behavior.

## Mid/Senior Notes

`HashMap` is simple to use but important to understand deeply.

In backend systems, `HashMap` appears in:

- caches,
- grouping logic,
- request processing,
- deduplication,
- indexing in memory,
- framework internals.

Performance depends on:

- good `hashCode()` implementation,
- stable keys,
- low collision rate,
- appropriate initial capacity for large maps,
- avoiding unnecessary resizing.

Example:

```java
Map<Long, User> usersById = new HashMap<>(expectedSize);
```

For very large maps, initial capacity can matter.

But do not over-optimize small maps.

Senior-level answer:

```text
HashMap is usually O(1) for get and put, but that depends on good hash distribution
and stable keys. It is not ordered and not thread-safe. The equals/hashCode contract
is critical for correct behavior.
```

## Quick Summary

`HashMap` stores key-value pairs.

It uses `hashCode()` to choose a bucket.

It uses `equals()` to find the exact key.

Collisions are normal and handled internally.

Equal keys must have the same hash code.

Do not use mutable objects as keys if their equality fields can change.

`HashMap` is not thread-safe.

