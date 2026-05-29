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

## Why Children Must Be final, sealed, or non-sealed

This is the part that often feels strange at first.

When a class or interface is `sealed`, Java says:

```text
This hierarchy is controlled.
Only explicitly permitted types can extend or implement this type.
```

Because of that, every direct permitted child must clearly say what happens next.

It has three choices:

```text
final      -> I stop the hierarchy here.
sealed     -> I continue controlling the hierarchy.
non-sealed -> I open the hierarchy again from here.
```

That is why this does not compile:

```java
public sealed interface Result permits Success {
}

public class Success implements Result {
}
```

`Success` is allowed to implement `Result`, but it does not say what happens next.

Java wants an explicit decision.

Correct option 1: stop inheritance with `final`.

```java
public final class Success implements Result {
}
```

Correct option 2: keep the hierarchy controlled with `sealed`.

```java
public sealed class Success implements Result
        permits DetailedSuccess {
}

public final class DetailedSuccess extends Success {
}
```

Correct option 3: open inheritance again with `non-sealed`.

```java
public non-sealed class Success implements Result {
}
```

`non-sealed` basically means:

```text
Yes, the parent was sealed, but from this class down inheritance is open again.
```

So yes, a `non-sealed` class behaves like a normal open class.

But Java forces you to write `non-sealed` explicitly because it wants to know that reopening the hierarchy was intentional.

This rule applies only to direct permitted children.

Records are a special case worth remembering: records are implicitly final, so a record can implement a sealed interface without writing `final`.

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

Example with records:

```java
public sealed interface LoginResult
        permits LoginSuccess, LoginFailure {
}

public record LoginSuccess(String token) implements LoginResult {
}

public record LoginFailure(String reason) implements LoginResult {
}
```

This is valid because records are implicitly final.

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

### What does `non-sealed` mean?

`non-sealed` reopens inheritance.

Good answer:

```text
If a class directly extends or implements a sealed type, Java forces it to say
what happens next. `non-sealed` means this class is allowed by the sealed parent,
but from this point down normal inheritance is open again.
```

Example:

```java
public sealed interface Result permits UnknownResult {
}

public non-sealed class UnknownResult implements Result {
}
```

Other classes can now extend `UnknownResult`.

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

Mistake 3: Forgetting that direct permitted subclasses must follow sealing rules.

Direct permitted subclasses must be `final`, `sealed`, or `non-sealed`.

Records are implicitly final, so they already satisfy this rule.

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

Direct permitted subclasses must be `final`, `sealed`, or `non-sealed`.

Records are implicitly final, so they do not need an explicit `final` keyword.

Use sealed classes when a domain concept has a known set of different shapes.
