---
layout: default
title: final Keyword
parent: Java Fundamentals
grand_parent: Book
nav_order: 17
---

# final Keyword

## Simple Explanation

`final` means something cannot be changed in a specific way.

The exact meaning depends on where you use it:

```text
final variable -> cannot be reassigned
final method   -> cannot be overridden
final class    -> cannot be extended
```

Example:

```java
final int maxRetries = 3;
maxRetries = 5; // does not compile
```

The variable cannot be reassigned.

## Why It Exists

`final` helps communicate intent.

It tells other developers:

```text
This should not change.
```

That can make code easier to reason about.

It also protects some design decisions:

- a final variable cannot be reassigned,
- a final method cannot be changed by subclass overriding,
- a final class cannot be subclassed.

## Basic Code Example

```java
public final class Money {
    private final int cents;

    public Money(int cents) {
        this.cents = cents;
    }

    public int getCents() {
        return cents;
    }
}
```

Here:

- `Money` cannot be extended,
- `cents` cannot be reassigned after construction.

This is a simple step toward immutability.

## final Reference vs Immutable Object

This is very important.

`final` prevents reassignment.

It does not automatically make the object immutable.

Example:

```java
final List<String> names = new ArrayList<>();

names.add("Anna"); // allowed
names.add("Tom");  // allowed

names = new ArrayList<>(); // does not compile
```

The reference cannot point to a different list.

But the list object itself can still be modified.

## Common Interview Questions

### What does `final` mean in Java?

Good answer:

```text
It depends on where it is used. A final variable cannot be reassigned, a final
method cannot be overridden, and a final class cannot be extended.
```

### Does `final` make an object immutable?

No.

Good answer:

```text
`final` only prevents reassignment of the variable. If the variable points to a
mutable object, the object can still be changed.
```

### Can a final method be overloaded?

Yes.

`final` prevents overriding, not overloading.

Example:

```java
public final void print(String text) {
}

public void print(int number) {
}
```

This is overloading, not overriding.

### Why make a class final?

To prevent inheritance.

This can be useful when subclassing could break assumptions, security, or immutability.

`String` is a famous final class.

## Common Mistakes

Mistake 1: Thinking final objects cannot change.

```java
final StringBuilder builder = new StringBuilder();
builder.append("hello"); // allowed
```

The reference is final. The object is still mutable.

Mistake 2: Using `final` everywhere without team agreement.

Some teams like final local variables. Some find it noisy.

Be consistent with the codebase.

Mistake 3: Confusing final with finally or finalize.

They are different:

```text
final    -> keyword for variables, methods, classes
finally  -> block used with try/catch
finalize -> old Object method, not something modern code should rely on
```

## Mid/Senior Notes

`final` is connected to immutability and design.

This is a good pattern:

```java
public class UserId {
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

The field cannot be reassigned after construction.

That makes the object easier to reason about.

But remember: true immutability also requires that the object does not expose mutable internal state.

For classes, `final` is a design decision.

If you do not want inheritance as an extension point, make that clear.

But do not mark classes final blindly if your framework or testing approach expects proxies/subclassing. For example, some frameworks create proxies under the hood.

## Quick Summary

`final` variable cannot be reassigned.

`final` method cannot be overridden.

`final` class cannot be extended.

`final` does not automatically make mutable objects immutable.

Use it to communicate intent and protect important design constraints.

