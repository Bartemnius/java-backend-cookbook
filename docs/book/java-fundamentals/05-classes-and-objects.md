---
layout: default
title: Classes And Objects
parent: Java Fundamentals
grand_parent: Book
nav_order: 5
---

# Classes And Objects


## Simple Explanation

A class is a blueprint.

An object is a real thing created from that blueprint.

Example:

```text
Class:  Car
Object: myCar, yourCar, companyCar
```

The class describes what a car has and what a car can do.

An object is one specific car in memory.

In Java, most code is organized around classes and objects.

## Why It Exists

Programs need to represent real or abstract things:

- users,
- orders,
- payments,
- invoices,
- products,
- connections,
- services,
- requests,
- responses.

Classes let us describe those things using data and behavior together.

Data is stored in fields.

Behavior is written as methods.

## Basic Code Example

```java
public class User {
    private String email;

    public User(String email) {
        this.email = email;
    }

    public String getEmail() {
        return email;
    }
}
```

Create objects:

```java
User firstUser = new User("anna@example.com");
User secondUser = new User("tom@example.com");

System.out.println(firstUser.getEmail());
System.out.println(secondUser.getEmail());
```

`User` is the class.

`firstUser` and `secondUser` are object references pointing to two different `User` objects.

## Important Detail: Object vs Reference

This is where many beginners get confused.

In Java, a variable that holds an object does not contain the object directly.

It contains a reference to the object.

Simple mental model:

```text
User user = new User("anna@example.com");

user  ---- points to ---->  User object in memory
```

This matters because two variables can point to the same object:

```java
User first = new User("anna@example.com");
User second = first;
```

Now `first` and `second` refer to the same object.

They are two references, but one object.

## Common Interview Questions

### What is the difference between a class and an object?

Good answer:

```text
A class is a blueprint that defines fields and methods. An object is an instance
created from that class. For example, `User` is a class, and `new User(...)`
creates a specific User object.
```

### What does `new` do?

`new` creates a new object in memory and calls a constructor to initialize it.

Then it returns a reference to that object.

### Does a Java variable store the object itself?

For object types, the variable stores a reference to the object, not the object itself.

That is why two variables can refer to the same object.

## Common Mistakes

Mistake 1: Thinking a class and an object are the same thing.

They are connected, but not the same.

The class is the definition. The object is the instance.

Mistake 2: Forgetting that object variables store references.

This causes confusion when changing an object through one reference also appears visible through another reference.

Mistake 3: Creating classes that only hold random data with no clear responsibility.

Not every class needs complicated behavior, but a class should still represent a clear concept.

## Mid/Senior Notes

At mid and senior levels, classes are not only syntax. They are design units.

A weak class often has one of these problems:

- it does too many things,
- it has unclear responsibility,
- it exposes too much internal state,
- it depends on too many unrelated classes,
- it is hard to test without starting half the application.

A better class has a clear reason to exist.

For example, this is vague:

```java
public class UserHelper {
}
```

This is more meaningful:

```java
public class PasswordResetService {
}
```

The second name tells us what business capability the class supports.

A senior developer should think less about "how many classes do I need?" and more about:

```text
Where should this responsibility live?
What should this class know?
What should this class not know?
How painful will this be to change later?
```

## Quick Summary

A class is a blueprint.

An object is an instance of a class.

Object variables store references to objects.

Classes should not only compile. They should represent clear responsibilities.

