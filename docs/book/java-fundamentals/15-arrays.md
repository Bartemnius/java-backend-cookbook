---
layout: default
title: Arrays
parent: Java Fundamentals
grand_parent: Book
nav_order: 15
---

# Arrays

## Simple Explanation

An array stores multiple values of the same type in a fixed-size container.

Example:

```java
int[] numbers = {10, 20, 30};
```

This array has three elements:

```text
index 0 -> 10
index 1 -> 20
index 2 -> 30
```

Arrays use zero-based indexing.

That means the first element is at index `0`, not index `1`.

## Why It Exists

Programs often need to work with many values:

- scores,
- IDs,
- names,
- request times,
- search results,
- bytes from a file.

An array lets you store those values together and access them by index.

Arrays are simple and fast, but they have one major limitation:

```text
Array size is fixed after creation.
```

If you need a collection that grows and shrinks easily, you usually use `ArrayList`.

## Basic Code Example

```java
public class ArrayExample {
    public static void main(String[] args) {
        String[] names = new String[3];

        names[0] = "Anna";
        names[1] = "Tom";
        names[2] = "Maria";

        for (String name : names) {
            System.out.println(name);
        }
    }
}
```

Output:

```text
Anna
Tom
Maria
```

## Array Length

Arrays use the `length` field:

```java
int[] numbers = {1, 2, 3};

System.out.println(numbers.length); // 3
```

Important:

```text
array.length
```

Not:

```text
array.length()
```

`length()` is used by `String`:

```java
String text = "java";
System.out.println(text.length());
```

This small difference appears in beginner interviews more often than it should.

## Common Interview Questions

### What is an array in Java?

Good answer:

```text
An array is a fixed-size container that stores elements of the same type and lets
us access them by index.
```

### Are arrays fixed size?

Yes.

After an array is created, its size cannot be changed.

You can change elements inside the array, but not the array length.

### What happens if you access an invalid index?

Java throws `ArrayIndexOutOfBoundsException`.

Example:

```java
int[] numbers = {1, 2, 3};
System.out.println(numbers[3]);
```

Valid indexes are `0`, `1`, and `2`.

Index `3` is outside the array.

### What is the difference between an array and `ArrayList`?

Good answer:

```text
An array has fixed size and uses indexes directly. ArrayList is a resizable list
built on top of an array internally. For most business code, ArrayList is more
convenient when the number of elements can change.
```

## Common Mistakes

Mistake 1: Off-by-one errors.

This is wrong:

```java
for (int i = 0; i <= numbers.length; i++) {
    System.out.println(numbers[i]);
}
```

It should be:

```java
for (int i = 0; i < numbers.length; i++) {
    System.out.println(numbers[i]);
}
```

Mistake 2: Confusing `length` and `length()`.

```java
numbers.length; // array
text.length();  // String
```

Mistake 3: Thinking arrays are always better because they are fast.

Arrays are useful, but in normal application code `List` is often easier to work with.

Use arrays when fixed size, low-level APIs, performance, or simple indexed storage really matter.

## Mid/Senior Notes

Arrays are important because many higher-level structures are built on top of them.

For example, `ArrayList` internally uses an array. When it runs out of space, it creates a larger array and copies elements into it.

That is why adding to an `ArrayList` is usually fast, but occasionally more expensive when resizing happens.

Arrays also matter in algorithms.

Many interview problems use arrays because they are simple and force you to reason about:

- indexes,
- boundaries,
- time complexity,
- space complexity,
- sorting,
- two pointers,
- sliding windows,
- binary search.

A strong backend developer does not need to use arrays everywhere in business code.

But they should be comfortable with arrays because arrays are the base for many data structures and interview tasks.

## Quick Summary

Arrays store elements of the same type.

Array size is fixed after creation.

Arrays use zero-based indexing.

Invalid indexes throw `ArrayIndexOutOfBoundsException`.

Use arrays when fixed-size indexed storage makes sense.

Use `ArrayList` when you need a resizable collection.

