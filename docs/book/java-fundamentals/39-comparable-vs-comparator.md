---
layout: default
title: Comparable Vs Comparator
parent: Java Fundamentals
grand_parent: Book
nav_order: 39
---

# Comparable Vs Comparator

## Simple Explanation

`Comparable` and `Comparator` are used for sorting.

Simple rule:

```text
Comparable -> natural order inside the class
Comparator -> external custom order
```

Example:

```java
public class User implements Comparable<User> {
    private final String email;

    @Override
    public int compareTo(User other) {
        return this.email.compareTo(other.email);
    }
}
```

This means users have a natural order by email.

With `Comparator`, you define sorting outside the class:

```java
Comparator<User> byEmail = Comparator.comparing(User::getEmail);
```

## Why It Exists

Objects often need to be sorted.

Examples:

- users by email,
- orders by date,
- products by price,
- employees by salary,
- tasks by priority.

Sometimes a class has one obvious natural order.

Sometimes you need many different orders.

That is why Java has both `Comparable` and `Comparator`.

## Comparable

Use `Comparable` when the class has one natural order.

Example:

```java
public class Product implements Comparable<Product> {
    private final String name;
    private final int priceInCents;

    public Product(String name, int priceInCents) {
        this.name = name;
        this.priceInCents = priceInCents;
    }

    @Override
    public int compareTo(Product other) {
        return Integer.compare(this.priceInCents, other.priceInCents);
    }
}
```

Now products are naturally sorted by price.

Usage:

```java
Collections.sort(products);
```

## Comparator

Use `Comparator` when you want custom sorting outside the class.

Example:

```java
Comparator<Product> byName =
        Comparator.comparing(Product::getName);

Comparator<Product> byPriceDescending =
        Comparator.comparing(Product::getPriceInCents).reversed();
```

Usage:

```java
products.sort(byName);
products.sort(byPriceDescending);
```

This is flexible because you can create many sorting strategies.

## Basic Code Example

```java
List<User> users = new ArrayList<>();

users.sort(Comparator.comparing(User::getEmail));
users.sort(Comparator.comparing(User::getCreatedAt).reversed());
```

You can also chain comparators:

```java
users.sort(
        Comparator.comparing(User::getLastName)
                .thenComparing(User::getFirstName)
);
```

This sorts by last name first, then first name.

## Common Interview Questions

### What is the difference between `Comparable` and `Comparator`?

Good answer:

```text
Comparable defines the natural order inside the class by implementing compareTo.
Comparator defines an external custom order and can be created separately, which
allows multiple sorting strategies for the same class.
```

### Which method does `Comparable` use?

`compareTo()`.

```java
int compareTo(T other);
```

### Which method does `Comparator` use?

`compare()`.

```java
int compare(T first, T second);
```

In modern Java, you often create comparators with helper methods:

```java
Comparator.comparing(User::getEmail)
```

### What should comparison methods return?

They return:

```text
negative number -> first is smaller
zero            -> equal order
positive number -> first is greater
```

You usually do not care about the exact number.

Only the sign matters.

## Common Mistakes

Mistake 1: Subtracting numbers to compare.

Bad:

```java
return this.priceInCents - other.priceInCents;
```

This can overflow for large numbers.

Better:

```java
return Integer.compare(this.priceInCents, other.priceInCents);
```

Mistake 2: Putting every possible order into `Comparable`.

If there are many valid ways to sort a type, use `Comparator`.

Mistake 3: Making comparison inconsistent with equality.

If `compareTo()` returns `0`, sorted collections may treat elements as equal for ordering purposes.

This matters in `TreeSet` and `TreeMap`.

## Mid/Senior Notes

`Comparable` is a strong design statement.

It says:

```text
This class has a natural order.
```

That should be true.

Good candidates:

- `LocalDate`,
- `Integer`,
- `BigDecimal`,
- value objects with one obvious ordering.

For business entities, natural order is often not obvious.

Is a user naturally sorted by email, id, creation date, or last name?

Usually, custom `Comparator`s are better.

Senior-level answer:

```text
I use Comparable only when a type has one obvious natural order. For business
objects with multiple possible sort orders, I prefer Comparator because it keeps
sorting rules external and explicit.
```

## Quick Summary

`Comparable` defines natural order inside the class.

`Comparator` defines external custom order.

Use `Integer.compare`, `Long.compare`, or comparator helpers instead of subtraction.

Use `Comparator` when multiple sorting strategies are needed.

