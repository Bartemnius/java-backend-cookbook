---
layout: default
title: Strings And Immutability
parent: Java Fundamentals
grand_parent: Book
nav_order: 13
---

# Strings And Immutability

## Simple Explanation

`String` represents text in Java.

Example:

```java
String name = "Anna";
```

The most important thing to know:

```text
Strings are immutable.
```

Immutable means that once a `String` object is created, it cannot be changed.

This code does not modify the original string:

```java
String text = "hello";
text = text.toUpperCase();
```

`toUpperCase()` creates a new `String`.

Then `text` points to the new value.

The original `"hello"` string was not changed.

## Why It Exists

String immutability makes Java safer and simpler.

Strings are used everywhere:

- usernames,
- passwords,
- URLs,
- file paths,
- SQL queries,
- HTTP headers,
- configuration values,
- map keys.

If strings were freely mutable, bugs would be much easier to create.

Imagine using a string as a key in a `HashMap`, then changing the string content after insertion. The map could become inconsistent.

Immutability makes strings reliable as keys and safe to share.

## Basic Code Example

```java
public class StringExample {
    public static void main(String[] args) {
        String original = "java";
        String upper = original.toUpperCase();

        System.out.println(original); // java
        System.out.println(upper);    // JAVA
    }
}
```

`original` did not change.

`upper` is a different string.

## String Pool

Java has a special memory area for string literals called the string pool.

Example:

```java
String first = "java";
String second = "java";
```

Both variables can point to the same pooled string object.

That is why this may print `true`:

```java
System.out.println(first == second); // true
```

But do not use `==` to compare string values.

Use `.equals()`:

```java
System.out.println(first.equals(second)); // true
```

Why?

Because `==` compares references.

`.equals()` compares text content.

## Common Interview Questions

### Why is `String` immutable in Java?

Good answer:

```text
String is immutable because strings are heavily shared and used in many important
places, like map keys, class loading, security-sensitive values, and configuration.
Immutability makes them safer, thread-safe, and reliable as keys.
```

### What is the difference between `==` and `.equals()` for strings?

`==` checks whether two references point to the same object.

`.equals()` checks whether the text content is equal.

Good answer:

```text
For strings, I should use `.equals()` when I want to compare text. `==` compares
object references, so it can give surprising results.
```

### What is the string pool?

The string pool is a JVM mechanism for reusing string literals.

If the same string literal appears multiple times, Java can reuse one object instead of creating a new one each time.

### Does string concatenation always create many objects?

It depends.

Simple concatenation can be optimized by the compiler or JVM.

But repeated concatenation inside loops can still be inefficient and is usually better handled with `StringBuilder`.

## Common Mistakes

Mistake 1: Comparing strings with `==`.

```java
String a = new String("java");
String b = new String("java");

System.out.println(a == b);      // false
System.out.println(a.equals(b)); // true
```

Mistake 2: Thinking string methods modify the original string.

```java
String text = " hello ";
text.trim();

System.out.println(text); // still " hello "
```

Correct:

```java
text = text.trim();
```

Mistake 3: Concatenating strings in large loops.

```java
String result = "";

for (String item : items) {
    result += item;
}
```

This can create many intermediate strings.

Use `StringBuilder` for repeated modifications.

## Mid/Senior Notes

String immutability is not only a trivia question.

It affects:

- thread safety,
- caching,
- map keys,
- memory usage,
- API design,
- security-sensitive code.

For example, using `String` for passwords can be problematic because strings are immutable and may stay in memory until garbage collected.

In very sensitive contexts, a `char[]` may be preferred because it can be cleared manually:

```java
char[] password = {'s', 'e', 'c', 'r', 'e', 't'};
Arrays.fill(password, '\0');
```

In normal backend applications, you will still see passwords as strings often, especially when working with frameworks. But at mid/senior level, you should understand the tradeoff.

Also, do not over-optimize normal string concatenation:

```java
String message = "User " + userId + " created order " + orderId;
```

This is fine.

The problem is usually repeated concatenation in loops or hot paths.

## Quick Summary

`String` is immutable.

String methods return new strings instead of changing the original.

Use `.equals()` to compare string content.

The string pool reuses string literals.

Use `StringBuilder` for repeated string building.

