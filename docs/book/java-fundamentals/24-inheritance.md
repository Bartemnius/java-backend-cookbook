---
layout: default
title: Inheritance
parent: Java Fundamentals
grand_parent: Book
nav_order: 24
---

# Inheritance

## Simple Explanation

Inheritance lets one class reuse behavior and state from another class.

Example:

```java
class Animal {
    void eat() {
        System.out.println("Eating");
    }
}

class Dog extends Animal {
    void bark() {
        System.out.println("Barking");
    }
}
```

`Dog` inherits `eat()` from `Animal`.

Simple mental model:

```text
Dog is an Animal.
```

That kind of relationship is called an "is-a" relationship.

## Why It Exists

Inheritance exists to share common behavior and model relationships between types.

Instead of repeating common code:

```java
class Dog {
    void eat() {
        System.out.println("Eating");
    }
}

class Cat {
    void eat() {
        System.out.println("Eating");
    }
}
```

You can move shared behavior into a parent class:

```java
class Animal {
    void eat() {
        System.out.println("Eating");
    }
}
```

Then `Dog` and `Cat` can extend `Animal`.

## Basic Code Example

```java
class Vehicle {
    private final String brand;

    Vehicle(String brand) {
        this.brand = brand;
    }

    String getBrand() {
        return brand;
    }
}

class Car extends Vehicle {
    private final int doors;

    Car(String brand, int doors) {
        super(brand);
        this.doors = doors;
    }

    int getDoors() {
        return doors;
    }
}
```

`super(brand)` calls the parent constructor.

## Common Interview Questions

### What is inheritance?

Good answer:

```text
Inheritance allows one class to extend another class and reuse or specialize its
behavior. It models an is-a relationship, like Dog is an Animal.
```

### Does Java support multiple inheritance of classes?

No.

Java does not allow a class to extend multiple classes.

This does not compile:

```java
class C extends A, B {
}
```

But Java allows a class to implement multiple interfaces:

```java
class Service implements Auditable, Retryable {
}
```

### What is `super`?

`super` refers to the parent class.

It can be used to call a parent constructor or method.

Example:

```java
super(brand);
super.toString();
```

### What is the difference between inheritance and composition?

Inheritance means a class extends another class.

Composition means a class contains and uses another object.

Good answer:

```text
Inheritance models an is-a relationship. Composition models a has-a relationship.
In real applications, composition is often preferred because it creates looser
coupling and more flexible designs.
```

## Common Mistakes

Mistake 1: Using inheritance only to reuse code.

This is dangerous.

Inheritance should model a real relationship, not just copy behavior cheaply.

Mistake 2: Creating deep inheritance trees.

Deep hierarchies become hard to understand and change.

Mistake 3: Overriding behavior in surprising ways.

If a subclass changes parent behavior in a way callers do not expect, bugs appear.

## Mid/Senior Notes

Inheritance is useful, but it is also easy to abuse.

The biggest problem is coupling.

A child class depends on details of the parent class.

If the parent changes, child classes may break.

This is especially painful with deep hierarchies:

```text
AbstractBaseController
  SecuredController
    AdminController
      ReportingAdminController
```

At some point, behavior is scattered across many levels.

You need to jump between classes to understand what is happening.

Senior-level answer:

```text
Inheritance is useful for stable is-a relationships, but I avoid using it only for
code reuse. In business applications, composition is often easier to test, change,
and reason about.
```

Inheritance is not bad.

Bad inheritance is bad.

## Quick Summary

Inheritance lets a class extend another class.

It models an is-a relationship.

Java supports single inheritance of classes.

Java supports multiple interfaces.

Use inheritance carefully because it creates coupling between parent and child classes.

