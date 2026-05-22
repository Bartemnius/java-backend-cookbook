---
layout: default
title: StringBuilder And StringBuffer
parent: Java Fundamentals
grand_parent: Book
nav_order: 14
---

# StringBuilder And StringBuffer

## Simple Explanation

`StringBuilder` and `StringBuffer` are used to build strings by modifying the same object.

This is different from `String`, which is immutable.

Example:

```java
StringBuilder builder = new StringBuilder();
builder.append("Hello");
builder.append(" ");
builder.append("Java");

String result = builder.toString();
```

Result:

```text
Hello Java
```

Simple rule:

```text
String        -> immutable text
StringBuilder -> mutable text builder, not synchronized
StringBuffer  -> mutable text builder, synchronized
```

## Why It Exists

Because repeated string changes can create many temporary `String` objects.

This is often inefficient:

```java
String result = "";

for (String word : words) {
    result += word;
}
```

Each concatenation may create a new string.

`StringBuilder` lets you append many pieces and create the final string once.

## Basic Code Example

```java
import java.util.List;

public class BuilderExample {
    public static void main(String[] args) {
        List<String> words = List.of("Java", "Backend", "Cookbook");

        StringBuilder builder = new StringBuilder();

        for (String word : words) {
            builder.append(word).append(" ");
        }

        String result = builder.toString().trim();

        System.out.println(result);
    }
}
```

Output:

```text
Java Backend Cookbook
```

## Common Interview Questions

### What is the difference between `String` and `StringBuilder`?

Good answer:

```text
String is immutable, so operations like concatenation create new strings.
StringBuilder is mutable and is useful when building a string through many changes,
especially inside loops.
```

### What is the difference between `StringBuilder` and `StringBuffer`?

`StringBuilder` is not synchronized.

`StringBuffer` is synchronized.

That means `StringBuffer` is thread-safe for individual method calls, but usually slower.

Good answer:

```text
StringBuilder is usually preferred in single-threaded code because it is faster
and not synchronized. StringBuffer is synchronized and older, so it can be used
when multiple threads need synchronized access, although in modern code we often
avoid sharing mutable builders between threads anyway.
```

### Which one should you use most of the time?

Use `StringBuilder` most of the time.

Use `StringBuffer` only when you really need its synchronization behavior.

## Common Mistakes

Mistake 1: Using `StringBuffer` by default.

Most modern code uses `StringBuilder`.

Mistake 2: Using `StringBuilder` for simple readable concatenation.

This is fine:

```java
String message = "User " + userId + " created order " + orderId;
```

Do not turn every small concatenation into a builder. That makes code uglier without real benefit.

Mistake 3: Thinking `StringBuffer` magically solves all thread-safety problems.

`StringBuffer` synchronizes individual methods, but that does not automatically make a larger multi-step operation correct.

Shared mutable state is still tricky.

## Mid/Senior Notes

Use `StringBuilder` when building strings in loops, parsers, formatters, or hot paths.

Example:

```java
StringBuilder csv = new StringBuilder();

for (User user : users) {
    csv.append(user.getId())
       .append(',')
       .append(user.getEmail())
       .append('\n');
}
```

But in normal logging or small messages, simple concatenation or formatted strings are often clearer.

In modern Java, the compiler and JVM can optimize many simple string concatenations. So the mature answer is not "always use StringBuilder."

The mature answer is:

```text
Use normal string concatenation for simple expressions. Use StringBuilder when
building strings repeatedly, especially in loops or performance-sensitive code.
```

## Quick Summary

`String` is immutable.

`StringBuilder` is mutable and usually preferred for repeated string building.

`StringBuffer` is synchronized and older.

Use `StringBuilder` in loops and repeated append operations.

Do not overuse it when simple concatenation is clearer.

