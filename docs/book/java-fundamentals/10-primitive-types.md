---
layout: default
title: Primitive Types
parent: Java Fundamentals
grand_parent: Book
nav_order: 10
---

# Primitive Types


## Simple Explanation

Primitive types are Java's basic value types.

They are not objects.

Java has eight primitive types:

```text
byte
short
int
long
float
double
char
boolean
```

They store simple values directly:

```java
int age = 30;
boolean active = true;
double price = 19.99;
char grade = 'A';
```

## Why It Exists

Primitive types are efficient and simple.

You do not need a full object just to store a number or a boolean flag.

For example:

```java
int count = 10;
```

This is simpler and usually more efficient than using an object wrapper:

```java
Integer count = 10;
```

## Basic Code Example

```java
public class PrimitiveExample {
    public static void main(String[] args) {
        int usersCount = 100;
        long totalRequests = 5_000_000L;
        double averageResponseTime = 123.45;
        boolean serviceHealthy = true;

        System.out.println(usersCount);
        System.out.println(totalRequests);
        System.out.println(averageResponseTime);
        System.out.println(serviceHealthy);
    }
}
```

Notice the `L` after the long literal:

```java
long totalRequests = 5_000_000L;
```

That tells Java this number should be treated as a `long`.

## Common Interview Questions

### What are primitive types in Java?

Good answer:

```text
Primitive types are Java's basic non-object value types, like `int`, `long`,
`double`, and `boolean`. They store simple values and are different from wrapper
classes like `Integer` or `Long`.
```

### Is `String` a primitive type?

No.

`String` is a class.

This is a very common beginner mistake.

### What is the default value of primitive fields?

Primitive fields have default values:

```text
int     -> 0
long    -> 0L
double  -> 0.0
boolean -> false
char    -> '\u0000'
```

But local variables must be initialized before use.

This will not compile:

```java
public void test() {
    int number;
    System.out.println(number);
}
```

## Common Mistakes

Mistake 1: Thinking primitives can be `null`.

They cannot.

This does not compile:

```java
int age = null;
```

If you need a nullable number, you need a wrapper type:

```java
Integer age = null;
```

Mistake 2: Comparing floating-point values carelessly.

Floating-point numbers can have precision issues.

This is risky:

```java
double result = 0.1 + 0.2;
System.out.println(result == 0.3);
```

For money, prefer `BigDecimal`, not `double`.

Mistake 3: Forgetting integer division.

```java
System.out.println(5 / 2); // 2
```

Both numbers are integers, so Java performs integer division.

Use:

```java
System.out.println(5.0 / 2); // 2.5
```

## Mid/Senior Notes

Primitive types matter for performance, memory usage, and API design.

For example, `int[]` is much more memory-efficient than `Integer[]`, because `Integer[]` stores references to wrapper objects.

In high-performance code, this can matter a lot.

But in normal business code, readability and correct null handling often matter more.

Important practical rule:

```text
Use primitives when a value is required.
Use wrappers when nullability, generics, or object behavior is needed.
```

Example:

```java
private int retryCount;        // always has a value
private Integer age;           // may be unknown
```

## Quick Summary

Java has eight primitive types.

Primitives are not objects.

Primitives cannot be `null`.

`String` is not a primitive.

Use primitives for required simple values, and wrappers when you need nullability or object behavior.

