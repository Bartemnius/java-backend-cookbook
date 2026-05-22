---
layout: default
title: try-with-resources
parent: Java Fundamentals
grand_parent: Book
nav_order: 32
---

# try-with-resources

## Simple Explanation

`try-with-resources` automatically closes resources after use.

Example:

```java
try (BufferedReader reader = Files.newBufferedReader(path)) {
    String line = reader.readLine();
    System.out.println(line);
}
```

When the `try` block finishes, Java closes the reader automatically.

Even if an exception happens.

## Why It Exists

Some objects need to be closed:

- files,
- streams,
- database connections,
- sockets,
- readers,
- writers.

If you forget to close them, your application can leak resources.

Before try-with-resources, code often looked like this:

```java
BufferedReader reader = null;

try {
    reader = Files.newBufferedReader(path);
    System.out.println(reader.readLine());
} finally {
    if (reader != null) {
        reader.close();
    }
}
```

This is noisy.

`try-with-resources` is cleaner and safer.

## Basic Code Example

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.nio.file.Files;
import java.nio.file.Path;

public class FileExample {
    public void printFirstLine(Path path) throws IOException {
        try (BufferedReader reader = Files.newBufferedReader(path)) {
            System.out.println(reader.readLine());
        }
    }
}
```

`BufferedReader` is closed automatically.

## AutoCloseable

For an object to work with try-with-resources, it must implement `AutoCloseable`.

Example:

```java
public class DatabaseConnection implements AutoCloseable {
    public void query(String sql) {
        System.out.println("Running query: " + sql);
    }

    @Override
    public void close() {
        System.out.println("Closing connection");
    }
}
```

Usage:

```java
try (DatabaseConnection connection = new DatabaseConnection()) {
    connection.query("select * from users");
}
```

After the block, `close()` is called.

## Multiple Resources

You can open multiple resources:

```java
try (
        BufferedReader reader = Files.newBufferedReader(input);
        BufferedWriter writer = Files.newBufferedWriter(output)
) {
    writer.write(reader.readLine());
}
```

Resources are closed in reverse order of creation.

## Common Interview Questions

### What is try-with-resources?

Good answer:

```text
try-with-resources is a Java construct that automatically closes resources after
the try block finishes. The resource must implement AutoCloseable.
```

### What interface must a resource implement?

`AutoCloseable`.

Older APIs may implement `Closeable`, which extends `AutoCloseable`.

### Does try-with-resources close resources if an exception is thrown?

Yes.

The resource is closed when the block exits, whether normally or because of an exception.

### In what order are multiple resources closed?

In reverse order of creation.

The last opened resource is closed first.

## Common Mistakes

Mistake 1: Manually closing resources when try-with-resources would be simpler.

Old style:

```java
try {
    reader = Files.newBufferedReader(path);
} finally {
    reader.close();
}
```

Better:

```java
try (BufferedReader reader = Files.newBufferedReader(path)) {
    // use reader
}
```

Mistake 2: Thinking garbage collection closes resources reliably.

Garbage collection manages memory.

It is not a resource management strategy for files, sockets, or database connections.

Close resources explicitly.

Mistake 3: Ignoring suppressed exceptions.

If both the try block and `close()` throw exceptions, Java may attach the close exception as a suppressed exception.

This matters during debugging.

## Mid/Senior Notes

try-with-resources is important for production stability.

Resource leaks can cause serious problems:

- too many open files,
- exhausted database connection pool,
- socket leaks,
- slow degradation over time.

In backend applications, you often do not manually open JDBC connections because frameworks and connection pools manage them.

But the principle still matters:

```text
Who owns this resource?
Who closes it?
What happens if an exception occurs?
```

Good code has a clear answer.

Also remember that try-with-resources is deterministic cleanup. The resource is closed at the end of the block.

That is very different from waiting for garbage collection.

## Quick Summary

try-with-resources automatically closes resources.

Resources must implement `AutoCloseable`.

It closes resources even when exceptions happen.

Multiple resources close in reverse order.

Use it for files, streams, sockets, and other closeable resources.

