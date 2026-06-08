---
layout: default
title: Dependency Injection Without Spring
parent: Intermediate And Advanced Java
grand_parent: Book
nav_order: 17
---

# Dependency Injection Without Spring

## Simple Explanation

Dependency injection means giving an object the things it needs from the outside.

Instead of a class creating its own dependencies:

```java
public class OrderService {
    private final PaymentClient paymentClient = new StripePaymentClient();
}
```

you pass the dependency in:

```java
public class OrderService {
    private final PaymentClient paymentClient;

    public OrderService(PaymentClient paymentClient) {
        this.paymentClient = paymentClient;
    }
}
```

Simple idea:

```text
Do not build your dependencies inside the class.
Receive them from outside.
```

Spring uses dependency injection heavily.

But the concept does not belong to Spring.

You can understand it without any framework.

## Why It Exists

If a class creates its dependencies directly, it becomes tightly coupled.

Bad:

```java
public class OrderService {
    private final StripePaymentClient paymentClient = new StripePaymentClient();

    public void pay(Order order) {
        paymentClient.charge(order.total());
    }
}
```

Problems:

- hard to test without Stripe,
- hard to replace Stripe with another provider,
- business logic depends on infrastructure details,
- class controls too much.

Better:

```java
public class OrderService {
    private final PaymentClient paymentClient;

    public OrderService(PaymentClient paymentClient) {
        this.paymentClient = paymentClient;
    }

    public void pay(Order order) {
        paymentClient.charge(order.total());
    }
}
```

Now `OrderService` depends on an abstraction.

The concrete implementation is provided from outside.

## Basic Code Example

Abstraction:

```java
public interface PaymentClient {
    PaymentResult charge(Money amount);
}
```

Production implementation:

```java
public class StripePaymentClient implements PaymentClient {
    @Override
    public PaymentResult charge(Money amount) {
        return new PaymentResult("stripe-payment-id");
    }
}
```

Service:

```java
public class OrderService {
    private final PaymentClient paymentClient;

    public OrderService(PaymentClient paymentClient) {
        this.paymentClient = paymentClient;
    }

    public PaymentResult pay(Order order) {
        return paymentClient.charge(order.total());
    }
}
```

Manual wiring:

```java
PaymentClient paymentClient = new StripePaymentClient();
OrderService orderService = new OrderService(paymentClient);
```

This is dependency injection without Spring.

Spring mostly automates this wiring.

## Testing Benefit

Dependency injection makes testing easier.

Fake implementation:

```java
public class FakePaymentClient implements PaymentClient {
    @Override
    public PaymentResult charge(Money amount) {
        return new PaymentResult("fake-payment-id");
    }
}
```

Test setup:

```java
PaymentClient fakePaymentClient = new FakePaymentClient();
OrderService orderService = new OrderService(fakePaymentClient);
```

Now the test does not call real Stripe.

That is the point.

## Constructor Injection

Constructor injection is usually the best default.

```java
public OrderService(PaymentClient paymentClient) {
    this.paymentClient = paymentClient;
}
```

Why?

- required dependencies are obvious,
- object cannot be created without dependencies,
- fields can be `final`,
- easier to test,
- fewer hidden states.

## Setter Injection

Setter injection passes dependency after object creation.

```java
public void setPaymentClient(PaymentClient paymentClient) {
    this.paymentClient = paymentClient;
}
```

This can be useful for optional dependencies.

But for required dependencies, constructor injection is usually better.

Otherwise, the object can exist in an incomplete state.

## Field Injection

Spring can inject directly into fields:

```java
@Autowired
private PaymentClient paymentClient;
```

This is common in older code, but it is weaker design.

Problems:

- dependencies are less visible,
- harder to test without Spring,
- fields cannot easily be final,
- object can be constructed without required dependencies.

Prefer constructor injection.

## Common Interview Questions

### What is dependency injection?

Good answer:

```text
Dependency injection means providing an object's dependencies from the outside
instead of letting the object create them itself. It reduces coupling and makes
code easier to test and change.
```

### Is dependency injection a Spring feature?

No.

Spring supports dependency injection, but the concept is independent from Spring.

You can do DI manually with constructors.

### Why is constructor injection preferred?

Good answer:

```text
Constructor injection makes required dependencies explicit, allows final fields,
prevents incomplete objects, and makes testing easier.
```

### How does DI relate to Dependency Inversion?

Dependency Inversion says high-level code should depend on abstractions, not concrete details.

Dependency injection is one way to provide those abstractions from outside.

Example:

```text
OrderService depends on PaymentClient interface.
StripePaymentClient is injected from outside.
```

## Common Mistakes

Mistake 1: Thinking DI means Spring annotations.

DI is a design technique.

Spring is just one tool that automates it.

Mistake 2: Creating dependencies with `new` inside business services.

```java
private final EmailClient emailClient = new SendGridEmailClient();
```

This couples business logic to infrastructure.

Mistake 3: Overusing interfaces.

If there is no boundary, no testing need, and no alternate implementation, an interface may add noise.

Mistake 4: Using field injection by default.

Constructor injection is usually cleaner.

## Mid/Senior Notes

Dependency injection is foundational for backend architecture.

It supports:

- testing,
- clean architecture,
- hexagonal architecture,
- replacing infrastructure,
- mocking/faking external systems,
- separating business logic from technical details.

Example boundary:

```java
public interface UserRepository {
    Optional<User> findById(UserId id);
    User save(User user);
}
```

Business service:

```java
public class UserService {
    private final UserRepository userRepository;

    public UserService(UserRepository userRepository) {
        this.userRepository = userRepository;
    }
}
```

The service does not know if users are stored in PostgreSQL, MongoDB, memory, or a test fake.

Senior-level answer:

```text
Dependency injection is not about frameworks. It is about controlling dependencies.
I use constructor injection for required dependencies, depend on abstractions at
important boundaries, and avoid hiding infrastructure creation inside business logic.
```

## Quick Summary

Dependency injection means passing dependencies from outside.

It reduces coupling.

It improves testability.

Constructor injection is usually the best default.

Spring automates DI, but DI exists without Spring.

Use abstractions at meaningful boundaries, not everywhere blindly.
