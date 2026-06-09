---
layout: default
title: Multithreading Basics
parent: Intermediate And Advanced Java
grand_parent: Book
nav_order: 18
---

# Multithreading Basics

## Simple Explanation

Multithreading means that a program can do more than one thing at the same time.

In Java, a thread is like a separate worker inside the same application.

One thread can handle one task.

Another thread can handle another task.

Example:

- one thread handles an HTTP request
- another thread reads data from a database
- another thread writes logs
- another thread sends an email

The important part is this:

Threads share the same application memory.

That is powerful.

That is also dangerous.

If two threads read and change the same data at the same time, the result can become incorrect.

## A Simple Mental Model

Imagine a kitchen.

One cook is simple.

The cook knows what they are doing, in what order, and nothing changes behind their back.

Now imagine five cooks using the same table, the same knife, the same pan, and the same list of orders.

The kitchen can become faster.

But it can also become messy:

- two cooks can grab the same ingredient
- one cook can move something while another cook is using it
- someone can read an old order
- two cooks can update the same checklist and lose one update

That is multithreading.

More workers can improve throughput.

But shared state needs discipline.

## Why It Exists

Multithreading exists because many applications need to do many things concurrently.

A backend application often handles many users at the same time.

If one user sends a request, the whole application should not stop for everyone else.

Threads help with:

- handling many requests
- waiting for slow I/O
- running background tasks
- parallelizing CPU-heavy work
- keeping an application responsive

But threads are not magic.

They do not automatically make code faster.

They add coordination cost.

They add debugging complexity.

They can create bugs that happen only sometimes.

That is why interviewers ask about them.

They want to know whether you understand the danger behind code that looks simple.

## Concurrency vs Parallelism

These two words are often mixed together.

They are related, but not the same.

Concurrency means dealing with many tasks at the same time.

Parallelism means literally running many tasks at the same time.

Example:

If one person switches between answering emails and cooking dinner, that is concurrency.

They are handling multiple tasks, but not physically doing both at the exact same moment.

If two people work at the same time, one answering emails and one cooking dinner, that is parallelism.

In Java:

- concurrency is about structuring work so multiple tasks can progress
- parallelism is about running work on multiple CPU cores

An application can be concurrent without being parallel.

For example, one CPU core can switch quickly between many threads.

It feels like many things happen at once, but only one thread runs at a tiny moment.

## Basic Code Example

You can create a simple thread like this:

```java
Thread thread = new Thread(() -> {
    System.out.println("Work is running in another thread");
});

thread.start();
```

The important method is `start()`.

`start()` tells the JVM:

> Create a new thread and run this task there.

Do not call `run()` directly if you want a new thread.

This code does not start a new thread:

```java
Thread thread = new Thread(() -> {
    System.out.println("This runs in the current thread");
});

thread.run();
```

Calling `run()` is just a normal method call.

It runs on the current thread.

This is a common interview trap.

## Thread Names

Thread names help during debugging.

```java
Thread thread = new Thread(() -> {
    System.out.println(Thread.currentThread().getName());
});

thread.setName("email-sender-thread");
thread.start();
```

In real applications, thread names are useful in logs.

If a production issue happens, logs from different threads can be mixed together.

A good thread name helps you understand what was running.

## Shared State Problem

The biggest danger in multithreading is shared mutable state.

Shared means many threads can access it.

Mutable means it can change.

Example:

```java
class Counter {
    private int value = 0;

    public void increment() {
        value++;
    }

    public int getValue() {
        return value;
    }
}
```

This looks simple.

But `value++` is not really one operation.

It is more like:

1. read the current value
2. add one
3. write the new value

If two threads do this at the same time, they can both read the same old value.

Then both write the same new value.

One increment is lost.

This is called a race condition.

We will discuss race conditions deeply later.

For now, remember this:

Code can be correct with one thread and broken with many threads.

## Why Bugs Are Hard To Find

Multithreading bugs are painful because they often depend on timing.

The same code can work 1000 times and fail once.

Then you add a log line and the bug disappears.

Why?

Because logging changes timing.

This is why thread bugs are not like normal syntax errors.

They are not always visible.

They often appear under load, on production, or during stress tests.

## Blocking

A thread is blocked when it waits and cannot continue.

Examples:

- waiting for a database response
- waiting for an HTTP API
- waiting for a file
- waiting for a lock
- sleeping

Blocking is not always bad.

But it matters.

If all request-handling threads are blocked, your application stops serving new requests.

This is why backend developers should understand thread pools.

A thread pool has a limited number of threads.

If all threads are busy or blocked, new work must wait.

This is one reason why a slow database can make the whole backend feel dead.

## CPU Work vs I/O Work

Not all tasks are the same.

CPU-heavy work uses the processor.

Examples:

- calculations
- parsing large files
- compression
- encryption
- image processing

I/O-heavy work mostly waits.

Examples:

- database calls
- HTTP calls
- reading files
- writing files
- waiting for external systems

This distinction matters because thread strategy depends on task type.

For CPU-heavy work, adding too many threads can make things worse.

The CPU can only do so much at once.

Too many threads create context switching overhead.

For I/O-heavy work, more concurrency can help because many threads are waiting most of the time.

This is also why Java virtual threads became important.

They make blocking I/O easier to scale, but they do not remove the need to understand concurrency.

We will cover virtual threads later.

## Common Interview Questions

### What is a thread?

A thread is an independent path of execution inside a process.

In Java, multiple threads can run inside the same application and share the same heap memory.

That sharing is useful because threads can work with the same objects.

But it is dangerous when multiple threads modify the same data without coordination.

### What is multithreading?

Multithreading is the ability of an application to use multiple threads.

It allows an application to handle multiple tasks concurrently.

For example, a backend server can process many HTTP requests at the same time instead of handling only one request at once.

### Does multithreading always improve performance?

No.

Multithreading can improve throughput, especially when tasks wait for I/O.

But it can also make performance worse if:

- there are too many threads
- threads fight for the same lock
- tasks are CPU-heavy and exceed available CPU cores
- context switching becomes expensive
- shared state causes synchronization overhead

More threads do not automatically mean faster code.

### What is the difference between concurrency and parallelism?

Concurrency means managing many tasks at the same time.

Parallelism means executing many tasks at the exact same time.

A single-core CPU can run concurrent code by switching between tasks.

Parallel execution needs multiple cores or multiple processors.

### What is shared mutable state?

Shared mutable state means data that:

- can be accessed by multiple threads
- can be changed

This is the most common source of concurrency bugs.

If multiple threads change the same object without proper coordination, the final result may be incorrect.

### Why is `start()` different from `run()`?

`start()` creates a new thread and runs the task on that new thread.

`run()` is just a normal method call.

If you call `run()` directly, the code runs on the current thread.

That means no new thread is created.

### What is a race condition?

A race condition happens when the result of code depends on the timing of multiple threads.

For example, two threads can read the same counter value, both increment it, and both write the same result.

One update is lost.

The program looks correct, but the final value is wrong.

### What is blocking?

Blocking means a thread is waiting and cannot continue.

It may wait for a database, network call, file, sleep, or lock.

Blocking is normal in backend applications, but too much blocking can exhaust thread pools and make the application stop responding.

## Common Mistakes

### Thinking that more threads always means better performance

This is wrong.

Threads are not free.

They use memory.

They need scheduling.

They can block each other.

They can create context switching overhead.

Use threads to solve a real concurrency problem, not because it sounds advanced.

### Creating threads manually everywhere

This is usually a bad idea.

In real backend applications, you normally do not create raw threads for every task.

You use higher-level tools:

- `ExecutorService`
- thread pools
- `CompletableFuture`
- framework-managed executors
- virtual threads in suitable cases

Raw threads are useful for learning.

They are rarely the cleanest production choice.

### Ignoring shared state

Many developers understand how to start a thread but ignore what data the thread touches.

That is the dangerous part.

The real question is not only:

> Can I run this in another thread?

The better question is:

> What shared data can this thread read or change?

### Blocking inside limited thread pools

This is a common backend problem.

If you block all threads in a small pool, new tasks cannot run.

Example:

- pool has 10 threads
- 10 tasks wait for a slow external API
- new requests arrive
- there are no free threads
- everything waits

This can look like the whole application is broken, even if the CPU is almost idle.

## Mid/Senior Notes

At mid and senior level, interviewers usually do not care whether you can write `new Thread(...)`.

That is basic.

They care whether you understand tradeoffs.

Important questions:

- Is this task CPU-bound or I/O-bound?
- What data is shared?
- Can the shared data be immutable?
- Do we need synchronization?
- Could a queue or executor make this simpler?
- What happens when tasks are slower than expected?
- What happens when the thread pool is full?
- Can this code fail only under load?

Good backend code often avoids complicated shared-state concurrency.

Instead, it uses simpler patterns:

- immutable objects
- request-scoped data
- queues
- database transactions
- idempotent operations
- thread-safe collections
- well-sized executors
- clear ownership of mutable state

The best concurrency bug is the one you never create.

## Quick Summary

Multithreading lets Java applications handle many tasks concurrently.

Threads share memory, which makes them powerful and dangerous.

The hardest part is not starting a thread.

The hardest part is controlling shared mutable state.

More threads do not automatically mean better performance.

For interviews, understand:

- what a thread is
- concurrency vs parallelism
- `start()` vs `run()`
- shared mutable state
- race conditions
- blocking
- CPU-bound vs I/O-bound work

This foundation will make the next topics much easier.
