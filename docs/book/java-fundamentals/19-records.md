---
layout: default
title: Records
parent: Java Fundamentals
grand_parent: Book
nav_order: 19
---

# Records

## Simple Explanation

A record is a compact way to create a simple data carrier class.

Example:

```java
public record UserDto(String email, String name) {
}
```

This automatically gives you:

- private final fields,
- constructor,
- accessor methods,
- `equals`,
- `hashCode`,
- `toString`.

So this record:

```java
public record Point(int x, int y) {
}
```

Replaces a lot of boring boilerplate code.

Records became a standard feature in Java 16.

## Why It Exists

Java classes often need to represent simple data.

Before records, you had to write a lot of code:

```java
public class Point {
    private final int x;
    private final int y;

    public Point(int x, int y) {
        this.x = x;
        this.y = y;
    }

    public int getX() {
        return x;
    }

    public int getY() {
        return y;
    }

    // equals, hashCode, toString...
}
```

With a record:

```java
public record Point(int x, int y) {
}
```

Much cleaner.

## Basic Code Example

```java
public record UserResponse(Long id, String email, String displayName) {
}
```

Usage:

```java
UserResponse response = new UserResponse(
    1L,
    "anna@example.com",
    "Anna"
);

System.out.println(response.email());
System.out.println(response);
```

Notice:

Record accessors are named like this:

```java
response.email()
```

Not:

```java
response.getEmail()
```

## Validation In Records

Records can have compact constructors:

```java
public record EmailAddress(String value) {
    public EmailAddress {
        if (value == null || value.isBlank()) {
            throw new IllegalArgumentException("Email must not be blank");
        }
    }
}
```

This validates the value before the record is created.

## Common Interview Questions

### What is a record in Java?

Good answer:

```text
A record is a concise way to define an immutable data carrier. Java automatically
generates the constructor, accessors, equals, hashCode, and toString based on the
record components.
```

### Are records immutable?

Records are shallowly immutable by default.

Their fields are final, so you cannot reassign record components.

But if a record contains a mutable object, that object can still be changed.

Example:

```java
public record Team(List<String> members) {
}
```

The `members` reference is final.

But the list itself may still be mutable.

### Can records have methods?

Yes.

```java
public record Money(int cents) {
    public boolean isPositive() {
        return cents > 0;
    }
}
```

### Can records extend classes?

No.

Records implicitly extend `java.lang.Record`.

They can implement interfaces.

## Common Mistakes

Mistake 1: Thinking records are only DTOs.

Records are very useful for DTOs, but they can also represent small value objects.

Example:

```java
public record UserId(String value) {
}
```

Mistake 2: Assuming deep immutability.

```java
public record OrderLines(List<String> lines) {
}
```

If the passed list is mutable, the record does not automatically protect you.

Use defensive copying when needed:

```java
public record OrderLines(List<String> lines) {
    public OrderLines {
        lines = List.copyOf(lines);
    }
}
```

Mistake 3: Using records for entities with complex lifecycle.

Records are not always the best fit for JPA entities or objects that need identity, lazy loading, proxies, or mutable lifecycle state.

## Mid/Senior Notes

Records are excellent for:

- DTOs,
- API responses,
- small value objects,
- query results,
- immutable command objects,
- simple grouping of values.

They are weaker for:

- complex domain entities,
- mutable objects,
- objects requiring inheritance,
- some framework-managed proxy scenarios.

In Spring applications, records are commonly used for request and response DTOs:

```java
public record CreateUserRequest(String email, String password) {
}

public record UserResponse(Long id, String email) {
}
```

This reduces boilerplate and makes the intent clear:

```text
This object is mainly data.
```

But do not use records blindly. If the object needs behavior, invariants, or lifecycle, think carefully.

## Quick Summary

Records are concise immutable data carriers.

They generate constructor, accessors, `equals`, `hashCode`, and `toString`.

Records are shallowly immutable.

They can have validation and methods.

They are great for DTOs and value objects, but not always ideal for complex entities.

