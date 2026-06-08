---
layout: default
title: Common Design Patterns In Java
parent: Intermediate And Advanced Java
grand_parent: Book
nav_order: 16
---

# Common Design Patterns In Java

## Simple Explanation

A design pattern is a common solution to a common design problem.

Patterns are not magic.

They are names for ideas developers use repeatedly.

Good pattern usage makes code easier to understand.

Bad pattern usage makes code overengineered.

## Why It Exists

Software problems repeat.

Examples:

- choose one of many algorithms,
- adapt one API to another,
- create objects in a controlled way,
- notify many listeners,
- decorate behavior,
- separate business logic from infrastructure.

Design patterns give these solutions shared names.

That helps developers communicate.

## Strategy Pattern

Use Strategy when you have interchangeable behavior.

Example:

```java
public interface DiscountPolicy {
    Money apply(Order order);
}
```

Implementations:

```java
public class NoDiscount implements DiscountPolicy {
    public Money apply(Order order) {
        return Money.zero();
    }
}

public class PercentageDiscount implements DiscountPolicy {
    public Money apply(Order order) {
        return order.total().multiplyByPercent(10);
    }
}
```

Instead of giant `if` statements, behavior is separated into strategies.

## Factory Pattern

Use Factory when object creation has logic.

Example:

```java
public class PaymentClientFactory {
    public PaymentClient create(PaymentProvider provider) {
        return switch (provider) {
            case STRIPE -> new StripePaymentClient();
            case PAYPAL -> new PayPalPaymentClient();
        };
    }
}
```

Factories hide creation details.

Do not create factories for simple `new` calls with no logic.

## Adapter Pattern

Use Adapter when you need to wrap one interface to match another.

Example:

```java
public interface EmailClient {
    void send(EmailMessage message);
}
```

Third-party client:

```java
public class SendGridSdk {
    public void sendEmail(String to, String subject, String body) {
    }
}
```

Adapter:

```java
public class SendGridEmailClient implements EmailClient {
    private final SendGridSdk sdk;

    public SendGridEmailClient(SendGridSdk sdk) {
        this.sdk = sdk;
    }

    public void send(EmailMessage message) {
        sdk.sendEmail(message.to(), message.subject(), message.body());
    }
}
```

Your business code depends on `EmailClient`, not SendGrid.

## Builder Pattern

Use Builder when object creation has many optional parameters.

Example:

```java
User user = User.builder()
        .email("anna@example.com")
        .displayName("Anna")
        .active(true)
        .build();
```

Builder can improve readability for complex object construction.

But for simple objects, constructors or records are often better.

## Decorator Pattern

Use Decorator to add behavior around another object with the same interface.

Example:

```java
public class LoggingPaymentGateway implements PaymentGateway {
    private final PaymentGateway delegate;

    public LoggingPaymentGateway(PaymentGateway delegate) {
        this.delegate = delegate;
    }

    public PaymentResult charge(Money amount) {
        System.out.println("Charging: " + amount);
        return delegate.charge(amount);
    }
}
```

This wraps another `PaymentGateway`.

Spring AOP and proxies often feel similar to decorators.

## Common Interview Questions

### What is a design pattern?

Good answer:

```text
A design pattern is a named, reusable solution to a common design problem. It is
not code you copy blindly, but a design idea that helps communicate structure.
```

### Which patterns are common in backend Java?

Common ones:

- Strategy,
- Factory,
- Adapter,
- Builder,
- Decorator,
- Proxy,
- Template Method,
- Repository.

### What is Strategy pattern?

Good answer:

```text
Strategy lets us define interchangeable behavior behind a common interface, so
we can choose or replace algorithms without changing the caller.
```

### What is Adapter pattern?

Adapter wraps one API and exposes another API expected by our code.

This is very common when integrating external services.

## Common Mistakes

Mistake 1: Forcing patterns everywhere.

If the simple code is clear, keep it simple.

Mistake 2: Learning pattern names without understanding problems.

The problem matters more than the name.

Mistake 3: Using patterns to hide bad design.

More classes do not automatically mean better architecture.

Mistake 4: Confusing patterns.

Strategy changes behavior.

Adapter changes interface.

Decorator adds behavior around an object.

Factory handles creation.

## Mid/Senior Notes

Patterns are useful when they reduce real complexity.

Backend examples:

- Strategy: payment method, discount policy, pricing rule.
- Adapter: external API client wrapper.
- Factory: choose provider/client based on config.
- Decorator: logging, metrics, retry wrapper.
- Repository: persistence abstraction.
- Proxy: Spring transactions/security/AOP.

Senior-level answer:

```text
I use design patterns when they solve a real problem. I avoid pattern-driven
development where the code becomes more complex just to match a pattern name.
The value of a pattern is clearer communication and easier change.
```

## Quick Summary

Design patterns are named solutions to common design problems.

Strategy handles interchangeable behavior.

Factory handles creation logic.

Adapter wraps external or incompatible APIs.

Builder helps construct complex objects.

Decorator adds behavior around another object.

Use patterns pragmatically, not automatically.
