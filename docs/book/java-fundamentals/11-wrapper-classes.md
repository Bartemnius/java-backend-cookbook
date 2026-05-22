---
layout: default
title: Wrapper Classes
parent: Java Fundamentals
grand_parent: Book
nav_order: 11
---

# Wrapper Classes


## Simple Explanation

Wrapper classes are object versions of primitive types.

Examples:

```text
int     -> Integer
long    -> Long
double  -> Double
boolean -> Boolean
char    -> Character
```

Why do they exist?

Because sometimes Java needs values to be objects.

For example, collections and generics work with objects, not primitive types:

```java
List<Integer> numbers = List.of(1, 2, 3);
```

You cannot write:

```java
List<int> numbers = List.of(1, 2, 3);
```

That does not compile.

## Why It Exists

Java has both primitives and objects.

Primitives are efficient.

Objects are more flexible.

Wrapper classes connect these two worlds.

They allow primitive-like values to be used:

- in collections,
- with generics,
- as nullable values,
- with utility methods,
- where an object is required.

## Basic Code Example

```java
Integer age = 30;
Long requests = 1000L;
Boolean active = true;

int parsed = Integer.parseInt("123");
String text = Integer.toString(456);
```

Wrapper classes also contain useful constants:

```java
System.out.println(Integer.MAX_VALUE);
System.out.println(Integer.MIN_VALUE);
```

## Common Interview Questions

### What is a wrapper class?

Good answer:

```text
A wrapper class is an object representation of a primitive type, like `Integer`
for `int` or `Long` for `long`. Wrappers are useful when we need objects, for
example in collections, generics, or nullable fields.
```

### Why do collections use `Integer` instead of `int`?

Java generics work with reference types, not primitive types.

That is why we write:

```java
List<Integer> numbers = new ArrayList<>();
```

Not:

```java
List<int> numbers = new ArrayList<>();
```

### Can wrapper types be `null`?

Yes.

That is one major difference from primitives.

```java
Integer age = null;
```

But this can create `NullPointerException` if Java tries to unbox it into an `int`.

## Common Mistakes

Mistake 1: Forgetting wrappers can be null.

This code compiles:

```java
Integer value = null;
int number = value;
```

But it fails at runtime with `NullPointerException`, because Java tries to unbox `null`.

Mistake 2: Comparing wrapper objects with `==`.

This can be confusing:

```java
Integer a = 1000;
Integer b = 1000;

System.out.println(a == b);      // false in many cases
System.out.println(a.equals(b)); // true
```

Use `.equals()` for value comparison with wrapper objects.

Mistake 3: Using wrappers everywhere without reason.

If a field is required and cannot be missing, a primitive may communicate that better:

```java
private boolean active;
```

Instead of:

```java
private Boolean active;
```

Unless `null` has a real meaning.

## Mid/Senior Notes

Wrapper classes look simple, but they matter in real systems.

Nullability is the biggest design issue.

For example:

```java
private Boolean emailVerified;
```

This can have three states:

```text
true
false
null
```

That may be useful if `null` means "unknown".

But if your business logic only has two states, `boolean` is better:

```java
private boolean emailVerified;
```

Wrappers also matter in performance-sensitive code because they add object overhead and can create many allocations when used heavily.

Most business code should not obsess over this, but a mid/senior developer should know the tradeoff.

## Quick Summary

Wrapper classes are object versions of primitive types.

Collections and generics use wrappers, not primitives.

Wrappers can be `null`.

Use `.equals()` for wrapper value comparison.

Use wrappers when you need nullability or object behavior. Use primitives when the value is always required.
