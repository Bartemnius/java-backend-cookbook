---
layout: default
title: Sealed Classes
parent: Java Fundamentals
grand_parent: Book
nav_order: 20
---

# Sealed Classes

## Simple Explanation

A sealed class controls which classes are allowed to extend it.

Example:

```java
public sealed interface PaymentResult
        permits PaymentSuccess, PaymentFailure {
}

public record PaymentSuccess(String transactionId) implements PaymentResult {
}

public record PaymentFailure(String reason) implements PaymentResult {
}
```

This means:

```text
Only PaymentSuccess and PaymentFailure can implement PaymentResult.
```

Sealed classes became a standard feature in Java 17.

## Why It Exists

Normal inheritance is open.

If a class or interface is public, many different classes can extend or implement it.

Sometimes that is too open.

For example, maybe a result can only be:

```text
success
failure
```

You do not want random third states appearing elsewhere in the codebase.

Sealed classes let you model a limited set of possible types.

## Basic Code Example

```java
public sealed interface LoginResult
        permits LoginSuccess, InvalidPassword, UserLocked {
}

public record LoginSuccess(String token) implements LoginResult {
}

public record InvalidPassword() implements LoginResult {
}

public record UserLocked(String reason) implements LoginResult {
}
```

Now every login result must be one of these known types.

This is useful because code can handle all cases explicitly.

## sealed, non-sealed, final

A class that extends a sealed class must say what happens next.

It must be one of:

```text
final      -> cannot be extended further
sealed     -> still restricted
non-sealed -> opens inheritance again
```

Example:

```java
public sealed class Shape permits Circle, Rectangle {
}

public final class Circle extends Shape {
}

public non-sealed class Rectangle extends Shape {
}
```

`Circle` cannot be extended.

`Rectangle` can be extended because it is `non-sealed`.

## Common Interview Questions

### What is a sealed class?

Good answer:

```text
A sealed class or interface restricts which classes can extend or implement it.
It is useful when we want a controlled hierarchy with a known set of subtypes.
```

### Why use sealed classes?

To model a limited set of possible types and prevent uncontrolled inheritance.

This can make domain models safer and pattern matching more useful.

### What is the `permits` keyword?

`permits` lists the classes that are allowed to extend or implement the sealed type.

Example:

```java
public sealed interface Result permits Success, Failure {
}
```

### What is the difference between `final` and `sealed`?

`final` blocks inheritance completely.

`sealed` allows inheritance only by specific permitted classes.

## Common Mistakes

Mistake 1: Using sealed classes where an enum is enough.

If your values have no different data or behavior, an enum may be simpler.

Example:

```java
public enum PaymentStatus {
    SUCCESS,
    FAILURE
}
```

Use sealed classes when each case may carry different data or behavior.

Mistake 2: Making hierarchies complex for no reason.

Sealed classes are powerful, but they can be overused.

If a simple class or enum solves the problem clearly, use the simple thing.

Mistake 3: Forgetting that permitted subclasses must follow sealing rules.

Subclasses must be `final`, `sealed`, or `non-sealed`.

## Mid/Senior Notes

Sealed classes are useful for modeling closed domain alternatives.

Example:

```java
public sealed interface DiscountPolicy
        permits NoDiscount, PercentageDiscount, FixedAmountDiscount {
}
```

Each implementation can carry different data:

```java
public record NoDiscount() implements DiscountPolicy {
}

public record PercentageDiscount(int percent) implements DiscountPolicy {
}

public record FixedAmountDiscount(int cents) implements DiscountPolicy {
}
```

This can be cleaner than a class with many nullable fields:

```java
public class Discount {
    private String type;
    private Integer percent;
    private Integer cents;
}
```

That design allows invalid states.

Sealed types help say:

```text
A discount is exactly one of these shapes.
```

They work especially well with records and pattern matching in modern Java.

But do not force them into every design. Many business applications are still simpler with normal classes, enums, and interfaces.

## Quick Summary

Sealed classes restrict which classes can extend them.

They are useful for controlled type hierarchies.

`permits` lists allowed subclasses.

Subclasses must be `final`, `sealed`, or `non-sealed`.

Use sealed classes when a domain concept has a known set of different shapes.

