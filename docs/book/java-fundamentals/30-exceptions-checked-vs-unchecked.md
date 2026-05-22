---
layout: default
title: "Exceptions: Checked Vs Unchecked"
parent: Java Fundamentals
grand_parent: Book
nav_order: 30
---

# Exceptions: Checked Vs Unchecked

## Simple Explanation

An exception is a signal that something went wrong while the program was running.

Example:

```java
int result = 10 / 0; // ArithmeticException
```

Java has two main categories developers talk about:

```text
checked exceptions   -> compiler forces you to handle or declare them
unchecked exceptions -> compiler does not force handling
```

Checked exceptions extend `Exception`, but not `RuntimeException`.

Unchecked exceptions extend `RuntimeException`.

## Why It Exists

Programs fail.

Files may be missing.

Network calls may timeout.

User input may be invalid.

Databases may be unavailable.

Exceptions give Java a structured way to report and handle failure.

Instead of returning weird values like `-1` or `null` everywhere, a method can throw an exception to say:

```text
I cannot complete this operation.
```

## Basic Code Example

Checked exception:

```java
import java.io.IOException;
import java.nio.file.Files;
import java.nio.file.Path;

public class FileReader {
    public String readFile(Path path) throws IOException {
        return Files.readString(path);
    }
}
```

`IOException` is checked, so the caller must handle it or declare it.

Unchecked exception:

```java
public class AgeValidator {
    public void validate(int age) {
        if (age < 0) {
            throw new IllegalArgumentException("Age cannot be negative");
        }
    }
}
```

`IllegalArgumentException` is unchecked.

The compiler does not force callers to catch it.

## Checked Exceptions

Checked exceptions are for failures that callers are expected to handle or at least consciously propagate.

Example:

```java
public void importFile(Path path) throws IOException {
    String content = Files.readString(path);
    // process file
}
```

The file may not exist.

The disk may fail.

Permissions may be wrong.

These are external problems.

## Unchecked Exceptions

Unchecked exceptions are often used for programming errors, invalid arguments, illegal state, or failures that normal callers cannot reasonably recover from.

Examples:

```java
throw new IllegalArgumentException("Email must not be blank");
throw new IllegalStateException("Order is already paid");
```

Unchecked does not mean unimportant.

It means the compiler does not force handling.

## Common Interview Questions

### What is the difference between checked and unchecked exceptions?

Good answer:

```text
Checked exceptions must be handled or declared with throws. Unchecked exceptions
extend RuntimeException and do not need to be declared or caught. Checked exceptions
are often used for recoverable external failures, while unchecked exceptions are
often used for programming errors or invalid state.
```

### Is `RuntimeException` checked or unchecked?

Unchecked.

Any exception that extends `RuntimeException` is unchecked.

### Should you catch every exception?

No.

Only catch an exception when you can do something useful:

- recover,
- add context and rethrow,
- translate it to another layer,
- return a proper error response,
- clean up resources.

Do not catch exceptions just to hide them.

### What is the difference between `Exception` and `Error`?

`Exception` represents conditions that applications may catch and handle.

`Error` represents serious JVM-level problems, like `OutOfMemoryError` or `StackOverflowError`.

Applications usually should not catch `Error`.

## Common Mistakes

Mistake 1: Swallowing exceptions.

Bad:

```java
try {
    processOrder(order);
} catch (Exception e) {
}
```

This hides the problem.

The system may continue in a broken state.

Mistake 2: Catching `Exception` everywhere.

This is too broad.

You may accidentally catch things you did not intend to handle.

Mistake 3: Throwing checked exceptions from every business method.

This can make code noisy and force callers to handle things they cannot actually recover from.

Mistake 4: Returning `null` instead of signaling failure clearly.

Sometimes `Optional` is better.

Sometimes an exception is better.

But silent `null` is often a bug factory.

## Mid/Senior Notes

Exception strategy is an architecture decision.

In backend applications, common practice is:

- use unchecked exceptions for validation and business rule violations,
- translate exceptions at boundaries,
- avoid leaking low-level exceptions into API responses,
- log exceptions once at the right level,
- include enough context for debugging,
- avoid exposing sensitive details to clients.

Example:

```java
throw new OrderAlreadyPaidException(orderId);
```

This is better than:

```java
throw new RuntimeException("error");
```

Specific exceptions communicate intent.

In Spring, you often translate exceptions into HTTP responses using `@ControllerAdvice`.

Example:

```text
OrderAlreadyPaidException -> 409 Conflict
ValidationException       -> 400 Bad Request
ResourceNotFoundException -> 404 Not Found
```

Senior-level answer:

```text
I do not catch exceptions everywhere. I catch them where I can add value: recover,
translate, add context, or return a meaningful response. Otherwise I let them
propagate to a central error handling layer.
```

## Quick Summary

Exceptions signal runtime failure.

Checked exceptions must be handled or declared.

Unchecked exceptions extend `RuntimeException`.

Do not swallow exceptions.

Catch exceptions only when you can do something useful.

Use meaningful exception types in business code.

