---
layout: default
title: Lambdas
parent: Intermediate And Advanced Java
grand_parent: Book
nav_order: 9
---

# Lambdas

## Simple Explanation

A lambda is a short way to write a piece of behavior.

Example:

```java
names.forEach(name -> System.out.println(name));
```

This part is the lambda:

```java
name -> System.out.println(name)
```

It means:

```text
Take a name and print it.
```

Lambdas are commonly used with streams, collections, callbacks, and functional interfaces.

## Why It Exists

Before lambdas, Java often required anonymous classes for small pieces of behavior.

Old style:

```java
button.addActionListener(new ActionListener() {
    @Override
    public void actionPerformed(ActionEvent event) {
        System.out.println("Clicked");
    }
});
```

Lambda style:

```java
button.addActionListener(event -> System.out.println("Clicked"));
```

The lambda is shorter and focuses on the behavior.

## Basic Syntax

No parameters:

```java
() -> System.out.println("Hello")
```

One parameter:

```java
name -> name.toUpperCase()
```

Multiple parameters:

```java
(a, b) -> a + b
```

Block body:

```java
name -> {
    String upper = name.toUpperCase();
    System.out.println(upper);
}
```

## Basic Code Example

```java
List<String> names = List.of("Anna", "Tom", "Maria");

List<String> upperNames = names.stream()
        .map(name -> name.toUpperCase())
        .toList();
```

This lambda:

```java
name -> name.toUpperCase()
```

takes a `String` and returns another `String`.

With method reference:

```java
List<String> upperNames = names.stream()
        .map(String::toUpperCase)
        .toList();
```

Both versions are fine.

## Lambdas And Functional Interfaces

A lambda needs a target type.

That target type is usually a functional interface.

Example:

```java
Predicate<String> isLong = text -> text.length() > 10;
```

`Predicate<String>` is the target type.

The lambda provides implementation for its single abstract method.

## Capturing Variables

Lambdas can use local variables from the surrounding method if they are effectively final.

Example:

```java
String prefix = "User: ";

names.forEach(name -> System.out.println(prefix + name));
```

`prefix` is effectively final because it is not changed after assignment.

This does not compile:

```java
String prefix = "User: ";

prefix = "Customer: ";

names.forEach(name -> System.out.println(prefix + name));
```

Java requires captured local variables to be final or effectively final.

## Common Interview Questions

### What is a lambda in Java?

Good answer:

```text
A lambda is a concise way to represent behavior that can be passed around, usually
as an implementation of a functional interface.
```

### What is an effectively final variable?

A variable is effectively final if it is assigned once and not changed afterwards.

Lambdas can capture local variables only if they are final or effectively final.

### Are lambdas objects?

At the language level, lambdas are used as instances of functional interfaces.

The JVM implementation is more complex and may use invokedynamic, but for most interviews it is enough to say that a lambda provides an implementation for a functional interface.

### What is the difference between a lambda and a method reference?

A method reference is a shorter form for a lambda that only calls an existing method.

Example:

```java
name -> name.toUpperCase()
```

can become:

```java
String::toUpperCase
```

## Common Mistakes

Mistake 1: Making lambdas too large.

Bad:

```java
users.forEach(user -> {
    validate(user);
    updateDatabase(user);
    sendEmail(user);
    publishEvent(user);
});
```

If a lambda has too much logic, extract a method.

Mistake 2: Mutating external state.

```java
List<String> emails = new ArrayList<>();

users.forEach(user -> emails.add(user.getEmail()));
```

This works, but a stream transformation is often clearer:

```java
List<String> emails = users.stream()
        .map(User::getEmail)
        .toList();
```

Mistake 3: Thinking lambdas are always more readable.

Sometimes a normal method is better.

## Mid/Senior Notes

Lambdas are useful when behavior is small and focused.

Good:

```java
users.stream()
        .filter(User::isActive)
        .map(User::getEmail)
        .toList();
```

Weak:

```java
users.stream()
        .map(user -> {
            // 30 lines of business logic
        })
        .toList();
```

Senior-level answer:

```text
I use lambdas for small behavior and data transformations. If the lambda becomes
large, has side effects, or hides business logic, I extract a named method or use
a normal loop.
```

## Quick Summary

Lambdas represent small pieces of behavior.

They usually target functional interfaces.

Captured local variables must be final or effectively final.

Method references are shorter lambdas for existing methods.

Use lambdas for clarity, not to look clever.
