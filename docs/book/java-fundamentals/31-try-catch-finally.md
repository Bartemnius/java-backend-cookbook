---
layout: default
title: try/catch/finally
parent: Java Fundamentals
grand_parent: Book
nav_order: 31
---

# try/catch/finally

## Simple Explanation

`try/catch/finally` is Java's basic structure for handling exceptions.

```java
try {
    // code that may fail
} catch (Exception e) {
    // handle failure
} finally {
    // code that runs after try/catch
}
```

Simple meaning:

```text
try     -> attempt risky code
catch   -> handle an exception
finally -> run cleanup code
```

## Why It Exists

Some operations can fail:

- reading files,
- calling APIs,
- parsing input,
- accessing databases,
- converting text to numbers.

`try/catch/finally` lets you handle those failures in a controlled way.

Without it, failures would crash through the program without structure.

## Basic Code Example

```java
public class ParseExample {
    public static void main(String[] args) {
        String input = "abc";

        try {
            int number = Integer.parseInt(input);
            System.out.println(number);
        } catch (NumberFormatException e) {
            System.out.println("Input is not a valid number");
        } finally {
            System.out.println("Parsing attempt finished");
        }
    }
}
```

Output:

```text
Input is not a valid number
Parsing attempt finished
```

`finally` runs even when an exception is thrown and caught.

## Multiple catch Blocks

You can catch different exception types:

```java
try {
    process(input);
} catch (IllegalArgumentException e) {
    System.out.println("Invalid input");
} catch (IllegalStateException e) {
    System.out.println("Invalid state");
}
```

Order matters.

Catch more specific exceptions before more general exceptions.

Bad:

```java
try {
    process(input);
} catch (Exception e) {
    System.out.println("General error");
} catch (IllegalArgumentException e) {
    System.out.println("Invalid input");
}
```

This does not compile because `IllegalArgumentException` is already covered by `Exception`.

## finally

`finally` is often used for cleanup:

```java
Resource resource = null;

try {
    resource = openResource();
    resource.use();
} catch (Exception e) {
    System.out.println("Something failed");
} finally {
    if (resource != null) {
        resource.close();
    }
}
```

In modern Java, `try-with-resources` is usually better for closing resources.

But `finally` is still important to understand.

## Common Interview Questions

### Does `finally` always run?

Usually, yes.

`finally` normally runs after `try` or `catch`.

But there are extreme cases where it may not run, for example:

- JVM exits with `System.exit()`,
- JVM crashes,
- process is killed,
- hardware failure.

Good interview answer:

```text
In normal control flow, finally runs after try/catch, even if an exception is thrown.
But it is not guaranteed if the JVM exits or crashes.
```

### Can you have try without catch?

Yes, if you have `finally`:

```java
try {
    doWork();
} finally {
    cleanup();
}
```

And in modern Java, you can also use try-with-resources.

### Can catch be used without try?

No.

`catch` belongs to a `try`.

### Should you catch `Exception`?

Sometimes at application boundaries, but not everywhere.

Usually, catch the most specific exception you can handle.

## Common Mistakes

Mistake 1: Empty catch block.

Bad:

```java
try {
    process();
} catch (Exception e) {
}
```

This hides bugs.

Mistake 2: Logging and rethrowing everywhere.

```java
catch (Exception e) {
    log.error("Failed", e);
    throw e;
}
```

If every layer does this, you get duplicated logs.

Log at the right boundary.

Mistake 3: Returning from `finally`.

Bad:

```java
try {
    return "try";
} finally {
    return "finally";
}
```

The `finally` return wins and hides the original return.

Do not do this.

## Mid/Senior Notes

Exception handling should preserve useful context.

Weak:

```java
catch (IOException e) {
    throw new RuntimeException("Failed");
}
```

Better:

```java
catch (IOException e) {
    throw new FileImportException("Failed to import file: " + path, e);
}
```

Notice the original exception is passed as the cause.

That preserves the stack trace.

Also, catch exceptions at the right layer.

Example:

```text
Repository layer -> may throw database exception
Service layer    -> may translate to business exception
Controller layer -> may translate to HTTP response
```

Do not randomly catch and hide exceptions in the middle of the application.

Senior-level answer:

```text
I catch exceptions where I can add value. I preserve the cause when wrapping,
avoid empty catch blocks, and avoid duplicated logging across layers.
```

## Quick Summary

`try` runs risky code.

`catch` handles exceptions.

`finally` runs cleanup code in normal control flow.

Catch specific exceptions when possible.

Do not swallow exceptions.

Avoid returning from `finally`.

