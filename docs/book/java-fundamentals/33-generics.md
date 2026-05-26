---
layout: default
title: Generics
parent: Java Fundamentals
grand_parent: Book
nav_order: 33
---

# Generics

## Simple Explanation

Generics let you write code that works with types in a safe way.

Example:

```java
List<String> names = new ArrayList<>();
```

This means:

```text
This list contains Strings.
```

So Java will stop you from adding the wrong type:

```java
names.add("Anna");
names.add(123); // does not compile
```

Generics are like labels for types.

They let the compiler help you before the program runs.

## Why It Exists

Before generics, collections could hold any `Object`.

Example of old-style code:

```java
List names = new ArrayList();
names.add("Anna");
names.add(123);

String first = (String) names.get(0);
String second = (String) names.get(1); // runtime error
```

This compiles, but fails at runtime because `123` is not a `String`.

Generics move that error to compile time:

```java
List<String> names = new ArrayList<>();
names.add(123); // compile-time error
```

That is much better.

Good code should fail early.

## Basic Code Example

```java
import java.util.ArrayList;
import java.util.List;

public class GenericsExample {
    public static void main(String[] args) {
        List<String> emails = new ArrayList<>();

        emails.add("anna@example.com");
        emails.add("tom@example.com");

        for (String email : emails) {
            System.out.println(email.toLowerCase());
        }
    }
}
```

Because the list is `List<String>`, Java knows every element is a `String`.

No casting is needed.

## Generic Classes

You can create your own generic class.

Example:

```java
public class Box<T> {
    private final T value;

    public Box(T value) {
        this.value = value;
    }

    public T getValue() {
        return value;
    }
}
```

Usage:

```java
Box<String> textBox = new Box<>("hello");
String text = textBox.getValue();

Box<Integer> numberBox = new Box<>(123);
Integer number = numberBox.getValue();
```

`T` is a type parameter.

It is a placeholder for a real type.

Common names:

```text
T -> Type
E -> Element
K -> Key
V -> Value
R -> Result
```

## Generic Methods

Methods can also be generic:

```java
public static <T> T first(List<T> items) {
    if (items.isEmpty()) {
        throw new IllegalArgumentException("List must not be empty");
    }

    return items.get(0);
}
```

Usage:

```java
String firstName = first(List.of("Anna", "Tom"));
Integer firstNumber = first(List.of(1, 2, 3));
```

The same method works with different types.

## Bounded Type Parameters

Sometimes you want to limit which types are allowed.

Example:

```java
public class NumberBox<T extends Number> {
    private final T value;

    public NumberBox(T value) {
        this.value = value;
    }

    public double asDouble() {
        return value.doubleValue();
    }
}
```

Now `T` must be `Number` or a subclass of `Number`.

Allowed:

```java
new NumberBox<Integer>(10);
new NumberBox<Double>(10.5);
```

Not allowed:

```java
new NumberBox<String>("hello"); // does not compile
```

## Wildcards

Wildcards use `?`.

They mean "some type."

Example:

```java
public void printAll(List<?> items) {
    for (Object item : items) {
        System.out.println(item);
    }
}
```

This method accepts:

```java
List<String>
List<Integer>
List<User>
```

But when you use `List<?>`, Java does not know the exact element type.

So you can read values as `Object`, but you cannot safely add new specific values.

## extends vs super

This is the part people often hate.

Use this simple rule:

```text
? extends T -> producer, you read T values
? super T   -> consumer, you write T values
```

This is often called PECS:

```text
Producer Extends, Consumer Super
```

Example with `extends`:

```java
public double sum(List<? extends Number> numbers) {
    double total = 0;

    for (Number number : numbers) {
        total += number.doubleValue();
    }

    return total;
}
```

This accepts:

```java
List<Integer>
List<Double>
List<Long>
```

You can read numbers from it.

Example with `super`:

```java
public void addIntegers(List<? super Integer> numbers) {
    numbers.add(1);
    numbers.add(2);
}
```

This accepts lists that can safely receive integers:

```java
List<Integer>
List<Number>
List<Object>
```

## Type Erasure

Generics are mostly a compile-time feature in Java.

At runtime, generic type information is mostly erased.

Example:

```java
List<String> names = new ArrayList<>();
List<Integer> numbers = new ArrayList<>();
```

At runtime, both are basically `ArrayList`.

This is called type erasure.

That is why this does not work:

```java
if (items instanceof List<String>) {
}
```

Java cannot check `List<String>` like that at runtime.

## Common Interview Questions

### What are generics in Java?

Good answer:

```text
Generics let us write type-safe code that works with different types. They are
commonly used in collections, like List<String>, so the compiler can prevent wrong
types from being added and reduce the need for casting.
```

### Why are generics useful?

They provide:

- type safety,
- fewer casts,
- reusable code,
- earlier error detection,
- clearer APIs.

### What is a raw type?

A raw type is a generic type used without type parameters.

Bad:

```java
List list = new ArrayList();
```

Better:

```java
List<String> list = new ArrayList<>();
```

Raw types should be avoided in modern Java because they remove type safety.

### What is type erasure?

Good answer:

```text
Type erasure means Java removes most generic type information at runtime.
Generics give type safety mostly at compile time, but the JVM does not keep full
information like List<String> versus List<Integer> at runtime.
```

### What is the difference between `List<Object>` and `List<?>`?

`List<Object>` means a list that can contain objects of type `Object`.

`List<?>` means a list of some unknown type.

Important:

```java
List<String> strings = List.of("a", "b");

List<?> unknown = strings;       // allowed
List<Object> objects = strings;  // not allowed
```

Why?

Because if `List<String>` were assignable to `List<Object>`, you could add an `Integer` into a list of strings.

That would break type safety.

## Common Mistakes

Mistake 1: Using raw types.

```java
List users = new ArrayList();
```

Use:

```java
List<User> users = new ArrayList<>();
```

Mistake 2: Thinking generics exist fully at runtime.

Because of type erasure, you cannot normally check:

```java
items instanceof List<String>
```

Mistake 3: Thinking `List<String>` is a subtype of `List<Object>`.

It is not.

Even though `String` is an `Object`, `List<String>` is not a `List<Object>`.

Mistake 4: Overcomplicating APIs with wildcards.

Wildcards are useful, but too many of them can make APIs hard to read.

If a simple type parameter works, use the simple thing.

## Mid/Senior Notes

Generics are especially important when designing reusable APIs.

Good generic API:

```java
public interface Repository<T, ID> {
    Optional<T> findById(ID id);
    T save(T entity);
}
```

Usage:

```java
Repository<User, Long> userRepository;
Repository<Order, UUID> orderRepository;
```

The abstraction is reusable and type-safe.

But generics can also become unreadable.

Bad:

```java
Processor<T extends Comparable<? super T>, R extends Result<T>>
```

Sometimes this is necessary in libraries.

In normal business code, it is often overengineering.

Senior-level answer:

```text
Generics are great for type safety and reusable APIs, but I avoid making business
code overly generic unless there is a real repeated pattern. Clear code usually
beats clever generic abstractions.
```

## Quick Summary

Generics make code type-safe.

They reduce casting.

They are heavily used in collections.

Avoid raw types.

`? extends T` is useful for reading producers.

`? super T` is useful for writing consumers.

Java generics use type erasure, so most generic type information is not available at runtime.

