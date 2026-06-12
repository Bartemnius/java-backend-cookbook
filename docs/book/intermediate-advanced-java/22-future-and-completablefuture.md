---
layout: default
title: Future And CompletableFuture
parent: Intermediate And Advanced Java
grand_parent: Book
nav_order: 22
---

# Future And CompletableFuture

## Simple Explanation

`Future` represents a result that may be available later.

You submit work to another thread.

Instead of getting the result immediately, you get a `Future`.

Later, you can ask the `Future` for the result.

`CompletableFuture` is a more powerful version.

It lets you chain asynchronous steps, combine results, and handle errors more fluently.

Simple idea:

```text
Future = a placeholder for a later result
CompletableFuture = a later result that can be composed with more work
```

## Why It Exists

Asynchronous work creates a problem:

If work runs in another thread, how does the caller get the result?

One option is to block and wait.

Another option is to attach more work that should run when the result is ready.

`Future` supports the first style.

`CompletableFuture` supports both, especially the second style.

This matters in backend applications when you call slow external systems, run background tasks, or combine independent operations.

## Future

`Future<T>` represents a result of type `T` that may not be ready yet.

Example:

```java
ExecutorService executor = Executors.newSingleThreadExecutor();

Future<Integer> future = executor.submit(() -> {
    return 21 * 2;
});

Integer result = future.get();

executor.shutdown();
```

`future.get()` blocks until the result is ready.

That is the key limitation.

`Future` gives you a handle to async work, but getting the result is usually blocking.

## Useful Future Methods

Common methods:

```java
boolean cancel(boolean mayInterruptIfRunning);
boolean isCancelled();
boolean isDone();
T get();
T get(long timeout, TimeUnit unit);
```

`isDone()` tells you whether the task completed.

`get()` waits for the result.

`get(timeout, unit)` waits only for a limited time.

`cancel(true)` attempts to cancel the task and may interrupt it.

Cancellation requires cooperation from the running task.

## Future Exceptions

If the task throws an exception, `Future.get()` throws `ExecutionException`.

The original exception is inside it.

Example:

```java
Future<String> future = executor.submit(() -> {
    throw new IllegalStateException("Something failed");
});

try {
    future.get();
} catch (ExecutionException e) {
    Throwable original = e.getCause();
    System.out.println(original.getMessage());
}
```

This wrapping is important for interviews.

The exception does not usually appear at `submit()` time.

It appears when you observe the result.

## CompletableFuture

`CompletableFuture<T>` is a more flexible future.

You can create async work:

```java
CompletableFuture<String> future = CompletableFuture.supplyAsync(() -> {
    return "user-123";
});

String result = future.join();
```

`supplyAsync()` is used when the task returns a value.

`runAsync()` is used when the task returns nothing:

```java
CompletableFuture<Void> future = CompletableFuture.runAsync(() -> {
    System.out.println("Background work");
});
```

## Chaining

`CompletableFuture` can chain operations.

Example:

```java
CompletableFuture<String> future = CompletableFuture
        .supplyAsync(() -> "john")
        .thenApply(name -> name.toUpperCase())
        .thenApply(name -> "Hello " + name);

String result = future.join();
```

`thenApply()` transforms the result.

It is similar to `map()` in streams.

## Consuming a Result

Use `thenAccept()` when you want to consume the result but not return another value.

```java
CompletableFuture<Void> future = CompletableFuture
        .supplyAsync(() -> "report.csv")
        .thenAccept(fileName -> System.out.println("Generated " + fileName));
```

The final future has type `CompletableFuture<Void>` because the chain no longer returns a business value.

## Combining Independent Tasks

`CompletableFuture` can combine independent async operations.

Example:

```java
CompletableFuture<Integer> price = CompletableFuture.supplyAsync(() -> 100);
CompletableFuture<Integer> tax = CompletableFuture.supplyAsync(() -> 23);

CompletableFuture<Integer> total = price.thenCombine(tax, Integer::sum);

Integer result = total.join();
```

`thenCombine()` waits for both futures and combines their results.

This is useful when two operations are independent.

If one operation depends on another, chaining is usually clearer.

## Waiting For Many Tasks

`allOf()` creates a future that completes when all given futures complete.

```java
CompletableFuture<String> first = CompletableFuture.supplyAsync(() -> "A");
CompletableFuture<String> second = CompletableFuture.supplyAsync(() -> "B");

CompletableFuture<Void> all = CompletableFuture.allOf(first, second);

all.join();

String firstResult = first.join();
String secondResult = second.join();
```

Be careful:

`allOf()` returns `CompletableFuture<Void>`.

It does not directly give you a list of results.

You still collect results from the original futures.

## Error Handling

Use `exceptionally()` to recover from failure:

```java
CompletableFuture<String> future = CompletableFuture
        .supplyAsync(() -> {
            throw new IllegalStateException("Failed");
        })
        .exceptionally(error -> "fallback");
```

Use `handle()` when you want access to both success and failure:

```java
CompletableFuture<String> future = CompletableFuture
        .supplyAsync(() -> "data")
        .handle((value, error) -> {
            if (error != null) {
                return "fallback";
            }
            return value;
        });
```

Error handling must be explicit.

Async failures are easy to lose if nobody observes them.

## `get()` vs `join()`

Both can wait for a result.

`get()` throws checked exceptions:

```java
String value = future.get();
```

It can throw `InterruptedException` and `ExecutionException`.

`join()` throws unchecked `CompletionException`:

```java
String value = future.join();
```

In many examples, `join()` looks cleaner.

In production code, choose deliberately.

Checked interruption handling may matter.

## Default Executor

By default, many `CompletableFuture` async methods use the common `ForkJoinPool`.

Example:

```java
CompletableFuture<String> future = CompletableFuture.supplyAsync(() -> {
    return "data";
});
```

This uses the default async executor.

For production backend code, this can be a bad hidden dependency.

You can provide your own executor:

```java
ExecutorService executor = Executors.newFixedThreadPool(4);

CompletableFuture<String> future = CompletableFuture.supplyAsync(() -> {
    return "data";
}, executor);

executor.shutdown();
```

This makes resource ownership clearer.

## Common Interview Questions

### What is a `Future`?

A `Future` represents the result of an asynchronous computation.

The result may not be ready yet.

You can wait for it with `get()`, check completion with `isDone()`, or try to cancel it.

### What is the main limitation of `Future`?

The main limitation is poor composition.

You usually call `get()` to obtain the result, which blocks the current thread.

Chaining and combining async operations is awkward with plain `Future`.

### What is `CompletableFuture`?

`CompletableFuture` is an advanced future that supports chaining, combining, manual completion, and error handling.

It is useful for composing asynchronous workflows.

### What is the difference between `thenApply()` and `thenAccept()`?

`thenApply()` transforms a result and returns a new result.

`thenAccept()` consumes a result and returns no business value.

### What is the difference between `thenApply()` and `thenCompose()`?

`thenApply()` transforms a value.

`thenCompose()` is used when the transformation itself returns another `CompletableFuture`.

It avoids nested futures like `CompletableFuture<CompletableFuture<T>>`.

### What does `allOf()` return?

`allOf()` returns `CompletableFuture<Void>`.

It completes when all given futures complete, but it does not directly return their results.

You collect results from the original futures.

### What is the difference between `get()` and `join()`?

`get()` throws checked exceptions such as `InterruptedException` and `ExecutionException`.

`join()` throws unchecked `CompletionException`.

Both can block.

### Does `CompletableFuture` make code non-blocking automatically?

No.

It helps structure asynchronous workflows.

But if you call `join()` or `get()`, the current thread blocks.

Also, the async work still runs on some executor.

That executor can become overloaded.

## Common Mistakes

### Calling `get()` immediately after submitting work

This often removes the benefit of asynchronous execution.

Example:

```java
Future<String> future = executor.submit(() -> loadData());
String data = future.get();
```

This may be fine if you only want to move work to another thread.

But if the current thread immediately waits, you did not gain much concurrency.

### Ignoring timeouts

Waiting forever is dangerous in backend systems.

Prefer timeouts when waiting for external work.

Without timeouts, one stuck dependency can hold resources indefinitely.

### Forgetting the executor

Async work always runs somewhere.

If you do not provide an executor to `CompletableFuture`, it may use the common pool.

That may be fine for demos.

It may be weak engineering in production.

### Hiding exceptions

Async exceptions can disappear if nobody observes the future.

Always decide how failures are logged, propagated, or recovered.

### Creating unreadable async chains

`CompletableFuture` can become messy when chains are too long or error handling is scattered.

Readable async code still needs structure.

Do not turn simple sequential logic into a clever async maze without a performance reason.

## Mid/Senior Notes

At junior level, know that `Future` represents a later result.

At mid level, understand blocking, timeouts, cancellation, and exception wrapping.

At senior level, focus on system behavior:

- What executor runs this work?
- Is the workload CPU-bound or I/O-bound?
- What happens when the executor is full?
- Are timeouts enforced?
- How is cancellation handled?
- How are failures observed?
- Is parallelism actually useful here?
- Does async code make the design clearer or just harder to debug?

Asynchronous code is not automatically better.

It is useful when it improves throughput, responsiveness, or separation of work.

It is harmful when it hides control flow and failure handling.

## When To Use It / When Not To Use It

Use `Future` when:

- you submit a task and need a simple handle to its result
- blocking for the result is acceptable
- simple executor-based async work is enough

Use `CompletableFuture` when:

- you need to chain async steps
- you need to combine independent async results
- you need fluent error handling
- you want to avoid manual callback-style code

Be careful when:

- you immediately block anyway
- no timeout exists
- the executor is unclear
- the async chain becomes hard to read
- the work touches shared mutable state

## Quick Summary

`Future` is a placeholder for a result that may be available later.

`Future.get()` blocks and wraps task failures in `ExecutionException`.

`CompletableFuture` adds chaining, combining, and better async workflow composition.

`thenApply()` transforms a result.

`thenAccept()` consumes a result.

`thenCombine()` combines two independent results.

`allOf()` waits for many futures but returns `CompletableFuture<Void>`.

Async code still needs clear executors, timeouts, cancellation, and error handling.

