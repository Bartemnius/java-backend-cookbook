---
layout: default
title: Abstract Classes
parent: Java Fundamentals
grand_parent: Book
nav_order: 29
---

# Abstract Classes

## Simple Explanation

An abstract class is a class that cannot be instantiated directly.

It can contain:

- fields,
- constructors,
- normal methods,
- abstract methods.

Example:

```java
public abstract class Animal {
    public void eat() {
        System.out.println("Eating");
    }

    public abstract void makeSound();
}
```

Subclass:

```java
public class Dog extends Animal {
    @Override
    public void makeSound() {
        System.out.println("Bark");
    }
}
```

You cannot do:

```java
Animal animal = new Animal(); // does not compile
```

Because `Animal` is abstract.

## Why It Exists

Abstract classes are useful when you want to share some common implementation but still force subclasses to provide specific behavior.

Example:

```text
All reports have a title.
All reports can be exported.
But each report builds its content differently.
```

An abstract class can hold the shared logic and leave the variable part abstract.

## Basic Code Example

```java
public abstract class Report {
    private final String title;

    protected Report(String title) {
        this.title = title;
    }

    public void printTitle() {
        System.out.println(title);
    }

    public abstract String buildContent();
}
```

Concrete class:

```java
public class SalesReport extends Report {
    public SalesReport() {
        super("Sales Report");
    }

    @Override
    public String buildContent() {
        return "Sales data...";
    }
}
```

`Report` shares title behavior.

`SalesReport` provides specific content.

## Common Interview Questions

### What is an abstract class?

Good answer:

```text
An abstract class is a class that cannot be instantiated directly. It can contain
both implemented methods and abstract methods that subclasses must implement.
```

### Can an abstract class have a constructor?

Yes.

The constructor is called when a subclass is created.

Example:

```java
protected Report(String title) {
    this.title = title;
}
```

### Can an abstract class have no abstract methods?

Yes.

A class can be abstract even if all its methods are implemented.

That means you still cannot instantiate it directly.

### What is the difference between an interface and an abstract class?

Good answer:

```text
An interface defines a contract and is usually used to describe capability.
An abstract class can define shared state and shared implementation for related
classes. A class can implement many interfaces, but it can extend only one class.
```

## Common Mistakes

Mistake 1: Using abstract classes only for code reuse.

If there is no real type relationship, composition may be better.

Mistake 2: Creating deep abstract hierarchies.

This can make behavior hard to trace.

Mistake 3: Choosing abstract class when an interface would be enough.

If you only need a contract, use an interface.

## Mid/Senior Notes

Abstract classes are useful when there is a strong relationship and shared implementation.

But in modern backend development, interfaces plus composition are often more flexible.

Example where abstract class can be reasonable:

```java
public abstract class BaseCsvExporter {
    public final String export() {
        return header() + "\n" + rows();
    }

    protected abstract String header();

    protected abstract String rows();
}
```

This uses the Template Method pattern.

The base class controls the algorithm structure.

Subclasses fill in details.

But be careful. Template Method can become rigid if overused.

Senior-level answer:

```text
I use abstract classes when I need shared state or shared implementation across
closely related classes. If I only need a contract or want loose coupling, I usually
prefer an interface.
```

## Quick Summary

Abstract classes cannot be instantiated directly.

They can contain fields, constructors, concrete methods, and abstract methods.

Subclasses implement abstract methods.

Use abstract classes for shared implementation in related classes.

Use interfaces when you mainly need a contract.

