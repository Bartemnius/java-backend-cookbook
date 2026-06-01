---
layout: default
title: Reflection
parent: Intermediate And Advanced Java
grand_parent: Book
nav_order: 7
---

# Reflection

## Simple Explanation

Reflection lets Java code inspect and use classes, methods, fields, and constructors at runtime.

Example:

```java
Class<?> clazz = User.class;

System.out.println(clazz.getName());
```

Reflection means code can ask questions like:

```text
What fields does this class have?
What methods does it have?
What annotations are present?
Can I create an object dynamically?
Can I call this method dynamically?
```

## Why It Exists

Reflection is useful for frameworks and tools.

Frameworks often need to work with classes they do not know at compile time.

Examples:

- Spring finds classes annotated with `@Component`,
- Jackson reads fields/getters to serialize objects to JSON,
- Hibernate maps entities to database tables,
- testing frameworks find methods annotated with `@Test`.

Reflection is one reason Java frameworks can feel "magical."

But the magic is still code.

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

Reflection:

```java
Class<User> clazz = User.class;

System.out.println(clazz.getSimpleName());

for (Method method : clazz.getDeclaredMethods()) {
    System.out.println(method.getName());
}
```

This prints method names declared in `User`.

## Creating Objects With Reflection

```java
Constructor<User> constructor =
        User.class.getConstructor(String.class);

User user = constructor.newInstance("anna@example.com");
```

This creates a `User` object dynamically.

Most application code should not do this manually.

Frameworks do it often.

## Accessing Private Members

Reflection can access private members if accessibility is changed:

```java
Field field = User.class.getDeclaredField("email");
field.setAccessible(true);
```

This is powerful, but dangerous.

It breaks normal encapsulation.

Modern Java modules can also restrict reflective access.

## Common Interview Questions

### What is reflection?

Good answer:

```text
Reflection is a Java feature that allows code to inspect and interact with classes,
methods, fields, constructors, and annotations at runtime.
```

### Where is reflection used?

Common examples:

- Spring,
- Hibernate,
- Jackson,
- JUnit,
- dependency injection,
- serialization,
- object mapping,
- annotation processing at runtime.

### Is reflection slow?

Reflection is generally slower than direct method calls and can prevent some optimizations.

For most framework use cases, the cost is acceptable or optimized internally.

But you should not use reflection casually in hot business code.

### Why can reflection be dangerous?

Because it can break encapsulation, access private members, make code harder to understand, and cause runtime errors that the compiler cannot catch.

## Common Mistakes

Mistake 1: Using reflection in normal business logic.

Usually, there is a cleaner design.

Mistake 2: Thinking reflection is magic.

Frameworks use reflection to inspect your classes and annotations.

It is powerful, but understandable.

Mistake 3: Ignoring runtime failures.

With reflection, many errors happen at runtime:

- method not found,
- constructor not found,
- illegal access,
- wrong parameter type.

The compiler cannot protect you as much.

## Mid/Senior Notes

Reflection is mostly framework infrastructure.

You should understand it because it explains how tools work:

```text
Spring scans classes -> finds annotations -> creates beans
Jackson inspects fields/getters -> serializes JSON
Hibernate inspects entities -> maps them to tables
JUnit finds @Test methods -> runs tests
```

Reflection also matters for:

- native images,
- modular Java,
- performance,
- security,
- framework configuration.

Senior-level answer:

```text
I avoid reflection in normal business code, but I understand it as a mechanism
used by frameworks. It gives runtime flexibility, but it reduces compile-time
safety and can affect performance, security, and maintainability.
```

## Quick Summary

Reflection inspects and uses classes at runtime.

Frameworks use reflection heavily.

Reflection can access methods, fields, constructors, and annotations.

It is powerful but reduces compile-time safety.

Avoid reflection in normal business code unless there is a strong reason.
