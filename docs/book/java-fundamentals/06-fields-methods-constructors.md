---
layout: default
title: Fields, Methods, And Constructors
parent: Java Fundamentals
grand_parent: Book
nav_order: 6
---

# Fields, Methods, And Constructors


## Simple Explanation

Fields store data.

Methods define behavior.

Constructors create and initialize objects.

Example:

```java
public class User {
    private String email;          // field

    public User(String email) {    // constructor
        this.email = email;
    }

    public String getEmail() {     // method
        return email;
    }
}
```

Simple mental model:

```text
Field       -> what the object knows
Method      -> what the object can do
Constructor -> how the object starts its life
```

## Why It Exists

Objects need state and behavior.

For example, a `BankAccount` may know its balance and allow deposits:

```java
public class BankAccount {
    private int balance;

    public BankAccount(int initialBalance) {
        this.balance = initialBalance;
    }

    public void deposit(int amount) {
        balance += amount;
    }

    public int getBalance() {
        return balance;
    }
}
```

The field stores the balance.

The constructor sets the initial balance.

The methods control how the balance can be read or changed.

## Basic Code Example

```java
public class Product {
    private String name;
    private int priceInCents;

    public Product(String name, int priceInCents) {
        this.name = name;
        this.priceInCents = priceInCents;
    }

    public String getName() {
        return name;
    }

    public int getPriceInCents() {
        return priceInCents;
    }

    public boolean isExpensive() {
        return priceInCents > 10_000;
    }
}
```

This class has:

- two fields: `name`, `priceInCents`,
- one constructor,
- three methods.

## Common Interview Questions

### What is a field?

A field is a variable that belongs to a class or object.

Instance fields store object state.

Static fields belong to the class itself.

### What is a method?

A method is a block of behavior that can receive input, do work, and optionally return a result.

### What is a constructor?

A constructor is a special block used to create and initialize an object.

It has the same name as the class and no return type.

### Can a class have multiple constructors?

Yes.

That is called constructor overloading.

Example:

```java
public class User {
    private String email;
    private boolean active;

    public User(String email) {
        this(email, true);
    }

    public User(String email, boolean active) {
        this.email = email;
        this.active = active;
    }
}
```

## Common Mistakes

Mistake 1: Making fields public by default.

This exposes internal state and makes the object harder to control.

Better:

```java
private String email;
```

Mistake 2: Creating constructors that do too much.

A constructor should initialize the object. It usually should not call external APIs, send emails, or start complicated workflows.

Mistake 3: Writing methods that change state unexpectedly.

If a method looks like a simple getter but changes the object, that is confusing design.

## Mid/Senior Notes

Constructors protect object validity.

This means an object should not be created in a broken state.

Weak version:

```java
public class User {
    public String email;
}
```

This allows:

```java
User user = new User();
user.email = null;
```

Better version:

```java
public class User {
    private final String email;

    public User(String email) {
        if (email == null || email.isBlank()) {
            throw new IllegalArgumentException("Email must not be blank");
        }

        this.email = email;
    }

    public String getEmail() {
        return email;
    }
}
```

This class protects itself.

That is an important design idea:

```text
Do not create objects that are invalid from the beginning.
```

At mid and senior levels, fields, methods, and constructors are not only syntax. They are tools for controlling state, protecting invariants, and making code easier to reason about.

## Quick Summary

Fields store data.

Methods define behavior.

Constructors initialize objects.

Good constructors help prevent invalid objects.

Good methods make behavior clear and predictable.

