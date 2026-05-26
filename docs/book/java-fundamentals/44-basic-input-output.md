---
layout: default
title: Basic Input/Output
parent: Java Fundamentals
grand_parent: Book
nav_order: 44
---

# Basic Input/Output

## Simple Explanation

Input/output means reading data from somewhere or writing data somewhere.

Examples:

```text
read a file
write a file
read user input
send bytes to a network socket
write logs
```

In Java, basic file IO is commonly done with:

```java
java.nio.file.Files
java.nio.file.Path
```

Modern code usually prefers `java.nio.file` over old `java.io.File` for file path operations.

## Why It Exists

Programs need to communicate with the outside world.

Without IO, a program can only work with data already inside memory.

Backend applications use IO constantly:

- reading configuration,
- writing logs,
- uploading files,
- downloading reports,
- reading CSV files,
- communicating with databases,
- calling HTTP APIs.

IO is also one of the main sources of failure.

Files can be missing.

Disks can be full.

Permissions can be wrong.

Networks can timeout.

## Reading A File

```java
import java.io.IOException;
import java.nio.file.Files;
import java.nio.file.Path;

public class ReadFileExample {
    public String read(Path path) throws IOException {
        return Files.readString(path);
    }
}
```

This reads the whole file into a string.

Good for small files.

Bad for huge files.

## Writing A File

```java
import java.io.IOException;
import java.nio.file.Files;
import java.nio.file.Path;

public class WriteFileExample {
    public void write(Path path, String content) throws IOException {
        Files.writeString(path, content);
    }
}
```

This writes text to a file.

## Reading Lines

For line-based files:

```java
List<String> lines = Files.readAllLines(path);
```

For large files, stream lines instead of loading everything:

```java
try (Stream<String> lines = Files.lines(path)) {
    lines.forEach(System.out::println);
}
```

`Files.lines()` opens a resource, so use try-with-resources.

## Bytes vs Characters

Computers store bytes.

Text is characters encoded as bytes.

That is why character encoding matters.

Example:

```java
String content = Files.readString(path, StandardCharsets.UTF_8);
```

Use explicit encodings when it matters.

`UTF-8` is the common default choice in modern systems.

## Common Interview Questions

### What is IO in Java?

Good answer:

```text
IO means input/output: reading data from sources like files or network connections
and writing data to destinations. In modern Java, file IO often uses Path and
Files from java.nio.file.
```

### What is the difference between byte streams and character streams?

Byte streams work with raw bytes.

Character streams work with text characters and use encoding.

Use byte streams for binary data like images.

Use character readers/writers for text.

### Why should resources be closed?

Resources like files, streams, sockets, and database connections use operating system resources.

If you do not close them, the application can leak resources.

Use try-with-resources.

### Should you read a huge file with `Files.readString()`?

No.

`Files.readString()` loads the whole file into memory.

For huge files, process line by line or stream the data.

## Common Mistakes

Mistake 1: Not closing resources.

Use:

```java
try (BufferedReader reader = Files.newBufferedReader(path)) {
    // use reader
}
```

Mistake 2: Loading huge files into memory.

Bad:

```java
String content = Files.readString(hugeFile);
```

Use streaming for large files.

Mistake 3: Ignoring encoding.

Text can break when the wrong encoding is used.

Be explicit when needed:

```java
StandardCharsets.UTF_8
```

Mistake 4: Treating IO exceptions as impossible.

IO fails often.

Handle or propagate failures intentionally.

## Mid/Senior Notes

IO is slow compared to memory operations.

Backend performance problems are often IO-related:

- database calls,
- HTTP calls,
- file reads,
- network latency,
- disk throughput.

Good backend thinking:

```text
CPU work is often not the bottleneck. IO usually is.
```

For file uploads/downloads, avoid loading huge content fully into memory unless the size is controlled.

For production systems, think about:

- file size limits,
- streaming,
- timeouts,
- retries,
- backpressure,
- cleanup,
- temporary files,
- security scanning,
- path traversal attacks.

Senior-level answer:

```text
For small files, Files.readString or readAllLines can be fine. For large files or
untrusted input, I prefer streaming and clear resource management with try-with-resources.
I also pay attention to encoding, size limits, and failure handling.
```

## Quick Summary

IO means reading and writing data.

Use `Path` and `Files` for modern basic file operations.

Close resources with try-with-resources.

Use byte streams for binary data.

Use character streams for text.

Do not load huge files into memory blindly.

