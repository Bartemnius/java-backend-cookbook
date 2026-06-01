---
layout: default
title: Annotations
parent: Intermediate And Advanced Java
grand_parent: Book
nav_order: 8
---

# Annotations

## Simple Explanation

Annotations add metadata to code.

Example:

```java
@Override
public String toString() {
    return "User";
}
```

`@Override` tells the compiler:

```text
This method should override a parent method.
```

Annotations do not usually contain business logic themselves.

They describe something about the code.

## Why It Exists

Annotations let tools and frameworks understand code without extra configuration files everywhere.

Examples:

```java
@Test
@Entity
@Service
@Transactional
@GetMapping
@Autowired
```

These annotations tell frameworks what to do:

- JUnit runs test methods,
- Hibernate maps entities,
- Spring creates beans,
- Spring MVC maps HTTP routes,
- Spring transactions wrap method calls.

## Basic Code Example

```java
public class UserService {
    @Deprecated
    public void oldMethod() {
    }

    public void newMethod() {
    }
}
```

`@Deprecated` tells developers and tools that `oldMethod()` should not be used anymore.

Another example:

```java
public class Dog extends Animal {
    @Override
    public void makeSound() {
        System.out.println("Bark");
    }
}
```

`@Override` helps the compiler catch mistakes.

## Runtime Annotations

Some annotations are available at runtime.

Frameworks can read them using reflection.

Example:

```java
@Service
public class OrderService {
}
```

Spring can scan classes, find `@Service`, and create a bean.

That is possible because the annotation is available to the framework.

## Annotation Retention

Annotations can have different retention policies:

```text
SOURCE  -> available only in source code
CLASS   -> stored in class file, not necessarily available at runtime
RUNTIME -> available at runtime through reflection
```

Example:

```java
@Retention(RetentionPolicy.RUNTIME)
public @interface Audited {
}
```

`RUNTIME` means reflection can read it while the application runs.

## Creating A Custom Annotation

```java
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;

@Retention(RetentionPolicy.RUNTIME)
public @interface Audited {
    String value() default "";
}
```

Usage:

```java
@Audited("user-created")
public void createUser() {
}
```

By itself, this annotation does nothing.

Some code must read it and act on it.

## Common Interview Questions

### What is an annotation in Java?

Good answer:

```text
An annotation is metadata added to code. It can be used by the compiler, tools,
or frameworks to validate code, generate code, configure behavior, or inspect
classes at runtime.
```

### Do annotations execute code by themselves?

No.

Annotations are metadata.

Something else must read them and do something:

- compiler,
- annotation processor,
- framework,
- runtime reflection code.

### What is annotation retention?

Retention defines how long an annotation is kept:

```text
SOURCE, CLASS, or RUNTIME
```

Runtime annotations can be read with reflection.

### How does Spring use annotations?

Spring scans classes and reads annotations like `@Component`, `@Service`, `@Controller`, `@Repository`, and `@Transactional`.

Then it uses that metadata to create beans, configure behavior, map routes, or apply proxies.

## Common Mistakes

Mistake 1: Thinking annotations are magic.

They are metadata.

Framework code reads them and acts on them.

Mistake 2: Creating custom annotations with no processor or runtime logic.

This does nothing:

```java
@Audited
public void process() {
}
```

Unless something reads `@Audited`, nothing happens.

Mistake 3: Ignoring retention.

If you want to read an annotation at runtime, it needs `RetentionPolicy.RUNTIME`.

Mistake 4: Overusing annotations for business logic.

Annotations are useful, but too much annotation-driven behavior can make code harder to trace.

## Mid/Senior Notes

Annotations are central in Spring.

But a senior developer should know:

```text
Annotation != behavior by itself.
```

Example:

```java
@Transactional
public void payOrder() {
}
```

The annotation does not start a transaction by magic.

Spring detects it and usually applies transactional behavior through a proxy.

That distinction matters a lot.

It explains common bugs like:

```text
self-invocation does not trigger @Transactional
```

because the call may not go through the Spring proxy.

Senior-level answer:

```text
Annotations are metadata. Frameworks like Spring read them using reflection,
annotation processing, or proxies, and then apply behavior. I try to understand
what reads an annotation and when, instead of treating it as magic.
```

## Quick Summary

Annotations add metadata to code.

They do not execute behavior by themselves.

Tools and frameworks read annotations.

Retention controls whether annotations are available in source, class files, or runtime.

Spring uses annotations heavily.

Understanding annotations helps demystify framework behavior.
