---
layout: default
title: Polymorphism
parent: Java Fundamentals
grand_parent: Book
nav_order: 26
---

# Polymorphism

## Simple Explanation

Polymorphism means "many forms."

In Java, it usually means you can use a parent type or interface while the actual object has a more specific type.

Example:

```java
Animal animal = new Dog();
animal.makeSound();
```

The variable type is `Animal`.

The object is `Dog`.

When `makeSound()` is called, Java runs the `Dog` version.

## Why It Exists

Polymorphism lets code depend on abstractions instead of concrete classes.

Without polymorphism, you often write rigid code:

```java
EmailSender emailSender = new EmailSender();
emailSender.send("Hello");
```

With polymorphism:

```java
NotificationSender sender = new EmailSender();
sender.send("Hello");
```

Now the code can work with any `NotificationSender`, not only email.

That makes code more flexible.

## Basic Code Example

```java
interface NotificationSender {
    void send(String message);
}

class EmailSender implements NotificationSender {
    @Override
    public void send(String message) {
        System.out.println("Email: " + message);
    }
}

class SmsSender implements NotificationSender {
    @Override
    public void send(String message) {
        System.out.println("SMS: " + message);
    }
}
```

Usage:

```java
public class NotificationService {
    private final NotificationSender sender;

    public NotificationService(NotificationSender sender) {
        this.sender = sender;
    }

    public void notifyUser(String message) {
        sender.send(message);
    }
}
```

`NotificationService` does not care whether the sender is email or SMS.

It only cares that it can send a message.

## Common Interview Questions

### What is polymorphism?

Good answer:

```text
Polymorphism means that the same reference type can point to different concrete
object types, and method calls are resolved based on the actual object. It lets
code work with abstractions like interfaces instead of concrete implementations.
```

### What is runtime polymorphism?

Runtime polymorphism happens when an overridden method is selected at runtime based on the actual object type.

Example:

```java
Animal animal = new Dog();
animal.makeSound(); // Dog implementation
```

### What is compile-time polymorphism?

Method overloading is often called compile-time polymorphism because the compiler selects the method based on parameters.

Example:

```java
print(String text)
print(int number)
```

### Why is polymorphism useful?

It reduces coupling.

Code can depend on an interface instead of a concrete class.

That makes implementations easier to replace, test, and extend.

## Common Mistakes

Mistake 1: Thinking polymorphism only means inheritance.

Interfaces are often the cleaner way to use polymorphism in real applications.

Mistake 2: Casting too much.

Bad:

```java
if (sender instanceof EmailSender emailSender) {
    emailSender.sendEmailOnlyFeature();
}
```

If you keep checking concrete types, you may not be using polymorphism well.

Mistake 3: Creating abstractions too early.

Do not create interfaces for everything just to look professional.

An abstraction is useful when it gives real flexibility or improves boundaries.

## Mid/Senior Notes

Polymorphism is one of the foundations of clean backend architecture.

It supports patterns like:

- Strategy,
- Adapter,
- Dependency Injection,
- Ports and Adapters,
- testing with fakes or mocks.

Example:

```java
public interface UserRepository {
    User save(User user);
}
```

The business logic depends on `UserRepository`.

The infrastructure can provide:

```java
PostgresUserRepository
InMemoryUserRepository
FakeUserRepository
```

That is powerful because business code does not need to know how persistence works.

Senior-level warning:

```text
Polymorphism is useful when behavior really varies. If there is only one implementation
and no meaningful boundary, an interface may just add noise.
```

## Quick Summary

Polymorphism means one abstraction can have many concrete forms.

Overriding gives runtime polymorphism.

Overloading is compile-time polymorphism.

Interfaces are a common way to use polymorphism in backend code.

Good polymorphism reduces coupling. Bad abstraction adds noise.

