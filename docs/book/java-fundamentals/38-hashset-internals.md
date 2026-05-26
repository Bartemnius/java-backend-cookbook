---
layout: default
title: HashSet Internals
parent: Java Fundamentals
grand_parent: Book
nav_order: 38
---

# HashSet Internals

## Simple Explanation

`HashSet` stores unique elements.

Example:

```java
Set<String> emails = new HashSet<>();

emails.add("anna@example.com");
emails.add("anna@example.com");

System.out.println(emails.size()); // 1
```

The duplicate value is not added.

Simple mental model:

```text
HashSet = uniqueness based on hashCode() and equals()
```

## Why It Exists

Sometimes you need to guarantee uniqueness.

Examples:

- unique email addresses,
- unique user roles,
- unique product ids,
- unique tags,
- visited nodes in graph algorithms,
- deduplication of imported data.

You could use a `List` and check manually:

```java
if (!emails.contains(email)) {
    emails.add(email);
}
```

But a `HashSet` expresses the intent better:

```java
emails.add(email);
```

The set handles uniqueness.

## Basic Code Example

```java
Set<String> roles = new HashSet<>();

roles.add("ADMIN");
roles.add("USER");
roles.add("ADMIN");

System.out.println(roles);      // order not guaranteed
System.out.println(roles.size()); // 2
```

`HashSet` does not guarantee iteration order.

If order matters, use another implementation:

```java
Set<String> roles = new LinkedHashSet<>();
```

## HashSet Uses HashMap

Internally, `HashSet` is backed by a `HashMap`.

Very simplified:

```text
HashSet element -> stored as HashMap key
```

The map value is just a dummy internal object.

This means `HashSet` depends on the same core rules:

- `hashCode()` chooses a bucket,
- `equals()` checks equality,
- collisions are handled internally,
- broken `equals/hashCode` breaks behavior.

## equals and hashCode

For custom objects, uniqueness depends on `equals()` and `hashCode()`.

Example:

```java
public record EmailAddress(String value) {
}
```

Records automatically generate `equals()` and `hashCode()`.

So this works:

```java
Set<EmailAddress> emails = new HashSet<>();

emails.add(new EmailAddress("anna@example.com"));
emails.add(new EmailAddress("anna@example.com"));

System.out.println(emails.size()); // 1
```

For normal classes, you may need to override `equals()` and `hashCode()` manually.

## Common Interview Questions

### How does `HashSet` work internally?

Good answer:

```text
HashSet stores unique elements and is internally backed by a HashMap. Elements are
stored as keys in that map. It uses hashCode to find a bucket and equals to check
whether an element already exists.
```

### Does `HashSet` allow duplicates?

No.

If an equal element already exists, adding it again has no effect.

### Does `HashSet` preserve order?

No.

`HashSet` does not guarantee iteration order.

Use:

```text
LinkedHashSet -> insertion order
TreeSet       -> sorted order
```

### Can `HashSet` contain null?

Yes.

`HashSet` can contain one `null` element.

### What determines uniqueness in `HashSet`?

`equals()` and `hashCode()`.

If two elements are equal according to `equals()`, they should have the same hash code.

## Common Mistakes

Mistake 1: Expecting insertion order from `HashSet`.

Bad assumption:

```java
Set<String> names = new HashSet<>();
names.add("Anna");
names.add("Tom");
names.add("Maria");

// Do not rely on this iteration order.
```

Use `LinkedHashSet` if order matters.

Mistake 2: Using mutable elements.

If the fields used in `equals()` or `hashCode()` change after insertion, the set may not find the element correctly.

Mistake 3: Not implementing `equals()` and `hashCode()` for custom objects.

Example:

```java
Set<User> users = new HashSet<>();

users.add(new User("anna@example.com"));
users.add(new User("anna@example.com"));
```

If `User` does not override `equals()` and `hashCode()`, the set may treat them as different objects.

## Mid/Senior Notes

`HashSet` is often used for:

- deduplication,
- membership checks,
- visited tracking,
- permission checks,
- fast existence checks.

Example:

```java
Set<Long> existingUserIds = users.stream()
        .map(User::getId)
        .collect(Collectors.toSet());

if (existingUserIds.contains(requestedUserId)) {
    // fast lookup
}
```

A `HashSet` membership check is usually much faster than scanning a list:

```java
users.contains(user)
```

But this only works correctly when equality is correct.

Senior-level answer:

```text
HashSet is backed by HashMap and uses equals/hashCode to enforce uniqueness.
It is great for fast membership checks and deduplication, but I do not rely on
iteration order and I avoid mutable equality fields.
```

## Quick Summary

`HashSet` stores unique elements.

It is backed by a `HashMap`.

It uses `hashCode()` and `equals()`.

It does not guarantee order.

Use `LinkedHashSet` for insertion order.

Use `TreeSet` for sorted order.

Avoid mutable elements when equality fields can change.

