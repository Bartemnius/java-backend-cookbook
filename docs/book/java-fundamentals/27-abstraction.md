---
layout: default
title: Abstraction
parent: Java Fundamentals
grand_parent: Book
nav_order: 27
---

# Abstraction

## Simple Explanation

Abstraction means hiding details and showing only what matters.

You use abstraction every day.

When you drive a car, you use:

```text
steering wheel
pedals
gear selector
```

You do not need to understand every internal engine detail to drive.

In code, abstraction works similarly.

It lets you use something through a simple contract without knowing all internal details.

## Why It Exists

Software systems are too complex to understand all at once.

Abstraction helps us separate:

```text
what something does
```

from:

```text
how it does it
```

Example:

```java
public interface PaymentGateway {
    PaymentResult charge(Money amount);
}
```

This tells us what the payment gateway can do.

It does not expose whether payment is handled by Stripe, PayPal, bank transfer, or a fake test implementation.

## Basic Code Example

```java
public interface FileStorage {
    void upload(String path, byte[] content);
}
```

Different implementations:

```java
public class LocalFileStorage implements FileStorage {
    @Override
    public void upload(String path, byte[] content) {
        System.out.println("Saving file locally: " + path);
    }
}

public class S3FileStorage implements FileStorage {
    @Override
    public void upload(String path, byte[] content) {
        System.out.println("Uploading file to S3: " + path);
    }
}
```

Business code can depend on the abstraction:

```java
public class DocumentService {
    private final FileStorage fileStorage;

    public DocumentService(FileStorage fileStorage) {
        this.fileStorage = fileStorage;
    }

    public void saveDocument(String path, byte[] content) {
        fileStorage.upload(path, content);
    }
}
```

`DocumentService` does not care where the file is stored.

That detail is hidden.

## Common Interview Questions

### What is abstraction in Java?

Good answer:

```text
Abstraction means exposing essential behavior while hiding implementation details.
In Java, we often create abstraction with interfaces and abstract classes.
```

### Why is abstraction useful?

It reduces complexity.

It lets code depend on stable contracts instead of concrete implementation details.

That makes systems easier to change and test.

### How do you create abstraction in Java?

Common tools:

- interfaces,
- abstract classes,
- normal classes with private implementation details,
- methods that expose simple behavior while hiding internal steps.

Abstraction is not only about interfaces.

Encapsulation also supports abstraction.

### What is the difference between abstraction and encapsulation?

Good answer:

```text
Abstraction is about showing only what matters. Encapsulation is about hiding and
protecting internal state or implementation details. They are related, but not
the same.
```

## Common Mistakes

Mistake 1: Thinking abstraction always means an interface.

An interface is one tool for abstraction, not the definition of abstraction.

Mistake 2: Creating abstractions too early.

Bad:

```java
UserServiceInterface
UserServiceImpl
```

If there is only one implementation and no real boundary, this may add noise.

Mistake 3: Leaking implementation details through abstraction.

Example:

```java
public interface UserRepository {
    JpaEntityManager getEntityManager();
}
```

This exposes JPA details through the abstraction.

That defeats the purpose.

## Mid/Senior Notes

Good abstraction is about choosing the right boundary.

For backend systems, useful abstraction often appears around:

- persistence,
- external APIs,
- message brokers,
- file storage,
- payment providers,
- authentication providers,
- time,
- random ID generation.

Example:

```java
public interface Clock {
    Instant now();
}
```

This may look small, but it makes time-dependent code easier to test.

Senior-level point:

```text
The best abstraction hides things that are likely to change and exposes a contract
that is stable for the business use case.
```

Bad abstraction hides nothing useful and only adds ceremony.

## Quick Summary

Abstraction hides unnecessary details.

It exposes what something does, not exactly how it does it.

Java uses interfaces and abstract classes for abstraction, but normal classes can abstract too.

Good abstraction reduces complexity.

Bad abstraction adds indirection without value.

