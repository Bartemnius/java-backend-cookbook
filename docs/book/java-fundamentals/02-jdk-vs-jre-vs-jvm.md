---
layout: default
title: JDK vs JRE vs JVM
parent: Java Fundamentals
grand_parent: Book
nav_order: 2
---

# JDK vs JRE vs JVM


## Simple Explanation

These three names are easy to confuse:

```text
JDK = tools for developers
JRE = environment for running Java applications
JVM = engine that executes Java bytecode
```

The simplest way to remember it:

```text
JDK includes tools like javac.
JRE provides what is needed to run Java programs.
JVM is the actual virtual machine that runs bytecode.
```

In modern Java, this topic is a little more confusing because separate JRE downloads are no longer the main way many developers install Java. Most developers install a JDK, and production applications are often shipped in containers or custom runtime images.

Still, interviewers ask this question often because it checks whether you understand the basic Java runtime model.

## Why It Exists

Different people need different parts of the Java platform.

A developer needs tools to write, compile, debug, and package code.

A machine that only runs an application does not always need all developer tools.

That is the conceptual difference:

- development needs the JDK,
- running needs a runtime environment,
- bytecode execution happens inside the JVM.

## Basic Example

If you want to compile this file:

```java
public class App {
    public static void main(String[] args) {
        System.out.println("Running");
    }
}
```

You need `javac`:

```bash
javac App.java
```

`javac` is part of the JDK.

Then you run:

```bash
java App
```

The `java` launcher starts the JVM, which runs the compiled bytecode.

## Common Interview Questions

### What is the difference between JDK, JRE, and JVM?

Good interview answer:

```text
The JVM executes Java bytecode. The JRE is the runtime environment needed to run
Java applications. The JDK is for developers and includes tools like the compiler,
debugger, and other utilities. In practice, modern developers usually install a JDK.
```

### Do you need a JDK to run Java code?

To compile Java code, yes.

To only run an already compiled Java application, conceptually you only need a runtime. In modern setups, many teams still use a JDK or a slim/custom runtime image in production, especially in containers.

### Is JVM platform-independent?

No.

The JVM is platform-specific. There is a JVM build for Windows, Linux, macOS, and different CPU architectures.

Java bytecode is the platform-independent part.

That distinction is important.

## Common Mistakes

Mistake 1: Saying "JDK runs Java."

Better:

```text
The JVM runs bytecode. The JDK gives developers tools, including the compiler and
the java launcher.
```

Mistake 2: Saying the JVM is the same on every operating system.

The JVM follows the same specification, but the actual binary is platform-specific.

Mistake 3: Ignoring modern deployment.

Old explanations often say: "Install JRE on the server." That is not always how modern backend deployment works. Today, applications are often packaged into Docker images with a chosen JDK/JRE base image or a custom runtime built with tools like `jlink`.

## Mid/Senior Notes

For real backend work, this topic connects to deployment.

In production, you should care about:

- which Java version your application targets,
- which JDK distribution you use,
- whether your Docker image includes unnecessary tools,
- security updates,
- image size,
- startup time,
- compatibility with your framework and libraries.

For 2026, Java 25 is an LTS release according to Oracle's Java SE Support Roadmap, while Java 21 remains a widely used modern LTS baseline in many projects. For this book, the examples focus on fundamentals that apply cleanly to modern Java, with Java 21+ features included where they matter.

The exact version matters most when you use newer language features, virtual threads, pattern matching, records, sealed classes, or framework versions that require a minimum Java version.

## Quick Summary

JVM runs bytecode.

JRE is the runtime environment.

JDK is the developer kit and includes tools like `javac`.

Modern developers usually install a JDK, and production systems often use containers or custom runtime images instead of old-style standalone JRE installations.

