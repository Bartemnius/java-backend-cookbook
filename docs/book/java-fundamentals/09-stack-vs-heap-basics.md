---
layout: default
title: Stack Vs Heap Basics
parent: Java Fundamentals
grand_parent: Book
nav_order: 9
---

# Stack Vs Heap Basics


## Simple Explanation

Stack and heap are two memory areas commonly discussed in Java.

The stack is used for method calls and local variables.

The heap is used for objects.

Simple mental model:

```text
Stack -> method calls, local variables, references
Heap  -> objects
```

Example:

```java
public void run() {
    User user = new User("anna@example.com");
}
```

Simplified:

```text
Stack:
  user reference

Heap:
  User object with email = "anna@example.com"
```

The variable `user` is local to the method, so the reference is on the stack.

The `User` object created with `new` is on the heap.

This is a simplified model, but it is useful for interviews.

## Why It Exists

Programs need memory for different things.

Method calls are temporary.

When a method starts, it gets a stack frame.

When the method ends, that stack frame is removed.

Objects often need to live longer than one method call, so they are stored on the heap and managed by the garbage collector.

## Basic Code Example

```java
public class MemoryExample {
    public static void main(String[] args) {
        int age = 30;
        User user = new User("anna@example.com");
        printUser(user);
    }

    private static void printUser(User user) {
        System.out.println(user.getEmail());
    }
}
```

Simplified view:

```text
main stack frame:
  age = 30
  user -> reference to User object

heap:
  User("anna@example.com")

printUser stack frame:
  user -> reference to the same User object
```

Both `main` and `printUser` can have a local variable named `user`.

Those are separate local variables.

But they can point to the same object on the heap.

## Common Interview Questions

### What is stored on the stack?

Method call frames, local primitive variables, and references to objects are commonly described as stack data.

Good simple answer:

```text
The stack stores method calls and local variables. For object variables, the local
reference is on the stack, while the object itself is usually on the heap.
```

### What is stored on the heap?

Objects are stored on the heap and managed by the garbage collector.

### Why can stack overflow happen?

Stack overflow usually happens when method calls are nested too deeply, often because of infinite or too-deep recursion.

Example:

```java
public void callForever() {
    callForever();
}
```

Each call needs a new stack frame. Eventually, the stack runs out of space.

### Why can out of memory happen?

Out of memory can happen when the application needs more heap memory than available, often because too many objects are created or references prevent garbage collection.

## Common Mistakes

Mistake 1: Saying "primitive types are always on the stack."

That is too simplistic.

Local primitive variables are typically discussed as stack values. But primitive fields inside objects are part of those objects, and objects live on the heap.

Example:

```java
public class User {
    private int age;
}
```

The `age` field is inside the `User` object.

If the `User` object is on the heap, the field is part of that heap object.

Mistake 2: Thinking the stack is manually managed by Java developers.

Java developers do not manually allocate and free stack frames. Method calls create them, method returns remove them.

Mistake 3: Thinking garbage collection cleans the stack.

Garbage collection is mainly about heap objects. Stack frames disappear automatically when methods return.

## Mid/Senior Notes

The stack/heap explanation is a mental model, not the full JVM optimization story.

Modern JVMs can optimize aggressively. For example, escape analysis may allow the JVM to optimize object allocation in ways that do not match the simple "everything created with `new` is always a normal heap allocation forever" explanation.

For interviews, do not start with that complexity.

Start simple:

```text
Local variables and method calls are associated with the stack.
Objects are generally allocated on the heap and managed by the garbage collector.
```

Then, if the interviewer goes deeper, mention that the JVM may optimize allocation internally.

That shows maturity without confusing the basic answer.

## Quick Summary

The stack stores method calls and local variables.

The heap stores objects.

Local variables can hold references to heap objects.

Stack overflow is usually caused by too many nested method calls.

Out of memory is usually connected to heap pressure or memory leaks.

The simple model is useful, but the JVM can optimize internally.

