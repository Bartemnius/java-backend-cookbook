---
layout: default
title: Thread Lifecycle
parent: Intermediate And Advanced Java
grand_parent: Book
nav_order: 19
---

# Thread Lifecycle

## Simple Explanation

A thread is not always running.

During its life, a Java thread moves through several states.

Sometimes it is new and has not started yet.

Sometimes it is ready to run.

Sometimes it is actually running on a CPU.

Sometimes it is waiting.

Sometimes it is blocked because another thread owns a lock.

Sometimes it is finished and cannot be started again.

The thread lifecycle describes these states and the transitions between them.

## Why It Exists

The lifecycle exists because the operating system and JVM must manage many threads with limited CPU resources.

Your application may have hundreds or thousands of threads.

But your machine may have only a few CPU cores.

That means not every thread can run at the same exact moment.

Some threads must wait.

Some threads are paused.

Some threads are waiting for I/O.

Some threads are waiting for locks.

Understanding the lifecycle helps you debug problems like:

- why a thread is not doing work
- why an application is stuck
- why CPU is low but requests are still slow
- why a deadlock happened
- why calling `start()` twice fails
- why `sleep()` does not release a lock
- why `wait()` behaves differently from `sleep()`

This is not just theory.

Thread dumps, production debugging, and interview questions all use these concepts.

## Java Thread States

Java exposes thread states through the `Thread.State` enum.

The main states are:

- `NEW`
- `RUNNABLE`
- `BLOCKED`
- `WAITING`
- `TIMED_WAITING`
- `TERMINATED`

These names matter because you can see them in thread dumps.

## NEW

A thread is in the `NEW` state after the `Thread` object is created but before `start()` is called.

Example:

```java
Thread thread = new Thread(() -> {
    System.out.println("Working");
});

System.out.println(thread.getState()); // NEW
```

At this point, no new operating system thread is running your task yet.

You only created a Java `Thread` object.

The task starts only after you call `start()`.

## RUNNABLE

A thread is in the `RUNNABLE` state when it is ready to run or currently running.

This name can be confusing.

In Java, `RUNNABLE` does not always mean "running right now".

It can mean:

- the thread is actually using the CPU
- the thread is ready and waiting for the CPU scheduler

Example:

```java
Thread thread = new Thread(() -> {
    while (true) {
        // Busy work
    }
});

thread.start();

System.out.println(thread.getState()); // usually RUNNABLE
```

The JVM does not expose a separate `RUNNING` state.

That detail is handled by the JVM and operating system scheduler.

## BLOCKED

A thread is in the `BLOCKED` state when it is waiting to enter a synchronized block or synchronized method.

This happens when another thread already owns the object's monitor lock.

Example:

```java
class SharedResource {
    synchronized void work() {
        try {
            Thread.sleep(5_000);
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
        }
    }
}

SharedResource resource = new SharedResource();

Thread first = new Thread(resource::work);
Thread second = new Thread(resource::work);

first.start();
second.start();
```

If `first` enters `work()` first, it owns the lock.

`second` cannot enter the synchronized method until `first` leaves it.

During that time, `second` is `BLOCKED`.

Important:

`BLOCKED` is specifically about waiting for a monitor lock.

It is not the same as waiting for a database, HTTP call, or `sleep()`.

## WAITING

A thread is in the `WAITING` state when it waits indefinitely for another thread to do something.

Common ways to enter `WAITING` include:

- `Object.wait()` without a timeout
- `Thread.join()` without a timeout
- `LockSupport.park()`

Example:

```java
Thread worker = new Thread(() -> {
    System.out.println("Work done");
});

worker.start();
worker.join(); // current thread waits until worker terminates
```

The current thread waits until `worker` finishes.

There is no fixed timeout.

It depends on another thread making progress.

## TIMED_WAITING

A thread is in the `TIMED_WAITING` state when it waits for a limited amount of time.

Common ways to enter `TIMED_WAITING` include:

- `Thread.sleep(time)`
- `Object.wait(time)`
- `Thread.join(time)`
- `LockSupport.parkNanos(...)`
- `LockSupport.parkUntil(...)`

Example:

```java
Thread thread = new Thread(() -> {
    try {
        Thread.sleep(1_000);
    } catch (InterruptedException e) {
        Thread.currentThread().interrupt();
    }
});

thread.start();
```

While sleeping, the thread is not using CPU to execute Java code.

It is waiting until the time passes or until it is interrupted.

## TERMINATED

A thread is in the `TERMINATED` state after its `run()` method finishes.

Example:

```java
Thread thread = new Thread(() -> {
    System.out.println("Done");
});

thread.start();
thread.join();

System.out.println(thread.getState()); // TERMINATED
```

Once a thread is terminated, it cannot be started again.

This will throw `IllegalThreadStateException`:

```java
Thread thread = new Thread(() -> {
    System.out.println("Run once");
});

thread.start();
thread.join();

thread.start(); // IllegalThreadStateException
```

If you need to run another task, create another `Thread` or use an executor.

In real applications, prefer executors over manually restarting threads.

## Basic Lifecycle Flow

A simplified lifecycle looks like this:

```text
NEW
  |
  | start()
  v
RUNNABLE
  |
  | waiting for lock
  v
BLOCKED
  |
  | lock acquired
  v
RUNNABLE

RUNNABLE
  |
  | wait(), join(), park()
  v
WAITING
  |
  | notified, joined thread finished, unparked
  v
RUNNABLE

RUNNABLE
  |
  | sleep(), timed wait(), timed join()
  v
TIMED_WAITING
  |
  | timeout finished or interrupted
  v
RUNNABLE

RUNNABLE
  |
  | run() completes
  v
TERMINATED
```

This is simplified, but it is good enough for most interviews.

## `sleep()` vs `wait()`

This is a classic interview topic.

`Thread.sleep()` pauses the current thread for a period of time.

It does not release any lock the thread already owns.

Example:

```java
synchronized (lock) {
    Thread.sleep(1_000);
}
```

During that sleep, the thread still owns `lock`.

Other threads cannot enter synchronized code protected by the same lock.

`Object.wait()` is different.

It must be called while owning the object's monitor lock.

When a thread calls `wait()`, it releases that monitor lock and waits until another thread calls `notify()` or `notifyAll()`.

Example:

```java
synchronized (lock) {
    lock.wait();
}
```

This releases `lock` while waiting.

That difference matters a lot.

If you say "`sleep()` releases the lock" in an interview, that is simply wrong.

## `join()`

`join()` means:

> Wait until another thread finishes.

Example:

```java
Thread worker = new Thread(() -> {
    System.out.println("Working");
});

worker.start();
worker.join();

System.out.println("Worker finished");
```

The current thread waits until `worker` reaches `TERMINATED`.

This is useful in small examples and tests.

In production code, manually joining raw threads is less common than using executors, futures, or structured concurrency.

## Interruption

Interruption is Java's cooperative way to ask a thread to stop waiting or stop working.

It is not a brutal kill signal.

If a thread is sleeping, waiting, or joining, interruption usually causes `InterruptedException`.

Example:

```java
Thread thread = new Thread(() -> {
    try {
        Thread.sleep(10_000);
    } catch (InterruptedException e) {
        Thread.currentThread().interrupt();
        System.out.println("Interrupted");
    }
});

thread.start();
thread.interrupt();
```

Notice this line:

```java
Thread.currentThread().interrupt();
```

That restores the interrupt status.

This is important because catching `InterruptedException` clears the interrupt flag.

If your method cannot handle the interruption properly, restore the flag and let higher-level code decide what to do.

Swallowing interruptions is a bad habit.

## Basic Code Example

This example prints a few thread states:

```java
public class ThreadLifecycleDemo {
    public static void main(String[] args) throws InterruptedException {
        Thread thread = new Thread(() -> {
            try {
                Thread.sleep(1_000);
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
        });

        System.out.println(thread.getState()); // NEW

        thread.start();
        System.out.println(thread.getState()); // RUNNABLE or TIMED_WAITING

        Thread.sleep(100);
        System.out.println(thread.getState()); // usually TIMED_WAITING

        thread.join();
        System.out.println(thread.getState()); // TERMINATED
    }
}
```

Do not build serious logic around exact observed states in tiny demos.

Thread scheduling is timing-dependent.

The result can vary between machines and runs.

Use this example to understand the concept, not to write fragile tests.

## Common Interview Questions

### What are the main Java thread states?

The main Java thread states are `NEW`, `RUNNABLE`, `BLOCKED`, `WAITING`, `TIMED_WAITING`, and `TERMINATED`.

They are defined in the `Thread.State` enum.

### What is the difference between `NEW` and `RUNNABLE`?

`NEW` means a `Thread` object exists, but `start()` has not been called yet.

`RUNNABLE` means the thread has been started and is either ready to run or currently running.

### Does Java have a `RUNNING` thread state?

No.

Java uses `RUNNABLE` for both "ready to run" and "currently running".

The exact CPU scheduling details are handled by the JVM and operating system.

### What does `BLOCKED` mean?

`BLOCKED` means a thread is waiting to enter a synchronized block or synchronized method because another thread owns the monitor lock.

It does not simply mean "the thread is waiting for anything".

### What is the difference between `WAITING` and `TIMED_WAITING`?

`WAITING` means a thread waits without a timeout.

For example, `join()` without a timeout.

`TIMED_WAITING` means a thread waits for a limited amount of time.

For example, `sleep(1000)` or `join(1000)`.

### Can a terminated thread be restarted?

No.

A terminated thread cannot be started again.

Calling `start()` on the same `Thread` object again throws `IllegalThreadStateException`.

Create a new thread or use an executor.

### Does `sleep()` release locks?

No.

`sleep()` pauses the current thread but does not release locks.

If a thread sleeps inside a synchronized block, it still owns that monitor lock.

### Does `wait()` release locks?

Yes, but specifically the monitor lock of the object on which `wait()` is called.

`wait()` must be called from synchronized code.

When the thread waits, it releases that monitor and later tries to reacquire it before continuing.

### What does `join()` do?

`join()` makes the current thread wait until another thread finishes.

It is often used in demos, tests, and low-level thread coordination.

### What is interruption?

Interruption is a cooperative mechanism for asking a thread to stop waiting or stop doing work.

It does not forcibly kill a thread.

Code must handle interruption correctly.

## Common Mistakes

### Thinking `RUNNABLE` always means currently running

This is wrong.

In Java, `RUNNABLE` also includes threads that are ready to run but waiting for CPU time.

There is no separate Java-level `RUNNING` state.

### Confusing `BLOCKED` with all kinds of waiting

`BLOCKED` has a specific meaning.

It means waiting for a monitor lock.

A thread sleeping is usually `TIMED_WAITING`, not `BLOCKED`.

A thread waiting on `join()` is usually `WAITING` or `TIMED_WAITING`.

### Calling `run()` instead of `start()`

Calling `run()` directly does not create a new thread.

It is just a normal method call on the current thread.

Call `start()` to start a new thread.

### Trying to restart the same thread

A `Thread` object can be started only once.

After it terminates, create a new thread or use a higher-level API.

If your design requires restarting raw threads manually, the design is probably too low-level.

### Swallowing `InterruptedException`

This is a serious mistake.

Bad example:

```java
try {
    Thread.sleep(1_000);
} catch (InterruptedException e) {
    // ignore
}
```

This hides the signal that someone asked the thread to stop waiting.

A better basic pattern is:

```java
try {
    Thread.sleep(1_000);
} catch (InterruptedException e) {
    Thread.currentThread().interrupt();
}
```

Real handling depends on context, but silently ignoring interruption is weak code.

## Mid/Senior Notes

At junior level, you should know the names of the states and basic transitions.

At mid level, you should understand what those states mean when debugging.

At senior level, you should connect thread states to production symptoms.

Examples:

- many `BLOCKED` threads may suggest lock contention
- many `WAITING` threads may be normal in pools or may suggest coordination problems
- many `TIMED_WAITING` threads may be sleeping, waiting on timeouts, or blocked on timed operations
- high request latency with low CPU may suggest threads are waiting on I/O, locks, or external systems
- a deadlock usually appears as threads permanently waiting for locks owned by each other

Thread dumps are one of the most useful tools here.

A thread dump shows what threads exist, what state they are in, and often what code they are executing.

You do not need to memorize every internal detail.

But you should be able to look at thread states and make reasonable debugging hypotheses.

That is the difference between theory and engineering.

## When To Use It / When Not To Use It

You do not usually choose to "use the thread lifecycle" directly.

You use this knowledge when:

- debugging stuck applications
- reading thread dumps
- explaining concurrency in interviews
- writing low-level thread examples
- understanding executors, futures, and locks
- reasoning about blocking behavior

You should not overuse raw `Thread` objects in production code.

For most backend work, prefer higher-level APIs:

- `ExecutorService`
- `CompletableFuture`
- scheduled executors
- framework-managed task executors
- virtual threads where they fit the problem

The lifecycle still matters because those higher-level tools use threads underneath.

## Quick Summary

A Java thread moves through states during its life.

The main states are `NEW`, `RUNNABLE`, `BLOCKED`, `WAITING`, `TIMED_WAITING`, and `TERMINATED`.

`RUNNABLE` means ready or running, not always actively using CPU.

`BLOCKED` means waiting for a monitor lock.

`WAITING` means waiting indefinitely.

`TIMED_WAITING` means waiting with a timeout.

`TERMINATED` means the thread finished and cannot be restarted.

For interviews, understand especially:

- `start()` vs `run()`
- `sleep()` vs `wait()`
- `join()`
- interruption
- why thread states matter in debugging
