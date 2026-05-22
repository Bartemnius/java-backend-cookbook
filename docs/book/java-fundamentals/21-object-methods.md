---
layout: default
title: "Object Methods: equals, hashCode, toString"
parent: Java Fundamentals
grand_parent: Book
nav_order: 21
---

# Object Methods: equals, hashCode, toString

## Simple Explanation

Every Java class ultimately inherits from `Object`.

That means every object has methods like:

```text
equals()
hashCode()
toString()
```

These methods look simple, but they matter a lot.

They affect:

- comparing objects,
- using objects in `HashMap` and `HashSet`,
- logging,
- debugging,
- testing.

## Why It Exists

Java needs basic behavior that all objects share.

For example:

- Can two objects be considered equal?
- What hash bucket should this object go into?
- How should this object be shown as text?

The default behavior from `Object` is often not enough for business classes.

That is why we override these methods.

## equals()

`equals()` answers this question:

```text
Should these two objects be considered equal?
```

Default `equals()` behaves like reference comparison:

```java
User first = new User("anna@example.com");
User second = new User("anna@example.com");

System.out.println(first == second);      // false
System.out.println(first.equals(second)); // false unless equals is overridden
```

If we want users with the same email to be equal, we need to override `equals()`.

Example:

```java
import java.util.Objects;

public class User {
    private final String email;

    public User(String email) {
        this.email = email;
    }

    @Override
    public boolean equals(Object other) {
        if (this == other) {
            return true;
        }

        if (!(other instanceof User user)) {
            return false;
        }

        return Objects.equals(email, user.email);
    }

    @Override
    public int hashCode() {
        return Objects.hash(email);
    }
}
```

## hashCode()

`hashCode()` returns an integer used by hash-based collections like:

- `HashMap`,
- `HashSet`,
- `LinkedHashMap`,
- `LinkedHashSet`.

The important contract:

```text
If two objects are equal according to equals(), they must have the same hashCode().
```

If you override `equals()`, you almost always must override `hashCode()` too.

## toString()

`toString()` returns a text representation of an object.

Default version is usually not helpful:

```text
User@5a07e868
```

Better:

```java
@Override
public String toString() {
    return "User{email='" + email + "'}";
}
```

This helps with logs and debugging.

But be careful: do not put passwords, tokens, or sensitive data into `toString()`.

## Common Interview Questions

### What is the contract between `equals()` and `hashCode()`?

Good answer:

```text
If two objects are equal according to equals(), they must return the same hashCode().
If this contract is broken, hash-based collections like HashMap and HashSet may
behave incorrectly.
```

### Do equal objects need to have different hash codes from unequal objects?

No.

Unequal objects can have the same hash code. That is called a collision.

But equal objects must have the same hash code.

### Why should you override `toString()`?

To make logs, debugging, and test failures easier to understand.

### Why are records useful here?

Records automatically generate `equals()`, `hashCode()`, and `toString()` based on their components.

Example:

```java
public record UserDto(String email, String name) {
}
```

For simple data carriers, this saves a lot of boilerplate.

## Common Mistakes

Mistake 1: Overriding `equals()` but not `hashCode()`.

This can break `HashSet` and `HashMap`.

Mistake 2: Using mutable fields inside `hashCode()`.

Example:

```java
Set<User> users = new HashSet<>();
User user = new User("old@example.com");

users.add(user);
user.setEmail("new@example.com");
```

If `email` is used in `hashCode()`, the object may now be in the wrong hash bucket.

Mistake 3: Leaking secrets in `toString()`.

Bad:

```java
return "User{email='" + email + "', password='" + password + "'}";
```

Never log passwords or tokens.

## Mid/Senior Notes

`equals()` and `hashCode()` are not just syntax.

They are identity design.

You need to decide what makes an object equal:

- database id?
- business key?
- all fields?
- immutable value?

For value objects, equality is usually based on value:

```java
public record Money(int cents, String currency) {
}
```

Two `Money` records with the same cents and currency are equal.

For entities, equality can be harder.

Example:

```text
User with database id 10
```

Should equality be based on id? Email? Object reference before persistence?

In JPA/Hibernate, `equals()` and `hashCode()` can be tricky because entities may be proxied and ids may be assigned later.

Senior-level answer:

```text
For simple immutable value objects, equals and hashCode are straightforward.
For mutable entities, especially JPA entities, I am careful because changing identity
fields or relying on generated ids too early can break collections and persistence behavior.
```

That is much better than blindly saying "generate equals and hashCode from all fields."

## Quick Summary

`equals()` defines logical equality.

`hashCode()` is used by hash-based collections.

Equal objects must have the same hash code.

`toString()` helps with logs and debugging.

Be careful with mutable fields, entity identity, and sensitive data.

