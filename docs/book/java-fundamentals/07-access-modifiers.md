---
layout: default
title: Access Modifiers
parent: Java Fundamentals
grand_parent: Book
nav_order: 7
---

# Access Modifiers


## Simple Explanation

Access modifiers control who can see and use a class, field, method, or constructor.

They are Java's basic visibility rules.

The four main levels are:

```text
public          -> visible from everywhere
protected       -> visible in the same package and subclasses
package-private -> visible only in the same package
private         -> visible only inside the same class
```

Package-private means there is no modifier:

```java
class PackageOnlyClass {
}
```

Access modifiers exist because not everything in your code should be visible to everything else.

Good code hides details.

Bad code exposes too much and becomes hard to change.

## Why It Exists

Imagine every field and method in every class is public.

Then any part of the application can touch anything.

At first, that feels convenient.

Later, it becomes a trap.

If many classes depend on internal details, changing those details becomes risky.

Access modifiers help you say:

```text
This is part of the public contract.
This is only for this class.
This is only for this package.
This is only for subclasses.
```

This is one of the basic tools for encapsulation.

## Basic Code Example

```java
public class BankAccount {
    private int balance;

    public BankAccount(int initialBalance) {
        if (initialBalance < 0) {
            throw new IllegalArgumentException("Initial balance cannot be negative");
        }

        this.balance = initialBalance;
    }

    public void deposit(int amount) {
        if (amount <= 0) {
            throw new IllegalArgumentException("Amount must be positive");
        }

        balance += amount;
    }

    public int getBalance() {
        return balance;
    }
}
```

Here:

- `BankAccount` is public, so other code can use it,
- `balance` is private, so other code cannot change it directly,
- `deposit` is public, so other code can add money through controlled behavior,
- the constructor protects the object from invalid initial state.

This is much better than:

```java
public class BankAccount {
    public int balance;
}
```

Because this allows any code to do:

```java
account.balance = -999999;
```

That is not freedom. That is broken design.

## Common Interview Questions

### What are Java access modifiers?

Good answer:

```text
Access modifiers control visibility. `public` is visible everywhere, `private`
only inside the same class, `protected` in the same package and subclasses, and
package-private only inside the same package.
```

### What is package-private access?

Package-private access is used when no access modifier is written.

The member or class is visible only inside the same package.

Example:

```java
class InternalCalculator {
}
```

### Is `protected` visible only to subclasses?

No.

This is a common misunderstanding.

In Java, `protected` is visible to classes in the same package and also to subclasses.

## Common Mistakes

Mistake 1: Making fields public.

This breaks encapsulation because outside code can change object state directly.

Mistake 2: Making everything public "just in case."

That creates a larger API surface. The more public things you expose, the more things other code can depend on.

Mistake 3: Misunderstanding `protected`.

`protected` is not only subclass access. Same-package classes can also access it.

## Mid/Senior Notes

Access modifiers are not only syntax. They are design tools.

The public API of a class should be small and intentional.

If everything is public, the class has no clear boundary.

A useful rule:

```text
Start as private as possible. Open visibility only when there is a real reason.
```

This makes refactoring easier.

If a method is private, you can change it freely inside the class.

If a method is public, other code may depend on it. Changing it can break callers.

In libraries, this matters even more. A public method can become a long-term contract.

## Quick Summary

Access modifiers control visibility.

`private` is the most restrictive.

`public` is the most open.

Package-private means no modifier.

Good visibility choices protect encapsulation and make code easier to change.

