---
layout: default
title: Autoboxing And Unboxing
parent: Java Fundamentals
grand_parent: Book
nav_order: 12
---

# Autoboxing And Unboxing

## Simple Explanation

Autoboxing is when Java automatically converts a primitive into its wrapper class.

Unboxing is when Java automatically converts a wrapper object back into a primitive.

Example:

```java
Integer number = 10; // autoboxing: int -> Integer
int value = number;  // unboxing: Integer -> int
```

Java does this to make code less noisy.

Without autoboxing, you would need to write:

```java
Integer number = Integer.valueOf(10);
```

With autoboxing, Java does it for you:

```java
Integer number = 10;
```

## Why It Exists

Java has primitives and objects.

Collections and generics work with objects:

```java
List<Integer> numbers = new ArrayList<>();
```

But developers often work with primitive values:

```java
int number = 10;
```

Autoboxing and unboxing make it easier to move between those worlds.

They save you from constantly writing manual conversions.

## Basic Code Example

```java
import java.util.ArrayList;
import java.util.List;

public class BoxingExample {
    public static void main(String[] args) {
        List<Integer> numbers = new ArrayList<>();

        numbers.add(10);          // autoboxing: int -> Integer
        numbers.add(20);          // autoboxing: int -> Integer

        int first = numbers.get(0); // unboxing: Integer -> int

        System.out.println(first);
    }
}
```

This works because Java automatically converts between `int` and `Integer`.

## Common Interview Questions

### What is autoboxing?

Good answer:

```text
Autoboxing is the automatic conversion from a primitive type to its wrapper type,
for example from `int` to `Integer`.
```

### What is unboxing?

Good answer:

```text
Unboxing is the automatic conversion from a wrapper type to its primitive type,
for example from `Integer` to `int`.
```

### Can unboxing cause `NullPointerException`?

Yes.

This compiles:

```java
Integer value = null;
int number = value;
```

But it fails at runtime.

Why?

Because Java tries to convert `null` into an `int`.

There is no valid primitive `int` value for `null`, so Java throws `NullPointerException`.

## Common Mistakes

Mistake 1: Forgetting that wrappers can be `null`.

```java
Integer count = null;

if (count > 0) {
    System.out.println("Positive");
}
```

This throws `NullPointerException`, because `count > 0` forces unboxing.

Mistake 2: Thinking autoboxing has no cost.

Autoboxing can create wrapper objects. In normal business code, this usually does not matter much.

But in tight loops or high-performance code, unnecessary boxing can create extra memory pressure.

Mistake 3: Comparing wrapper objects with `==`.

```java
Integer a = 1000;
Integer b = 1000;

System.out.println(a == b);      // false in many cases
System.out.println(a.equals(b)); // true
```

Use `.equals()` when comparing wrapper values.

## Mid/Senior Notes

Autoboxing is convenient, but it can hide behavior.

This looks harmless:

```java
Long total = 0L;

for (long i = 0; i < 1_000_000; i++) {
    total += i;
}
```

But `Long` is immutable. Each addition requires unboxing, adding, and boxing again.

In performance-sensitive code, use primitives:

```java
long total = 0L;

for (long i = 0; i < 1_000_000; i++) {
    total += i;
}
```

In business code, the bigger risk is usually nullability:

```java
private Boolean active;
```

Does `null` mean unknown?

If not, use:

```java
private boolean active;
```

Good mid-level thinking:

```text
Autoboxing is useful, but I need to remember that wrappers can be null and that
boxing can matter in performance-sensitive code.
```

## Quick Summary

Autoboxing converts primitives to wrappers automatically.

Unboxing converts wrappers to primitives automatically.

Unboxing `null` causes `NullPointerException`.

Use primitives when a value is always required.

Use wrappers when you need nullability, generics, or object behavior.

