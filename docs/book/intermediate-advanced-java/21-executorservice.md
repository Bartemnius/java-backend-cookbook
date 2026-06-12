---
layout: default
title: ExecutorService
parent: Intermediate And Advanced Java
grand_parent: Book
nav_order: 21
---

# ExecutorService

## Simple Explanation

`ExecutorService` is a higher-level API for running tasks in threads.

Instead of creating threads manually, you submit tasks to an executor.

The executor manages the threads for you.

Simple idea:

```text
You give tasks to the executor.
The executor decides which thread runs them.
```

This is cleaner than creating `new Thread(...)` everywhere.

## Why It Exists

Creating raw threads manually is low-level and easy to misuse.

Problems with manual thread creation:

- too many threads can be created
- threads are not reused
- error handling becomes scattered
- shutdown is easy to forget
- naming and monitoring are inconsistent
- task submission and thread management are mixed together

`ExecutorService` separates two concerns:

- what work should run
- how threads are managed

That separation matters in real backend applications.

## Basic Example

```java
ExecutorService executor = Executors.newSingleThreadExecutor();

executor.submit(() -> {
    System.out.println("Task running in another thread");
});

executor.shutdown();
```

The task is submitted to the executor.

The executor runs it on a worker thread.

`shutdown()` tells the executor:

> Stop accepting new tasks and finish already submitted tasks.

If you forget shutdown in a small application, the JVM may keep running because executor threads are still alive.

## Runnable Task

Use `Runnable` when the task does not return a result:

```java
ExecutorService executor = Executors.newFixedThreadPool(4);

executor.submit(() -> {
    System.out.println("Sending email");
});

executor.shutdown();
```

This is useful for actions.

But remember: task failure must still be handled.

## Callable Task

Use `Callable` when the task returns a result:

```java
ExecutorService executor = Executors.newFixedThreadPool(4);

Future<Integer> future = executor.submit(() -> {
    return 10 + 20;
});

Integer result = future.get();

executor.shutdown();
```

`submit()` returns a `Future`.

The `Future` represents a result that may become available later.

## Fixed Thread Pool

A fixed thread pool has a fixed number of worker threads.

```java
ExecutorService executor = Executors.newFixedThreadPool(4);
```

This means up to four tasks can run at the same time.

Additional tasks wait in a queue.

This is useful when you want to limit concurrency.

But be careful.

If tasks block for a long time, the queue can grow and latency can explode.

## Single Thread Executor

A single thread executor uses one worker thread.

```java
ExecutorService executor = Executors.newSingleThreadExecutor();
```

Tasks run one after another.

This can be useful when order matters.

But it also means one slow task delays every task behind it.

## Cached Thread Pool

A cached thread pool creates threads as needed and reuses idle threads.

```java
ExecutorService executor = Executors.newCachedThreadPool();
```

This can be useful for many short-lived asynchronous tasks.

But it can also create too many threads under load.

That makes it dangerous as a default choice.

If your system receives work faster than it can complete it, a cached pool can make the situation worse.

## Scheduled Executor

`ScheduledExecutorService` runs tasks later or repeatedly.

```java
ScheduledExecutorService executor = Executors.newScheduledThreadPool(2);

executor.schedule(() -> {
    System.out.println("Runs after delay");
}, 1, TimeUnit.SECONDS);

executor.shutdown();
```

Use it for simple scheduled background work.

In larger applications, scheduling may be handled by Spring, Quartz, Kubernetes CronJobs, or another platform tool.

## Shutdown

Executor shutdown matters.

Common methods:

- `shutdown()`
- `shutdownNow()`
- `awaitTermination(...)`

`shutdown()` stops accepting new tasks but lets submitted tasks finish.

`shutdownNow()` tries to stop running tasks and returns tasks that were waiting.

It does not magically kill tasks safely.

It usually interrupts worker threads.

Tasks must cooperate with interruption.

Example:

```java
executor.shutdown();

if (!executor.awaitTermination(5, TimeUnit.SECONDS)) {
    executor.shutdownNow();
}
```

This pattern gives tasks a chance to finish before forcing a more aggressive shutdown attempt.

## `execute()` vs `submit()`

`ExecutorService` has both `execute()` and `submit()`.

`execute()` runs a `Runnable` and returns nothing:

```java
executor.execute(() -> {
    System.out.println("Fire and forget");
});
```

`submit()` returns a `Future`:

```java
Future<?> future = executor.submit(() -> {
    System.out.println("Task with observable completion");
});
```

One practical difference:

With `submit()`, task exceptions are captured in the returned `Future`.

If you never call `get()`, you may never notice the failure.

## Common Interview Questions

### What is `ExecutorService`?

`ExecutorService` is a Java API for running asynchronous tasks using managed threads.

It lets you submit `Runnable` or `Callable` tasks without manually creating a new thread for each task.

### Why use `ExecutorService` instead of `new Thread()`?

Because an executor can reuse threads, limit concurrency, queue tasks, return futures, and centralize shutdown.

Manual thread creation is lower-level and easier to misuse.

### What does `submit()` return?

`submit()` returns a `Future`.

The `Future` can be used to wait for completion, get a result, handle failure, or cancel the task.

### What is the difference between `shutdown()` and `shutdownNow()`?

`shutdown()` stops accepting new tasks and lets already submitted tasks finish.

`shutdownNow()` attempts to stop running tasks, usually by interrupting worker threads, and returns queued tasks that did not start.

### What happens if you forget to shut down an executor?

The application may keep running because executor worker threads may still be alive.

In server applications, executors should be owned and shut down by the application lifecycle or framework.

### Is `newCachedThreadPool()` safe as a default?

No.

It can create many threads under load.

That can increase memory use, context switching, and instability.

Use it only when you understand the workload and risk.

## Common Mistakes

### Creating a new executor for every request

This is usually bad design.

Executors are meant to be reused and lifecycle-managed.

Creating one per request can create too many threads and resource leaks.

### Forgetting shutdown

In small programs and tests, forgetting shutdown can make the JVM keep running.

In applications, unmanaged executors can leak resources.

### Using an unbounded queue without thinking

Some executor factory methods use queues that can grow very large.

If tasks arrive faster than they complete, memory usage and latency can grow badly.

Backpressure matters.

### Blocking inside a small pool

If all threads in a small pool block on slow I/O, new tasks wait.

The CPU may be mostly idle while the application is still slow.

This is a classic backend failure mode.

### Ignoring task failures

If you use `submit()` and never inspect the `Future`, exceptions can be hidden.

Production systems need clear failure handling.

## Mid/Senior Notes

At junior level, know how to submit tasks and shut down the executor.

At mid level, understand pool sizing, blocking, queueing, and failure handling.

At senior level, think in terms of capacity and ownership:

- Who owns this executor?
- How large should the pool be?
- Is the workload CPU-bound or I/O-bound?
- Is the queue bounded?
- What happens when the queue is full?
- Are timeouts enforced?
- How are failures logged or propagated?
- How does shutdown work during deployment?

Thread pools are not just a syntax feature.

They are a resource management boundary.

Bad executor configuration can take down a backend under load.

## When To Use It / When Not To Use It

Use `ExecutorService` when:

- you need to run tasks asynchronously
- you want to limit concurrency
- you want to reuse threads
- you need `Future` results
- you need controlled shutdown

Be careful when:

- tasks block for long periods
- task volume is unbounded
- failure handling is unclear
- executors are created ad hoc
- framework-managed execution already exists

In Spring applications, you often use framework-managed executors instead of creating random executors in business code.

## Quick Summary

`ExecutorService` runs tasks using managed worker threads.

It is usually better than creating raw threads manually.

You can submit `Runnable` or `Callable` tasks.

`submit()` returns a `Future`.

Always think about shutdown, queueing, pool size, blocking, and failure handling.

The hard part is not starting async work.

The hard part is controlling it.

