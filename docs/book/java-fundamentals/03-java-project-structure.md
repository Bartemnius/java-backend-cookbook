---
layout: default
title: Java Project Structure
parent: Java Fundamentals
grand_parent: Book
nav_order: 3
---

# Java Project Structure


## Simple Explanation

A Java project is not only a pile of `.java` files.

Real Java projects have structure.

That structure helps developers, build tools, IDEs, and test frameworks understand where things are.

In many modern Java projects, especially Maven and Gradle projects, you will often see a layout like this:

```text
my-app/
  pom.xml
  src/
    main/
      java/
        com/
          example/
            app/
              Application.java
      resources/
        application.properties
    test/
      java/
        com/
          example/
            app/
              ApplicationTest.java
      resources/
```

The basic idea:

- production Java code goes into `src/main/java`,
- production resources go into `src/main/resources`,
- test Java code goes into `src/test/java`,
- test resources go into `src/test/resources`,
- build configuration lives in files like `pom.xml` or `build.gradle`.

This layout is not random. It is a convention.

And conventions are useful because everyone knows where to look.

## Why It Exists

Without project structure, every project would organize files differently.

That sounds flexible, but in teams it becomes annoying fast.

Imagine joining a project and needing one hour just to find:

- source code,
- tests,
- configuration,
- resources,
- dependencies,
- build commands.

Standard layout solves that.

It makes Java projects easier to build, test, package, and understand.

## Basic Example

A small Maven project may look like this:

```text
calculator/
  pom.xml
  src/
    main/
      java/
        com/
          example/
            calculator/
              Calculator.java
    test/
      java/
        com/
          example/
            calculator/
              CalculatorTest.java
```

Production class:

```java
package com.example.calculator;

public class Calculator {
    public int add(int a, int b) {
        return a + b;
    }
}
```

Test class:

```java
package com.example.calculator;

import org.junit.jupiter.api.Test;

import static org.junit.jupiter.api.Assertions.assertEquals;

class CalculatorTest {
    @Test
    void addsTwoNumbers() {
        Calculator calculator = new Calculator();

        assertEquals(5, calculator.add(2, 3));
    }
}
```

The test mirrors the package of the production class.

That makes the relationship easy to see.

## Common Interview Questions

### Where does production Java code usually live in a Maven or Gradle project?

Good answer:

```text
Usually in `src/main/java`. Resources like configuration files usually go into
`src/main/resources`. Tests usually go into `src/test/java`.
```

### What is the difference between `src/main` and `src/test`?

`src/main` contains application code and resources used by the real application.

`src/test` contains test code and test resources used when running tests.

Test code should not be packaged as part of the production application in the same way as main code.

### What is `pom.xml`?

`pom.xml` is Maven's project configuration file.

It defines project metadata, dependencies, plugins, build settings, and sometimes profiles.

For Gradle, similar configuration usually lives in `build.gradle` or `build.gradle.kts`.

## Common Mistakes

Mistake 1: Putting everything into one folder.

That may work for a tiny exercise, but it does not scale into real projects.

Mistake 2: Mixing tests with production code.

Tests should have their own source tree. That keeps production packaging cleaner and avoids accidental dependencies on test-only tools.

Mistake 3: Treating project structure as unimportant.

Structure is communication. A messy structure makes the project harder to understand before anyone even reads the code.

## Mid/Senior Notes

At mid and senior levels, project structure becomes an architecture topic.

A simple package layout is fine for a small application. But as the codebase grows, structure should reflect boundaries.

For example, this is often weak:

```text
controller/
service/
repository/
entity/
dto/
```

This is common, but it groups code by technical layer only.

For small apps, that is acceptable.

For larger apps, grouping by business capability can be easier to maintain:

```text
orders/
  OrderController.java
  OrderService.java
  OrderRepository.java
  Order.java

payments/
  PaymentController.java
  PaymentService.java
  PaymentRepository.java
  Payment.java
```

The second structure makes business boundaries more visible.

There is no universal winner. The important senior-level point is this:

```text
Project structure should make change easier.
```

If a new feature touches files scattered across ten unrelated folders, the structure may be working against you.

## Quick Summary

Java projects usually follow a standard source layout.

Production code commonly lives in `src/main/java`.

Tests commonly live in `src/test/java`.

Build tools like Maven and Gradle rely on conventions, which make projects easier to build and understand.

At higher levels, project structure should reflect maintainable boundaries, not only technical layers.

