---
layout: default
title: SOLID Principles In Java
parent: Intermediate And Advanced Java
grand_parent: Book
nav_order: 15
---

# SOLID Principles In Java

## Simple Explanation

SOLID is a set of five object-oriented design principles.

They are meant to make code easier to change, test, and maintain.

The five principles:

```text
S -> Single Responsibility Principle
O -> Open/Closed Principle
L -> Liskov Substitution Principle
I -> Interface Segregation Principle
D -> Dependency Inversion Principle
```

SOLID is not a religion.

It is a vocabulary for talking about design quality.

## Single Responsibility Principle

A class should have one main reason to change.

Weak:

```java
public class UserService {
    public void createUser() {
        // validate user
        // save to database
        // send email
        // generate PDF
        // write audit file
    }
}
```

This class has too many reasons to change.

Better:

```java
UserService
EmailSender
AuditLogger
PdfGenerator
```

Each class has a clearer job.

## Open/Closed Principle

Code should be open for extension but closed for modification.

This does not mean you never edit code.

It means good design lets you add new behavior without constantly changing stable existing logic.

Example:

```java
public interface DiscountPolicy {
    Money apply(Order order);
}
```

New discount policies can be added as new implementations.

## Liskov Substitution Principle

Subtypes should be usable wherever the parent type is expected.

Bad example:

```java
class Bird {
    void fly() {
    }
}

class Penguin extends Bird {
    @Override
    void fly() {
        throw new UnsupportedOperationException();
    }
}
```

If code expects a `Bird` to fly, `Penguin` breaks that expectation.

The model is wrong.

## Interface Segregation Principle

Clients should not depend on methods they do not use.

Bad:

```java
public interface Worker {
    void work();
    void eat();
}
```

What about a robot worker?

Better:

```java
public interface Workable {
    void work();
}

public interface Eatable {
    void eat();
}
```

Smaller interfaces are often easier to implement and test.

## Dependency Inversion Principle

High-level code should depend on abstractions, not low-level concrete details.

Weak:

```java
public class OrderService {
    private final StripeClient stripeClient = new StripeClient();
}
```

Better:

```java
public class OrderService {
    private final PaymentGateway paymentGateway;

    public OrderService(PaymentGateway paymentGateway) {
        this.paymentGateway = paymentGateway;
    }
}
```

Now `OrderService` depends on a capability, not a specific provider.

## Common Interview Questions

### What is SOLID?

Good answer:

```text
SOLID is a set of five design principles that help make object-oriented code more
maintainable, testable, and easier to change.
```

### Which SOLID principle is most important in backend work?

There is no single winner, but SRP and Dependency Inversion appear constantly in backend design.

SRP helps keep classes focused.

Dependency Inversion helps separate business logic from infrastructure.

### Does SOLID mean more classes?

Not always.

Bad SOLID creates pointless abstractions.

Good SOLID reduces real coupling and makes change easier.

### What is Dependency Inversion?

Good answer:

```text
Dependency Inversion means high-level business code should depend on abstractions,
not concrete low-level details. This makes code easier to test and change.
```

## Common Mistakes

Mistake 1: Treating SOLID as law.

Principles are tools, not commandments.

Mistake 2: Creating interfaces for everything.

That is not automatically good design.

Mistake 3: Splitting tiny classes too early.

If abstraction does not reduce real complexity, it may add noise.

Mistake 4: Ignoring business boundaries.

SOLID does not replace understanding the domain.

## Mid/Senior Notes

In interviews, do not recite definitions only.

Connect SOLID to real decisions:

- Why is this class too large?
- Why is this dependency hard to test?
- Why does this interface force unused methods?
- Why does this inheritance hierarchy break expectations?
- How would you change this code safely?

Senior-level answer:

```text
I use SOLID as a way to reason about change. If code is hard to test, hard to
modify, or one change breaks unrelated features, there is probably a design problem.
But I do not create abstractions just to satisfy acronyms.
```

## Quick Summary

SOLID helps discuss maintainable design.

SRP keeps classes focused.

Open/Closed supports extension without constant modification.

LSP protects substitutability.

ISP favors smaller interfaces.

DIP separates business logic from concrete infrastructure.

Use SOLID pragmatically.
