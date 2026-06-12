---
layout: default
title: Runnable Vs Callable
parent: Intermediate And Advanced Java
grand_parent: Book
nav_order: 20
---

# Runnable Vs Callable

## Simple Explanation

`Runnable` and `Callable` both represent work that can run in another thread.

The difference is simple:

`Runnable` does not return a result.

`Callable` returns a result.

`Runnable` cannot directly throw checked exceptions.

`Callable` can throw checked exceptions.

Basic idea:

```text
Runnable = run some work
Callable = run some work and give me a result
```

## Why It Exists

Java needs a way to describe tasks separately from the thread that runs them.

This is important because in real applications you usually do not want to create raw threads everywhere.

You want to submit tasks to an executor or thread pool.

The executor decides which thread runs the task.

Your code only describes the work.

`Runnable` and `Callable` are two common ways to describe that work.

## Runnable

`Runnable` is a functional interface with one method:

```java
@FunctionalInterface
public interface Runnable {
    void run();
}
```

It returns `void`.

Example:

```java
Runnable task = () -> {
    System.out.println("Sending email");
};

Thread thread = new Thread(task);
thread.start();
```

Use `Runnable` when you only need to perform an action.

Examples:

- write a log
- send a notification
- clean temporary files
- update a cache
- run a background action where the caller does not need a direct result

## Callable

`Callable` is a functional interface with one method:

```java
@FunctionalInterface
public interface Callable<V> {
    V call() throws Exception;
}
```

It returns a value of type `V`.

It can also throw an exception.

Example:

```java
Callable<Integer> task = () -> {
    return 42;
};
```

You normally use `Callable` with an executor:

```java
ExecutorService executor = Executors.newSingleThreadExecutor();

Future<Integer> future = executor.submit(() -> {
    return 42;
});

Integer result = future.get();

executor.shutdown();
```

The task runs in another thread.

The result is available through `Future`.

We will discuss `Future` more deeply in the next topics.

## Main Differences

| Feature | Runnable | Callable |
| --- | --- | --- |
| Method | `run()` | `call()` |
| Return value | No | Yes |
| Checked exceptions | Cannot throw directly | Can throw |
| Common result wrapper | `Future<?>` | `Future<V>` |
| Common use | Fire-and-forget work | Work that produces a result |

This is the core interview answer.

Do not overcomplicate it.

## Checked Exceptions

`Runnable.run()` does not declare checked exceptions.

This means this code does not compile:

```java
Runnable task = () -> {
    throw new IOException("Failed");
};
```

You must handle checked exceptions inside the lambda:

```java
Runnable task = () -> {
    try {
        throw new IOException("Failed");
    } catch (IOException e) {
        throw new RuntimeException(e);
    }
};
```

`Callable.call()` declares `throws Exception`.

This means checked exceptions are allowed:

```java
Callable<String> task = () -> {
    if (true) {
        throw new IOException("Failed");
    }

    return "done";
};
```

When used with `Future`, the exception is wrapped in `ExecutionException`.

## Runnable With ExecutorService

You can submit a `Runnable` to an executor:

```java
ExecutorService executor = Executors.newSingleThreadExecutor();

Future<?> future = executor.submit(() -> {
    System.out.println("Task running");
});

future.get();

executor.shutdown();
```

The `Future<?>` does not contain a useful business result.

It mainly tells you whether the task completed or failed.

## Callable With ExecutorService

You can submit a `Callable` to an executor:

```java
ExecutorService executor = Executors.newSingleThreadExecutor();

Future<String> future = executor.submit(() -> {
    return "Task result";
});

String result = future.get();

executor.shutdown();
```

Here, `Future<String>` represents a result that may not be ready yet.

Calling `get()` waits for the task to finish.

## Common Interview Questions

### What is the difference between `Runnable` and `Callable`?

`Runnable` represents a task that does not return a result and cannot directly throw checked exceptions.

`Callable` represents a task that returns a result and can throw checked exceptions.

### When would you use `Runnable`?

Use `Runnable` when you want to execute an action and the caller does not need a direct result.

For example, logging, cleanup, or fire-and-forget background work.

### When would you use `Callable`?

Use `Callable` when the task needs to return a value or report a checked exception to the caller through a `Future`.

For example, calculating a value, loading data, or calling another service and returning a response.

### Can `Runnable` throw checked exceptions?

Not directly from the `run()` method.

You must catch checked exceptions inside the task or wrap them in unchecked exceptions.

### What does `ExecutorService.submit()` return?

It returns a `Future`.

For a `Callable<T>`, it returns `Future<T>`.

For a `Runnable`, it returns `Future<?>` or a `Future<T>` if you provide a predefined result.

## Common Mistakes

### Thinking `Runnable` creates a thread

`Runnable` does not create a thread.

It only describes work.

A `Thread`, `ExecutorService`, or another runtime mechanism runs that work.

### Using `Callable<Void>` everywhere

`Callable<Void>` can work, but it often signals confusion.

If the task does not return anything meaningful, `Runnable` is usually clearer.

Use `Callable` when a result or checked exception behavior matters.

### Ignoring exceptions from submitted tasks

If a submitted task fails, the exception may sit inside the `Future`.

If nobody calls `get()`, the failure can be easy to miss.

This is one reason production task execution needs logging, monitoring, and clear error handling.

### Blocking blindly on `Future.get()`

Calling `get()` waits.

If you call it too early or in the wrong place, you may remove most of the benefit of asynchronous execution.

Submitting a task and immediately blocking for the result is sometimes fine.

But do not pretend it is magically non-blocking.

## Mid/Senior Notes

At junior level, know the method and return type differences.

At mid level, understand how these interfaces fit with executors and futures.

At senior level, think about task ownership:

- Who submits the task?
- Who observes failure?
- Who cancels it?
- Where is timeout handled?
- Is the caller allowed to block?
- Does the task touch shared state?
- Is a raw executor the right abstraction?

The interface choice is small.

The execution model around it is the real design decision.

## When To Use It / When Not To Use It

Use `Runnable` when:

- the task performs an action
- no result is needed
- checked exceptions are handled inside the task

Use `Callable` when:

- the task returns a value
- checked exceptions should be propagated through `Future`
- the caller needs to observe task completion and result

Avoid using raw `Thread` plus `Runnable` as your default production pattern.

In backend applications, prefer executor-based execution or framework-managed async support.

## Quick Summary

`Runnable` and `Callable` both describe tasks.

`Runnable.run()` returns nothing.

`Callable.call()` returns a value and can throw checked exceptions.

`Runnable` is good for actions.

`Callable` is good for tasks that produce results.

Neither one is a thread by itself.

They need something else, such as a `Thread` or `ExecutorService`, to run them.

