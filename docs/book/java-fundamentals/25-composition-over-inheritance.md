---
layout: default
title: Composition Over Inheritance
parent: Java Fundamentals
grand_parent: Book
nav_order: 25
---

# Composition Over Inheritance

## Simple Explanation

Composition means building a class from smaller objects instead of extending a parent class.

Inheritance says:

```text
Dog is an Animal.
```

Composition says:

```text
Car has an Engine.
OrderService has a PaymentClient.
UserController has a UserService.
```

In code:

```java
public class OrderService {
    private final PaymentClient paymentClient;

    public OrderService(PaymentClient paymentClient) {
        this.paymentClient = paymentClient;
    }
}
```

`OrderService` does not extend `PaymentClient`.

It uses one.

## Why It Exists

Composition creates flexible designs.

Instead of inheriting behavior from a parent class, your class delegates work to another object.

That makes it easier to replace, test, and change pieces independently.

Example:

```java
public interface PaymentClient {
    PaymentResult charge(Money amount);
}
```

Different implementations:

```java
public class StripePaymentClient implements PaymentClient {
    @Override
    public PaymentResult charge(Money amount) {
        return new PaymentResult("stripe");
    }
}

public class FakePaymentClient implements PaymentClient {
    @Override
    public PaymentResult charge(Money amount) {
        return new PaymentResult("fake");
    }
}
```

Now `OrderService` can use any `PaymentClient`.

That is very useful in tests.

## Basic Code Example

```java
public class EmailSender {
    public void send(String email, String message) {
        System.out.println("Sending email to " + email);
    }
}

public class RegistrationService {
    private final EmailSender emailSender;

    public RegistrationService(EmailSender emailSender) {
        this.emailSender = emailSender;
    }

    public void register(String email) {
        // create user
        emailSender.send(email, "Welcome");
    }
}
```

`RegistrationService` has an `EmailSender`.

It does not inherit from it.

This keeps responsibilities separate.

## Common Interview Questions

### What does "composition over inheritance" mean?

Good answer:

```text
It means we often prefer building classes from smaller objects instead of creating
deep inheritance hierarchies. Composition usually gives more flexibility, better
testability, and lower coupling.
```

### Is inheritance bad?

No.

Inheritance is useful when there is a real and stable is-a relationship.

But if you only want to reuse code, composition is often safer.

### What is delegation?

Delegation means one object asks another object to do part of the work.

Example:

```java
emailSender.send(email, message);
```

`RegistrationService` delegates email sending to `EmailSender`.

### How does composition relate to dependency injection?

Dependency injection is often how we provide composed dependencies.

Example:

```java
public RegistrationService(EmailSender emailSender) {
    this.emailSender = emailSender;
}
```

The dependency is passed from outside instead of created internally.

Spring uses this idea heavily.

## Common Mistakes

Mistake 1: Using inheritance for code reuse only.

If the relationship is not truly is-a, inheritance is probably wrong.

Bad:

```java
class InvoicePdfGenerator extends FileWriter {
}
```

An invoice PDF generator is not a file writer.

It may use a file writer.

Mistake 2: Creating abstract base classes too early.

Developers often create base classes before the duplication is real.

That creates a hierarchy that may not match the actual domain.

Mistake 3: Over-composing everything.

Composition is good, but tiny classes with no reason to exist can also make code annoying.

Do not split code just to look architectural.

## Mid/Senior Notes

Composition is one of the most important design habits in backend development.

It supports:

- dependency injection,
- testing with mocks/fakes,
- clean boundaries,
- strategy pattern,
- adapter pattern,
- hexagonal architecture,
- replacing infrastructure without rewriting business logic.

Example:

```java
public class OrderService {
    private final PaymentGateway paymentGateway;
    private final OrderRepository orderRepository;

    public OrderService(
            PaymentGateway paymentGateway,
            OrderRepository orderRepository
    ) {
        this.paymentGateway = paymentGateway;
        this.orderRepository = orderRepository;
    }
}
```

This design says:

```text
OrderService coordinates order behavior.
PaymentGateway handles payment integration.
OrderRepository handles persistence.
```

Each responsibility has a place.

Inheritance would make this worse if we tried:

```java
class OrderService extends PaymentGateway {
}
```

That relationship is nonsense.

Senior-level point:

```text
Composition makes dependencies visible. Inheritance often hides dependencies inside
parent classes.
```

Visible dependencies are easier to test and reason about.

## Quick Summary

Composition means building objects from other objects.

Inheritance models is-a.

Composition often models has-a or uses-a.

Prefer composition when you want flexibility, testability, and lower coupling.

Use inheritance only when the relationship is real and stable.

