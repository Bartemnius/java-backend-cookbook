---
layout: default
title: Packages And Imports
parent: Java Fundamentals
grand_parent: Book
nav_order: 4
---

# Packages And Imports


## Simple Explanation

A package is a namespace for Java classes.

It helps organize code and avoid name conflicts.

An import lets you use a class from another package without writing its full name every time.

Example:

```java
package com.example.orders;

import java.time.LocalDate;

public class Order {
    private LocalDate createdAt;
}
```

Here:

- `com.example.orders` is the package,
- `java.time.LocalDate` is imported,
- `Order` belongs to the `com.example.orders` package.

## Why It Exists

Large applications can contain thousands of classes.

Without packages, names would collide quickly.

For example, many systems can have a class called `User`:

```text
com.example.auth.User
com.example.billing.User
com.example.crm.User
```

The package tells us which `User` we mean.

Packages also help organize code by feature, layer, or domain.

## Basic Code Example

File path:

```text
src/main/java/com/example/shop/Product.java
```

Code:

```java
package com.example.shop;

public class Product {
    private String name;

    public Product(String name) {
        this.name = name;
    }

    public String getName() {
        return name;
    }
}
```

Another class can import it:

```java
package com.example.orders;

import com.example.shop.Product;

public class OrderLine {
    private Product product;

    public OrderLine(Product product) {
        this.product = product;
    }
}
```

Without the import, you would need to write:

```java
private com.example.shop.Product product;
```

That is valid, but ugly if used often.

## Common Interview Questions

### What is a package in Java?

Good answer:

```text
A package is a namespace used to organize Java classes and avoid name conflicts.
It also affects access control because package-private members are visible only
inside the same package.
```

### What is an import?

An import tells the compiler which class you want to use from another package.

It does not copy code into your file. It only lets you refer to that class by its simple name.

### Are packages only folders?

Not exactly.

In normal source projects, package names match folder structure, but the package is a Java language concept. The folder structure is how source files are usually organized to match that concept.

For example:

```java
package com.example.orders;
```

Usually lives in:

```text
com/example/orders/
```

## Common Mistakes

Mistake 1: Thinking `import` makes code slower.

Imports do not execute code. They are compile-time convenience.

Mistake 2: Using wildcard imports everywhere.

```java
import java.util.*;
```

This is not always terrible, but explicit imports are usually clearer in production code:

```java
import java.util.List;
import java.util.Map;
```

Mistake 3: Creating meaningless packages.

Package names should help people understand the codebase.

Names like `misc`, `utils`, and `common` often become dumping grounds. Sometimes a utility package is fine, but if everything ends up there, the structure is telling you the design is weak.

## Mid/Senior Notes

Package structure is not only cosmetic.

It can support or damage architecture.

For example, if every class is public and every package imports every other package freely, boundaries are weak.

Good package design can make the intended dependencies visible:

```text
orders -> payments
orders -> inventory
orders -> shared-kernel
```

Bad package design hides coupling until changing one feature breaks three others.

In larger systems, packages should usually follow business boundaries, not only technical categories.

Also remember package-private access. If a class, method, or constructor has no access modifier, it is visible only within the same package.

That can be useful for hiding implementation details without making everything `private`.

## Quick Summary

Packages organize Java classes and avoid name conflicts.

Imports let you use classes from other packages with shorter names.

Package names usually match folder paths.

Good package structure makes a codebase easier to understand and change.

