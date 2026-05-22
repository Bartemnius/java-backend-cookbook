---
layout: default
title: var Keyword
parent: Java Fundamentals
grand_parent: Book
nav_order: 16
---

# var Keyword

## Simple Explanation

`var` lets Java infer the type of a local variable from the value assigned to it.

Example:

```java
var name = "Anna";
var age = 30;
var active = true;
```

Java understands this as:

```java
String name = "Anna";
int age = 30;
boolean active = true;
```

Important:

```text
var does not mean dynamic typing.
```

Java is still statically typed.

The type is decided at compile time.

## Why It Exists

Sometimes explicit types make code noisy.

Example:

```java
Map<String, List<Order>> ordersByCustomer = new HashMap<>();
```

With `var`:

```java
var ordersByCustomer = new HashMap<String, List<Order>>();
```

This can reduce repetition.

But `var` should make code clearer, not more mysterious.

## Basic Code Example

```java
import java.util.ArrayList;

public class VarExample {
    public static void main(String[] args) {
        var names = new ArrayList<String>();

        names.add("Anna");
        names.add("Tom");

        for (var name : names) {
            System.out.println(name.toUpperCase());
        }
    }
}
```

Here Java knows:

```text
names -> ArrayList<String>
name  -> String
```

## Common Interview Questions

### Is `var` dynamic typing?

No.

Good answer:

```text
No. `var` is local variable type inference. Java still determines the type at
compile time, so it remains statically typed.
```

### Where can `var` be used?

`var` can be used for local variables.

Examples:

```java
var count = 10;

for (var item : items) {
    System.out.println(item);
}
```

It cannot be used for fields:

```java
public class User {
    private var email = "test@example.com"; // does not compile
}
```

It also cannot be used without an initializer:

```java
var name; // does not compile
```

Java needs the initializer to infer the type.

### Does `var` improve readability?

Sometimes.

It depends.

This is readable:

```java
var user = userRepository.findById(id);
```

This is weaker:

```java
var result = service.process(data);
```

If `result` tells us nothing and the method name is vague, explicit typing may be clearer.

## Common Mistakes

Mistake 1: Thinking `var` means the variable can change type.

It cannot:

```java
var value = "hello";
value = 123; // does not compile
```

`value` is a `String`.

Mistake 2: Overusing `var` everywhere.

This can make code harder to read when the type is not obvious.

Mistake 3: Using vague variable names with `var`.

Bad:

```java
var x = getData();
```

Better:

```java
var activeUsers = userRepository.findActiveUsers();
```

If the type is inferred, the variable name should work harder.

## Mid/Senior Notes

`var` is a readability tool, not a status symbol.

Use it when the type is obvious or not important:

```java
var users = new ArrayList<User>();
var response = httpClient.send(request);
```

Avoid it when the type is important for understanding the code:

```java
var timeout = config.getTimeout();
```

Is that an `int`, `Duration`, `long`, or something else?

Maybe this is better:

```java
Duration timeout = config.getTimeout();
```

Good rule:

```text
Use `var` when it removes noise. Avoid it when it removes useful information.
```

## Quick Summary

`var` is local variable type inference.

It does not make Java dynamically typed.

It requires an initializer.

It cannot be used for fields.

Use it when it improves readability.

