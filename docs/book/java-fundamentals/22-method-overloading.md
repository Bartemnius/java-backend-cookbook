---
layout: default
title: Method Overloading
parent: Java Fundamentals
grand_parent: Book
nav_order: 22
---

# Method Overloading

## Simple Explanation

Method overloading means having multiple methods with the same name but different parameter lists.

Example:

```java
public class Printer {
    public void print(String text) {
        System.out.println(text);
    }

    public void print(int number) {
        System.out.println(number);
    }

    public void print(String text, int copies) {
        for (int i = 0; i < copies; i++) {
            System.out.println(text);
        }
    }
}
```

All methods are named `print`, but their parameters are different.

## Why It Exists

Sometimes one operation can accept different inputs.

Example:

```java
print("hello");
print(123);
print("hello", 3);
```

Overloading lets you use one meaningful method name instead of inventing awkward names:

```java
printString()
printNumber()
printStringManyTimes()
```

That would be noisy.

## Basic Code Example

```java
public class Calculator {
    public int add(int a, int b) {
        return a + b;
    }

    public double add(double a, double b) {
        return a + b;
    }

    public int add(int a, int b, int c) {
        return a + b + c;
    }
}
```

Usage:

```java
Calculator calculator = new Calculator();

calculator.add(1, 2);
calculator.add(1.5, 2.5);
calculator.add(1, 2, 3);
```

The compiler chooses the correct method based on arguments.

## Common Interview Questions

### What is method overloading?

Good answer:

```text
Method overloading means defining multiple methods with the same name but different
parameter lists. The compiler chooses which method to call based on the arguments.
```

### Is overloading compile-time or runtime polymorphism?

Overloading is compile-time polymorphism.

The method is selected by the compiler based on the method signature.

### Can methods be overloaded only by return type?

No.

This does not compile:

```java
public int getValue() {
    return 1;
}

public String getValue() {
    return "one";
}
```

Return type alone is not enough.

The parameter list must be different.

### What is a method signature?

For overloading, the important parts are method name and parameter types.

Return type is not enough to overload a method.

## Common Mistakes

Mistake 1: Thinking different return type is enough.

It is not.

Mistake 2: Creating too many overloads.

Too many overloads can make code confusing.

Example:

```java
createUser(String email)
createUser(String email, boolean active)
createUser(String email, boolean active, boolean verified)
createUser(String email, boolean active, boolean verified, String role)
```

At some point, a parameter object or builder may be clearer.

Mistake 3: Ambiguous overloads with `null`.

```java
public void process(String value) {
}

public void process(Integer value) {
}

process(null); // ambiguous
```

The compiler may not know which method you mean.

## Mid/Senior Notes

Overloading can improve API ergonomics.

But it can also hide complexity.

Good overloads usually represent the same operation with different convenient inputs:

```java
findById(Long id)
findById(String id)
```

Weak overloads often hide different behavior under one name:

```java
process(User user)
process(Order order)
process(File file)
process(HttpRequest request)
```

If these operations are conceptually different, separate names may be clearer.

Senior-level thinking:

```text
Overloading is useful when methods do the same conceptual thing. If overloads
start doing unrelated work, the API becomes harder to understand.
```

## Quick Summary

Overloading means same method name, different parameters.

It is resolved at compile time.

Return type alone cannot overload a method.

Use overloading to improve readability, not to hide unrelated behavior.

