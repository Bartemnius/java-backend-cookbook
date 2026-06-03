---
layout: default
title: Functional Interfaces
parent: Intermediate And Advanced Java
grand_parent: Book
nav_order: 10
---

# Functional Interfaces

## Simple Explanation

A functional interface is an interface with exactly one abstract method.

Example:

```java
@FunctionalInterface
public interface EmailValidator {
    boolean isValid(String email);
}
```

Because it has one abstract method, it can be implemented with a lambda:

```java
EmailValidator validator = email -> email.contains("@");
```

Functional interfaces are the target type for lambdas.

## Why It Exists

Lambdas need a type.

Java is statically typed, so this lambda:

```java
email -> email.contains("@")
```

does not exist in isolation.

Java needs to know what kind of behavior it represents.

For example:

```java
Predicate<String> validator = email -> email.contains("@");
```

Now Java knows:

```text
input: String
output: boolean
```

## Common Built-In Functional Interfaces

Java provides many functional interfaces in `java.util.function`.

The most common:

```text
Predicate<T>  -> T -> boolean
Function<T,R> -> T -> R
Consumer<T>   -> T -> void
Supplier<T>   -> () -> T
UnaryOperator<T> -> T -> T
BinaryOperator<T> -> (T, T) -> T
```

## Predicate

`Predicate<T>` checks a condition.

```java
Predicate<User> isActive = user -> user.isActive();
```

Used in streams:

```java
users.stream()
        .filter(isActive)
        .toList();
```

## Function

`Function<T, R>` transforms one type into another.

```java
Function<User, String> getEmail = user -> user.getEmail();
```

Used in streams:

```java
users.stream()
        .map(getEmail)
        .toList();
```

## Consumer

`Consumer<T>` takes a value and returns nothing.

```java
Consumer<String> printer = text -> System.out.println(text);
```

Used with `forEach`:

```java
names.forEach(printer);
```

## Supplier

`Supplier<T>` takes no input and returns a value.

```java
Supplier<UUID> idGenerator = () -> UUID.randomUUID();
```

Good for lazy value creation:

```java
String value = optional.orElseGet(() -> loadDefaultValue());
```

## @FunctionalInterface

`@FunctionalInterface` is optional but useful.

Example:

```java
@FunctionalInterface
public interface PriceCalculator {
    Money calculate(Order order);
}
```

It tells the compiler:

```text
This interface should have exactly one abstract method.
```

If someone adds another abstract method, compilation fails.

## Common Interview Questions

### What is a functional interface?

Good answer:

```text
A functional interface is an interface with exactly one abstract method. It can
be implemented using a lambda expression or method reference.
```

### Is `@FunctionalInterface` required?

No.

An interface with one abstract method is functional even without the annotation.

But the annotation is useful because the compiler protects the intent.

### What is the difference between `Predicate`, `Function`, `Consumer`, and `Supplier`?

Good answer:

```text
Predicate returns boolean, Function transforms input to output, Consumer accepts
input and returns nothing, and Supplier provides a value without input.
```

### Can a functional interface have default methods?

Yes.

It can have default and static methods.

It must have exactly one abstract method.

## Common Mistakes

Mistake 1: Creating custom functional interfaces when built-in ones are enough.

Instead of:

```java
@FunctionalInterface
interface StringChecker {
    boolean check(String value);
}
```

Use:

```java
Predicate<String>
```

Mistake 2: Using functional interfaces for unclear business concepts.

Sometimes a domain-specific interface is better:

```java
PaymentAuthorizer
```

is clearer than:

```java
Function<PaymentRequest, AuthorizationResult>
```

Mistake 3: Forgetting checked exceptions.

Built-in functional interfaces usually do not declare checked exceptions.

That can make IO/database code inside lambdas awkward.

## Mid/Senior Notes

Functional interfaces are great for small strategies and callbacks.

Examples:

```java
Predicate<User> activeUser = User::isActive;
Function<User, String> email = User::getEmail;
Supplier<Instant> clock = Instant::now;
```

They also support design patterns:

- Strategy,
- Factory,
- Callback,
- Template behavior.

But readability matters.

Senior-level answer:

```text
I use built-in functional interfaces for simple generic behavior. For important
domain concepts, I prefer named interfaces because they communicate intent better
than generic Function or Predicate types.
```

## Quick Summary

A functional interface has one abstract method.

Lambdas implement functional interfaces.

Use `@FunctionalInterface` to protect intent.

Common types are `Predicate`, `Function`, `Consumer`, and `Supplier`.

Use built-in types for simple behavior and named domain interfaces for important concepts.
