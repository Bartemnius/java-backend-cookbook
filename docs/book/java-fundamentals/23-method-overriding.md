---
layout: default
title: Method Overriding
parent: Java Fundamentals
grand_parent: Book
nav_order: 23
---

# Method Overriding

## Simple Explanation

Method overriding means a subclass provides its own implementation of a method inherited from a parent class or interface.

Example:

```java
class Animal {
    void makeSound() {
        System.out.println("Some sound");
    }
}

class Dog extends Animal {
    @Override
    void makeSound() {
        System.out.println("Bark");
    }
}
```

`Dog` overrides `makeSound()`.

## Why It Exists

Overriding lets different types behave differently through a common API.

Example:

```java
Animal animal = new Dog();
animal.makeSound(); // Bark
```

The variable type is `Animal`.

The actual object is `Dog`.

At runtime, Java calls the `Dog` implementation.

That is runtime polymorphism.

## Basic Code Example

```java
abstract class NotificationSender {
    abstract void send(String message);
}

class EmailSender extends NotificationSender {
    @Override
    void send(String message) {
        System.out.println("Sending email: " + message);
    }
}

class SmsSender extends NotificationSender {
    @Override
    void send(String message) {
        System.out.println("Sending SMS: " + message);
    }
}
```

Usage:

```java
NotificationSender sender = new EmailSender();
sender.send("Welcome");
```

The call goes to `EmailSender`.

## Common Interview Questions

### What is method overriding?

Good answer:

```text
Method overriding happens when a subclass provides a specific implementation of
a method defined in a parent class or interface. The method called is chosen at
runtime based on the actual object type.
```

### Is overriding compile-time or runtime polymorphism?

Overriding is runtime polymorphism.

The actual method is selected at runtime.

### What is the difference between overloading and overriding?

Good answer:

```text
Overloading means same method name with different parameters in the same class or
related classes, resolved at compile time. Overriding means a subclass changes the
implementation of an inherited method, resolved at runtime.
```

### Why should you use `@Override`?

`@Override` tells the compiler you intend to override a method.

If you make a mistake in the method signature, the compiler catches it.

Example:

```java
@Override
public String toStrng() {
    return "broken";
}
```

This does not compile because `toStrng` does not override `toString`.

That is good.

## Common Mistakes

Mistake 1: Forgetting `@Override`.

The code may still work, but you lose compiler protection.

Mistake 2: Accidentally overloading instead of overriding.

```java
class Parent {
    void process(Object value) {
    }
}

class Child extends Parent {
    void process(String value) {
    }
}
```

This is overloading, not overriding.

The parameter type changed.

Mistake 3: Trying to override static methods.

Static methods are not overridden in the normal polymorphic sense. They are hidden.

## Mid/Senior Notes

Overriding is powerful, but it creates coupling between parent and child classes.

If a parent class changes behavior, subclasses can be affected.

This is one reason deep inheritance trees become fragile.

Example problem:

```text
BaseService
  UserService
    PremiumUserService
      EnterprisePremiumUserService
```

At some point, it becomes hard to know which method is actually running and which class owns the behavior.

Good overriding is usually based on a stable abstraction:

```java
interface PaymentProvider {
    PaymentResult charge(Money amount);
}
```

Different implementations can override behavior cleanly:

```java
class StripePaymentProvider implements PaymentProvider {
    @Override
    public PaymentResult charge(Money amount) {
        // Stripe-specific logic
    }
}
```

This is usually cleaner than a deep class hierarchy.

## Quick Summary

Overriding means replacing inherited behavior in a subclass.

It is runtime polymorphism.

Use `@Override`.

Changing method parameters creates overloading, not overriding.

Deep overriding chains can make systems hard to understand.

