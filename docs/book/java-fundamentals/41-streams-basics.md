---
layout: default
title: Streams Basics
parent: Java Fundamentals
grand_parent: Book
nav_order: 41
---

# Streams Basics

## Simple Explanation

A stream is a way to process data step by step.

It is often used with collections.

Example:

```java
List<String> names = List.of("Anna", "Tom", "Maria");

List<String> upperNames = names.stream()
        .map(String::toUpperCase)
        .toList();
```

This means:

```text
take names
turn each name uppercase
collect the result into a list
```

Streams let you describe what you want to do with data.

## Why It Exists

Before streams, you often wrote loops for every transformation.

Example:

```java
List<String> upperNames = new ArrayList<>();

for (String name : names) {
    upperNames.add(name.toUpperCase());
}
```

This is fine.

Streams provide another style:

```java
List<String> upperNames = names.stream()
        .map(String::toUpperCase)
        .toList();
```

For simple transformations, streams can be more readable.

For complex logic, normal loops may still be better.

## Basic Stream Operations

Common operations:

```text
filter -> keep only matching elements
map    -> transform each element
sorted -> sort elements
limit  -> take only some elements
forEach -> perform action for each element
collect/toList -> produce final result
```

Example:

```java
List<String> result = names.stream()
        .filter(name -> name.length() > 3)
        .map(String::toUpperCase)
        .sorted()
        .toList();
```

This:

1. keeps names longer than 3 characters,
2. converts them to uppercase,
3. sorts them,
4. returns a list.

## Intermediate vs Terminal Operations

Streams have two kinds of operations:

```text
intermediate operations -> return another stream
terminal operations     -> finish the stream
```

Intermediate:

```java
filter()
map()
sorted()
limit()
```

Terminal:

```java
toList()
collect()
forEach()
count()
findFirst()
anyMatch()
```

Nothing actually runs until a terminal operation is called.

Example:

```java
names.stream()
        .filter(name -> {
            System.out.println(name);
            return name.length() > 3;
        });
```

This prints nothing because there is no terminal operation.

Add `toList()`:

```java
names.stream()
        .filter(name -> {
            System.out.println(name);
            return name.length() > 3;
        })
        .toList();
```

Now the stream runs.

## Common Interview Questions

### What is a stream in Java?

Good answer:

```text
A stream is a sequence of elements that can be processed with operations like
filter, map, sorted, and collect. Streams do not store data themselves; they process
data from a source like a collection.
```

### What is the difference between `map` and `filter`?

`filter` keeps or removes elements.

`map` transforms elements.

Example:

```java
users.stream()
        .filter(User::isActive)
        .map(User::getEmail)
        .toList();
```

This keeps active users and transforms them into emails.

### What is a terminal operation?

A terminal operation ends the stream and produces a result or side effect.

Examples:

```java
toList()
count()
forEach()
findFirst()
anyMatch()
```

### Are streams always better than loops?

No.

Streams are great for clear data transformations.

Loops are often better for complex logic, early returns, debugging, or imperative workflows.

## Common Mistakes

Mistake 1: Making streams too complex.

Bad:

```java
var result = users.stream()
        .filter(...)
        .flatMap(...)
        .collect(...)
        .entrySet()
        .stream()
        .filter(...)
        .map(...)
        .toList();
```

If nobody can read it, it is not good code.

Mistake 2: Using `forEach` for everything.

Streams are usually best for transformations.

If you only use:

```java
stream().forEach(...)
```

ask whether a normal loop would be clearer.

Mistake 3: Expecting streams to run without terminal operation.

Intermediate operations are lazy.

No terminal operation means no execution.

Mistake 4: Modifying external state inside streams.

Bad:

```java
List<String> emails = new ArrayList<>();

users.stream()
        .filter(User::isActive)
        .forEach(user -> emails.add(user.getEmail()));
```

Better:

```java
List<String> emails = users.stream()
        .filter(User::isActive)
        .map(User::getEmail)
        .toList();
```

## Mid/Senior Notes

Streams are a tool, not a religion.

Good stream code is usually:

- short,
- linear,
- transformation-focused,
- free of surprising side effects.

Example:

```java
List<String> activeEmails = users.stream()
        .filter(User::isActive)
        .map(User::getEmail)
        .toList();
```

This is excellent.

But if the logic needs multiple branches, error handling, logging, or early returns, a normal loop can be better.

Senior-level answer:

```text
I use streams for clear data transformations. I avoid turning complex business
logic into unreadable stream pipelines. Readability matters more than looking
functional.
```

Also, be careful with parallel streams.

Parallel streams are not automatically faster.

They can cause problems with blocking IO, shared mutable state, transaction context, and thread pool behavior.

Use them only when you understand the workload and have measured the benefit.

## Quick Summary

Streams process sequences of data.

`filter` keeps matching elements.

`map` transforms elements.

Intermediate operations are lazy.

Terminal operations trigger execution.

Streams are useful for clear transformations.

Loops are still fine when they are clearer.

