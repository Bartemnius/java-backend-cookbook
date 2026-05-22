---
layout: default
title: Interfaces
parent: Java Fundamentals
grand_parent: Book
nav_order: 28
---

# Interfaces

## Simple Explanation

An interface defines a contract.

It says what a class can do, but not necessarily how it does it.

Example:

```java
public interface NotificationSender {
    void send(String message);
}
```

Any class that implements this interface must provide the `send` method:

```java
public class EmailSender implements NotificationSender {
    @Override
    public void send(String message) {
        System.out.println("Sending email: " + message);
    }
}
```

## Why It Exists

Interfaces let code depend on behavior instead of concrete classes.

Instead of saying:

```text
I need EmailSender.
```

Your code can say:

```text
I need something that can send notifications.
```

That is more flexible.

## Basic Code Example

```java
public interface PaymentProcessor {
    PaymentResult process(Money amount);
}
```

Implementations:

```java
public class StripePaymentProcessor implements PaymentProcessor {
    @Override
    public PaymentResult process(Money amount) {
        return new PaymentResult("stripe");
    }
}

public class FakePaymentProcessor implements PaymentProcessor {
    @Override
    public PaymentResult process(Money amount) {
        return new PaymentResult("fake");
    }
}
```

Service:

```java
public class CheckoutService {
    private final PaymentProcessor paymentProcessor;

    public CheckoutService(PaymentProcessor paymentProcessor) {
        this.paymentProcessor = paymentProcessor;
    }

    public void checkout(Money amount) {
        paymentProcessor.process(amount);
    }
}
```

`CheckoutService` depends on the interface, not a specific payment provider.

## Default And Static Methods

Modern Java interfaces can have default methods:

```java
public interface Printer {
    void print(String text);

    default void printUpperCase(String text) {
        print(text.toUpperCase());
    }
}
```

They can also have static methods:

```java
public interface TextUtils {
    static boolean isBlank(String text) {
        return text == null || text.isBlank();
    }
}
```

But do not turn interfaces into dumping grounds for utility methods.

## Common Interview Questions

### What is an interface?

Good answer:

```text
An interface defines a contract that classes can implement. It lets code depend
on behavior instead of a specific implementation.
```

### Can a class implement multiple interfaces?

Yes.

```java
public class ReportService implements Auditable, Exportable {
}
```

This is one way Java supports multiple types of behavior without multiple class inheritance.

### Can interfaces have methods with implementation?

Yes.

Since Java 8, interfaces can have default and static methods.

Modern Java also allows private helper methods inside interfaces.

### When should you create an interface?

Good answer:

```text
I create an interface when there is a real abstraction or boundary: multiple
implementations, testing needs, external integration, or a stable contract that
business code should depend on.
```

## Common Mistakes

Mistake 1: Creating interfaces for every class automatically.

Bad:

```text
UserService
UserServiceImpl
```

If there is only one implementation and no real boundary, this can be useless ceremony.

Mistake 2: Putting too many methods in one interface.

Large interfaces force implementers to depend on methods they may not need.

Better:

```java
interface ReadUserRepository {
    Optional<User> findById(Long id);
}

interface WriteUserRepository {
    User save(User user);
}
```

This follows the Interface Segregation Principle.

Mistake 3: Using interfaces as constant holders.

Bad old style:

```java
public interface Constants {
    String APP_NAME = "MyApp";
}
```

Use proper configuration or a final utility class if needed.

## Mid/Senior Notes

Interfaces are central in Spring and clean architecture.

They are useful around boundaries:

```text
business logic -> interface -> infrastructure implementation
```

Example:

```java
public interface EmailClient {
    void sendEmail(EmailMessage message);
}
```

Infrastructure:

```java
public class SendGridEmailClient implements EmailClient {
}
```

Tests:

```java
public class FakeEmailClient implements EmailClient {
}
```

This makes business logic independent from SendGrid.

Senior-level point:

```text
Interfaces are valuable when they protect a boundary or express a real capability.
Interfaces created only because "every service needs an interface" often add noise.
```

## Quick Summary

Interfaces define contracts.

Classes implement interfaces.

A class can implement multiple interfaces.

Interfaces can have default, static, and private methods.

Use interfaces for real abstractions and boundaries, not automatic ceremony.

