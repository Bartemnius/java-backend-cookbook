---
layout: default
title: Immutability In Real Applications
parent: Intermediate And Advanced Java
grand_parent: Book
nav_order: 13
---

# Immutability In Real Applications

## Simple Explanation

An immutable object is an object that cannot be changed after it is created.

Example:

```java
public record Money(int cents, String currency) {
}
```

After a `Money` object is created, its fields cannot be reassigned.

Simple idea:

```text
Create once. Read many times. Do not mutate.
```

Immutability makes code easier to reason about because the object does not secretly change under your feet.

## Why It Exists

Mutable state is one of the biggest sources of bugs.

Example:

```java
order.setStatus(PAID);
order.setTotal(1000);
order.setStatus(CANCELLED);
```

If many parts of the application can change the same object, it becomes harder to know:

- who changed it,
- when it changed,
- why it changed,
- whether the current state is valid.

Immutable objects reduce that problem.

They are especially useful for:

- value objects,
- DTOs,
- configuration,
- commands,
- events,
- cache keys,
- objects shared between threads.

## Basic Code Example

Mutable version:

```java
public class UserId {
    private String value;

    public void setValue(String value) {
        this.value = value;
    }

    public String getValue() {
        return value;
    }
}
```

Immutable version:

```java
public final class UserId {
    private final String value;

    public UserId(String value) {
        if (value == null || value.isBlank()) {
            throw new IllegalArgumentException("User id must not be blank");
        }

        this.value = value;
    }

    public String value() {
        return value;
    }
}
```

This object is safer:

- class is `final`,
- field is `final`,
- no setter,
- constructor validates state.

## Records And Immutability

Records are useful for simple immutable data carriers.

Example:

```java
public record CreateUserCommand(String email, String password) {
    public CreateUserCommand {
        if (email == null || email.isBlank()) {
            throw new IllegalArgumentException("Email must not be blank");
        }
    }
}
```

But records are only shallowly immutable.

This can still be dangerous:

```java
public record Team(List<String> members) {
}
```

The `members` reference cannot be reassigned, but the list itself may still be mutable.

That is where defensive copying matters.

## Common Interview Questions

### What is immutability?

Good answer:

```text
Immutability means an object cannot be changed after construction. It usually has
final fields, no setters, validation in the constructor, and does not expose mutable
internal state.
```

### Why is immutability useful?

It makes code easier to reason about.

It also helps with:

- thread safety,
- safer sharing,
- reliable map keys,
- fewer side effects,
- simpler testing.

### Are records fully immutable?

Not always.

Records are shallowly immutable.

If a record contains a mutable object like `List`, the list can still be changed unless you make a defensive copy.

### Is `final` enough for immutability?

No.

`final` prevents reassignment of the field.

It does not make a mutable object immutable.

## Common Mistakes

Mistake 1: Thinking no setters means immutable.

If a getter exposes a mutable list, callers can still modify state.

Mistake 2: Using mutable objects as `HashMap` keys.

If fields used by `equals()` or `hashCode()` change, map lookup can break.

Mistake 3: Making everything immutable without thinking.

Some objects naturally have lifecycle and state changes, like JPA entities.

Do not force immutability everywhere blindly.

## Mid/Senior Notes

In real applications, immutability is most useful at boundaries and for values.

Good candidates:

- `Money`,
- `EmailAddress`,
- `UserId`,
- request commands,
- response DTOs,
- domain events,
- configuration objects.

JPA entities are often mutable because frameworks need constructors, proxies, dirty checking, and lifecycle management.

That is fine.

Senior-level answer:

```text
I prefer immutability for value objects, DTOs, commands, events, and configuration.
For entities with persistence lifecycle, I am more careful because frameworks often
expect controlled mutability. The goal is not dogma; the goal is reducing accidental
state changes.
```

## Quick Summary

Immutable objects cannot change after construction.

They usually use final fields and no setters.

Records help, but only provide shallow immutability.

Immutability improves reasoning and thread safety.

Use it especially for value objects, DTOs, commands, events, and configuration.
