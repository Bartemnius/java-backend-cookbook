---
layout: default
title: How Java Code Runs
parent: Java Fundamentals
grand_parent: Book
nav_order: 1
---

# How Java Code Runs: Source Code, Bytecode, JVM


## Simple Explanation

Java code does not usually run directly on your operating system.

First, you write source code in a `.java` file.

Then the Java compiler turns that source code into bytecode, stored in a `.class` file.

Then the JVM runs that bytecode.

The flow looks like this:

```text
Hello.java  ->  javac compiler  ->  Hello.class  ->  JVM  ->  running program
```

Think about it like this:

- `.java` is what humans write,
- `.class` is what the compiler produces,
- JVM is the machine that understands and runs `.class` files.

This is one of the reasons Java is portable.

The same bytecode can run on different operating systems, as long as there is a compatible JVM installed there.

That is the idea behind the classic phrase:

```text
Write once, run anywhere.
```

It does not mean every application magically works everywhere without configuration. Real applications still depend on files, networks, operating system behavior, environment variables, containers, and external services.

But the core Java bytecode model makes Java much more portable than languages that compile directly into one platform-specific binary by default.

## Why It Exists

Java was designed to solve a practical problem: developers wanted to write code once and run it on many machines.

Without the JVM model, you often need to compile a separate version of your program for each target platform.

Java adds a middle layer:

```text
Java source code -> platform-independent bytecode -> platform-specific JVM
```

The JVM hides many operating system differences from your application.

This is why the JVM is so important. It is not only "the thing that runs Java." It is the runtime environment that manages execution, memory, garbage collection, class loading, JIT compilation, threads, and many other low-level details.

## Basic Code Example

Create a file called `Hello.java`:

```java
public class Hello {
    public static void main(String[] args) {
        System.out.println("Hello, Java");
    }
}
```

Compile it:

```bash
javac Hello.java
```

This creates:

```text
Hello.class
```

Run it:

```bash
java Hello
```

Output:

```text
Hello, Java
```

Important detail:

You run `java Hello`, not `java Hello.class`.

You give the JVM the class name, not the file name.

## What Happens Step By Step

When you run this program, a simplified version of the process looks like this:

1. You write `Hello.java`.
2. `javac` checks the code and compiles it.
3. The compiler creates `Hello.class`.
4. The JVM starts.
5. The JVM loads the `Hello` class.
6. The JVM looks for the `main` method.
7. The JVM executes the bytecode.
8. The program prints text to the console.

This is simplified, but it is enough for most interviews.

## Common Interview Questions

### What is bytecode?

Bytecode is the compiled form of Java source code.

It is not normal machine code for Windows, Linux, or macOS. It is an intermediate format that the JVM understands.

Good interview answer:

```text
Bytecode is the output of Java compilation. The compiler turns `.java` files into
`.class` files containing bytecode. The JVM then executes that bytecode, which is
one reason Java programs can run on different platforms with compatible JVMs.
```

### Why is Java called platform-independent?

Java is called platform-independent because Java source code is compiled into bytecode, and bytecode can run on any platform that has a compatible JVM.

The JVM itself is platform-specific, but the bytecode is not tied to one operating system in the same direct way as a native executable.

### Is Java interpreted or compiled?

Java uses both compilation and runtime execution techniques.

First, Java source code is compiled by `javac` into bytecode.

Then the JVM runs that bytecode. Modern JVMs also use JIT compilation, which means frequently executed bytecode can be compiled into native machine code at runtime for better performance.

Good interview answer:

```text
Java is compiled to bytecode first. Then the JVM executes that bytecode.
Modern JVMs also use JIT compilation, so hot parts of the program can be compiled
to native machine code while the application is running.
```

### What is the role of the JVM?

The JVM runs Java bytecode.

But in practice, it does more than just execute instructions. It also handles class loading, memory management, garbage collection, thread management, runtime optimizations, and safety checks.

### What is the `main` method?

The `main` method is the standard entry point for a basic Java application.

```java
public static void main(String[] args)
```

It is where execution starts when you run a simple Java program from the command line.

## Common Mistakes

Mistake 1: Saying that Java source code runs directly on the JVM.

More accurate:

```text
Java source code is compiled to bytecode first. The JVM runs the bytecode.
```

Mistake 2: Saying that JVM means Java compiler.

The compiler is usually `javac`.

The JVM is the runtime that executes bytecode.

Mistake 3: Thinking platform independence means zero platform problems.

Real applications can still depend on operating system paths, native libraries, environment variables, file permissions, time zones, network setup, and container configuration.

Java gives you portability at the bytecode/runtime level, not a guarantee that every production deployment will be effortless.

## Mid/Senior Notes

At mid and senior levels, interviewers may expect more than the simple `.java -> .class -> JVM` story.

You should know that the JVM is a serious runtime platform, not only a Java launcher.

Important deeper points:

- The JVM loads classes dynamically when they are needed.
- The JVM verifies bytecode before execution for safety.
- The JVM manages memory and garbage collection.
- The JVM can optimize code while the program runs.
- JIT compilation can make frequently used code faster over time.
- Many languages besides Java can run on the JVM, for example Kotlin, Scala, and Groovy.

This matters because backend performance is often affected by runtime behavior.

For example, a Java application may start slower than a tiny native binary, but after warm-up the JVM can optimize hot paths very aggressively.

That is why performance discussions around Java should be careful. "Java is slow" is an outdated and lazy statement. A better answer is:

```text
Java has JVM startup and warm-up costs, but modern JVMs can optimize running code
very well. Performance depends on the workload, garbage collector, memory usage,
JIT behavior, application design, and infrastructure.
```

## When To Use This Knowledge

You use this knowledge when:

- explaining Java portability,
- debugging classpath or runtime issues,
- understanding build tools like Maven and Gradle,
- discussing JVM performance,
- working with containers,
- preparing for questions about JDK, JRE, JVM, bytecode, and compilation.

This is one of those topics that looks basic, but weak answers here make a bad first impression.

## Quick Summary

Java source code is written in `.java` files.

The compiler turns `.java` files into `.class` files containing bytecode.

The JVM runs bytecode.

This bytecode model is one of the main reasons Java is portable.

Modern JVMs also manage memory, load classes, run garbage collection, and optimize code at runtime using JIT compilation.

