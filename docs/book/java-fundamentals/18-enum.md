---
layout: default
title: Enum
parent: Java Fundamentals
grand_parent: Book
nav_order: 18
---

# Enum

## Simple Explanation

An `enum` represents a fixed set of named values.

Example:

```java
public enum OrderStatus {
    NEW,
    PAID,
    SHIPPED,
    CANCELLED
}
```

This means an order status can be only one of these values.

Not a random string.

Not any number.

Only the defined constants.

## Why It Exists

Enums make code safer than using plain strings or integers.

Weak version:

```java
String status = "PAID";
```

This allows bugs:

```java
String status = "PAYD"; // typo
```

Better:

```java
OrderStatus status = OrderStatus.PAID;
```

Now the compiler helps you.

If you write a value that does not exist, the code does not compile.

## Basic Code Example

```java
public enum PaymentStatus {
    PENDING,
    COMPLETED,
    FAILED
}
```

Usage:

```java
public class Payment {
    private PaymentStatus status;

    public Payment(PaymentStatus status) {
        this.status = status;
    }

    public boolean isCompleted() {
        return status == PaymentStatus.COMPLETED;
    }
}
```

Enums can be compared with `==` because each enum constant is a single instance.

## Enums Can Have Fields And Methods

Enums in Java are more powerful than simple lists of names.

Example:

```java
public enum UserRole {
    ADMIN("Administrator"),
    CUSTOMER("Customer"),
    SUPPORT("Support Agent");

    private final String displayName;

    UserRole(String displayName) {
        this.displayName = displayName;
    }

    public String getDisplayName() {
        return displayName;
    }
}
```

Usage:

```java
System.out.println(UserRole.ADMIN.getDisplayName());
```

## Common Interview Questions

### What is an enum in Java?

Good answer:

```text
An enum is a type that represents a fixed set of constants. It is useful when a
value can only be one of a known set, like order status or user role.
```

### Why use enum instead of String constants?

Enums are type-safe.

The compiler prevents invalid values.

Strings allow typos and invalid states.

### Can enums have methods and constructors?

Yes.

Enums can have fields, constructors, and methods.

Enum constructors are private implicitly because enum constants are predefined.

### Can you extend an enum?

No.

Enums cannot extend another class because they already extend `java.lang.Enum`.

They can implement interfaces.

## Common Mistakes

Mistake 1: Using strings for fixed sets of values.

```java
String role = "ADMIN";
```

Better:

```java
UserRole role = UserRole.ADMIN;
```

Mistake 2: Storing enum ordinals in a database.

Each enum has an ordinal position:

```text
NEW -> 0
PAID -> 1
SHIPPED -> 2
```

If you reorder enum constants later, stored ordinal values can break meaning.

In databases, storing enum names is usually safer than storing ordinals.

Mistake 3: Creating huge enums with too much business logic.

Some behavior in enums is fine.

But if an enum becomes a giant strategy engine, maybe a different design is needed.

## Mid/Senior Notes

Enums are great for stable, finite sets.

Good examples:

- order status,
- payment status,
- user role,
- document type,
- notification channel.

But be careful when values are controlled outside your application.

For example, if a third-party API can add new statuses without warning, a strict enum may make deserialization fail unless you handle unknown values.

Also, in JPA/Hibernate, prefer explicit enum storage:

```java
@Enumerated(EnumType.STRING)
private OrderStatus status;
```

This stores names instead of ordinals.

It is usually safer for long-term database compatibility.

## Quick Summary

Enums represent fixed sets of values.

They are safer than strings for known states.

Enums can have fields, constructors, and methods.

Enums can implement interfaces.

Avoid storing enum ordinals in databases.

