---
layout: default
title: Java Memory Model
parent: Intermediate And Advanced Java
grand_parent: Book
nav_order: 30
---

# Java Memory Model

## Simple Explanation

The Java Memory Model, usually called JMM, defines how threads see changes made by other threads.

It is not about heap, stack, or metaspace.

That was the JVM memory areas topic.

The Java Memory Model is about concurrency correctness.

Simple idea:

```text
If one thread writes data, when is another thread guaranteed to see it?
```

That question sounds small.

It is not.

It is the foundation behind `volatile`, `synchronized`, final fields, safe publication, and many thread-safety rules.

## Why It Exists

Modern computers are complicated.

CPUs use caches.

Compilers optimize code.

The JVM may reorder operations when it is allowed to.

Processors may also execute operations in ways that are not exactly the same as the source code order.

In single-threaded code, these optimizations are usually invisible.

In multithreaded code, they can break your assumptions.

The Java Memory Model defines rules for what behavior is allowed and what behavior is guaranteed.

Without these rules, multithreaded Java code would be almost impossible to reason about.

## The Core Problem

Imagine this class:

```java
class SharedData {
    boolean ready = false;
    int value = 0;
}
```

Thread A does this:

```java
data.value = 42;
data.ready = true;
```

Thread B does this:

```java
if (data.ready) {
    System.out.println(data.value);
}
```

Many beginners assume:

```text
If ready is true, value must be 42.
```

That assumption is not safe without proper synchronization.

Thread B might see stale data.

It might see `ready == true` but still see an old value for `value`.

This is exactly the kind of problem the Java Memory Model explains.

## Visibility

Visibility means whether one thread can see another thread's changes.

Example:

```java
class Worker {
    private boolean running = true;

    public void run() {
        while (running) {
            // work
        }
    }

    public void stop() {
        running = false;
    }
}
```

One thread runs the loop.

Another thread calls `stop()`.

Without `volatile`, `synchronized`, or another happens-before relationship, the running thread is not guaranteed to see the updated value.

It may keep looping.

This is not because Java is broken.

It is because the code has a data race.

## Reordering

Reordering means operations may be executed in a different order than they appear in source code, as long as single-threaded behavior is preserved.

Example:

```java
data.value = 42;
data.ready = true;
```

In single-threaded code, the result is clear.

In multithreaded code without synchronization, another thread may observe effects in a surprising order.

The JVM and CPU are allowed to optimize.

The Java Memory Model says which reorderings are allowed and which are forbidden.

Synchronization creates boundaries that restrict dangerous reorderings.

## Data Race

A data race happens when:

- two or more threads access the same variable
- at least one access is a write
- there is no proper happens-before relationship between the accesses

Example:

```java
class Counter {
    int count;

    void increment() {
        count++;
    }
}
```

If multiple threads call `increment()` without synchronization, there is a data race.

Data races make code hard to reason about.

The result may depend on timing, CPU caches, compiler optimizations, and scheduling.

## Happens-Before

Happens-before is one of the most important JMM concepts.

It does not simply mean "this line appears before that line in the file".

It means:

```text
If action A happens-before action B, then B is guaranteed to see the effects of A.
```

This gives visibility and ordering guarantees.

Common happens-before relationships include:

- actions in one thread happen-before later actions in the same thread
- an unlock of a monitor happens-before a later lock of the same monitor
- a write to a volatile variable happens-before a later read of that same volatile variable
- starting a thread happens-before actions inside the started thread
- actions in a thread happen-before another thread successfully returns from `join()` on that thread
- completion of a `Future` task happens-before getting its result with `get()`

You do not need to memorize every rule at junior level.

But you must understand the idea.

Happens-before is how Java makes cross-thread visibility predictable.

## synchronized And Happens-Before

`synchronized` gives mutual exclusion and visibility.

Example:

```java
class SharedValue {
    private int value;

    public synchronized void setValue(int value) {
        this.value = value;
    }

    public synchronized int getValue() {
        return value;
    }
}
```

When one thread exits `setValue()`, it releases the monitor lock.

When another thread enters `getValue()`, it acquires the same monitor lock.

The unlock happens-before the later lock.

That means the reading thread can see the written value.

Important:

This works because both methods use the same lock.

Using different locks does not protect the same state.

## volatile And Happens-Before

`volatile` gives visibility for one variable and creates happens-before relationships.

Example:

```java
class Worker {
    private volatile boolean running = true;

    public void run() {
        while (running) {
            // work
        }
    }

    public void stop() {
        running = false;
    }
}
```

When one thread writes `running = false`, another thread reading `running` is guaranteed to see the updated value according to volatile rules.

Volatile also prevents certain reorderings around that variable.

But volatile still does not make compound actions atomic.

This is still broken:

```java
private volatile int count;

public void increment() {
    count++;
}
```

Visibility is not the same as atomicity.

## final Fields

Final fields have special safe initialization guarantees when objects are constructed correctly.

Example:

```java
public final class User {
    private final String email;

    public User(String email) {
        this.email = email;
    }

    public String email() {
        return email;
    }
}
```

If an object is properly constructed and the `this` reference does not escape during construction, other threads can safely see final field values after the object is published.

This is one reason immutability works well in concurrent code.

But final is not magic.

If a final field points to a mutable object, the reference is final, not necessarily the object's internal state.

Example:

```java
public final class Team {
    private final List<String> members;

    public Team(List<String> members) {
        this.members = members;
    }
}
```

The `members` reference cannot change.

But the list itself can still be modified unless you make a defensive copy or use an immutable list.

## Safe Publication

Safe publication means making an object visible to other threads in a way that guarantees they see it correctly initialized.

Unsafe publication:

```java
class Registry {
    static User user;

    static void initialize() {
        user = new User("a@example.com");
    }
}
```

Another thread may read `Registry.user` without a proper happens-before relationship.

Better options include:

- initialize static final fields
- publish through volatile fields
- publish through synchronized methods or blocks
- publish through thread-safe collections
- publish before starting another thread
- use immutable objects correctly

Example with static final:

```java
class Registry {
    static final User USER = new User("a@example.com");
}
```

Class initialization provides strong safety guarantees.

This is one reason static final immutable constants are safe to share.

## Double-Checked Locking

Double-checked locking is a classic JMM interview topic.

Correct modern version:

```java
class ServiceHolder {
    private volatile Service service;

    public Service getService() {
        Service result = service;

        if (result == null) {
            synchronized (this) {
                result = service;
                if (result == null) {
                    result = new Service();
                    service = result;
                }
            }
        }

        return result;
    }
}
```

The `volatile` field is important.

Without it, another thread could see a reference to an object whose construction is not safely visible.

That said, do not reach for double-checked locking casually.

It is easy to get wrong.

Often simpler alternatives are better:

- eager initialization
- static holder pattern
- dependency injection
- framework-managed singleton

## Common Interview Questions

### What is the Java Memory Model?

The Java Memory Model defines the rules for how threads see writes made by other threads.

It covers visibility, ordering, happens-before relationships, and safe publication.

It is a concurrency model, not a heap/stack/metaspace diagram.

### What is visibility?

Visibility means whether one thread is guaranteed to see changes made by another thread.

Without synchronization, one thread may read stale values.

### What is happens-before?

Happens-before is a relationship that gives ordering and visibility guarantees.

If action A happens-before action B, then B can see the effects of A.

### Does source code order always define cross-thread visibility?

No.

Source code order inside one thread matters for that thread's behavior, but other threads need proper happens-before relationships to observe writes reliably.

### What creates happens-before relationships?

Common examples include:

- monitor unlock before later monitor lock on the same object
- volatile write before later volatile read of the same variable
- thread start before actions in the started thread
- actions in a thread before another thread returns from `join()`
- task completion before successful `Future.get()`

### What is safe publication?

Safe publication means sharing an object with other threads in a way that guarantees they see the object's correctly initialized state.

Examples include using static final fields, volatile references, synchronized blocks, thread-safe collections, or proper initialization before starting a thread.

### Why is volatile needed in double-checked locking?

Because it prevents unsafe visibility and reordering problems around object construction and publication.

Without volatile, another thread may observe a reference before the object's initialized state is safely visible.

## Common Mistakes

### Confusing JVM memory areas with the Java Memory Model

Heap, stack, and metaspace are JVM memory areas.

The Java Memory Model is about concurrency visibility and ordering.

These are related only indirectly.

### Thinking "it happens first in code" means another thread sees it first

That is not guaranteed without happens-before.

Cross-thread reasoning needs synchronization rules, not just source code order.

### Using volatile for everything

Volatile is useful for visibility.

It does not protect multi-step invariants.

It does not replace locks, atomics, or better design.

### Publishing mutable objects unsafely

Sharing an object before it is safely initialized can create weird bugs.

Letting `this` escape from a constructor is one example.

Bad:

```java
class Listener {
    Listener(EventBus bus) {
        bus.register(this);
    }
}
```

Another thread may use the object before construction is complete.

### Overexplaining JMM without understanding it

In interviews, do not fake deep knowledge.

A clean practical answer is better than throwing around words like "cache coherence" without being able to explain the bug.

## Mid/Senior Notes

At junior level, know that unsynchronized shared mutable state is unsafe.

At mid level, understand visibility, reordering, volatile, synchronized, and happens-before.

At senior level, connect the JMM to design:

- Can this state be immutable?
- Is the object safely published?
- What happens-before relationship makes this correct?
- Is volatile enough or do we need atomicity?
- Are multiple fields part of one invariant?
- Does the framework already provide safe lifecycle publication?
- Are we relying on timing instead of guarantees?

The strongest concurrency answer is often:

```text
I would avoid sharing mutable state here.
```

That is not weakness.

That is good design.

## When To Use It / When Not To Use It

You do not "use" the Java Memory Model directly.

You use it to reason about concurrent code.

You apply it when:

- using `volatile`
- using `synchronized`
- publishing shared objects
- designing immutable objects
- debugging stale reads
- reviewing double-checked locking
- reasoning about thread lifecycle and `join()`
- reviewing concurrent collections and futures

Do not try to manually outsmart the JVM.

Use established concurrency tools and patterns.

If correctness depends on "probably the other thread will see it", the code is broken.

## Quick Summary

The Java Memory Model defines how threads see writes from other threads.

It is about visibility, ordering, happens-before, and safe publication.

It is not the same as heap, stack, and metaspace.

Without proper synchronization, threads may see stale values or surprising ordering.

`synchronized` gives mutual exclusion and visibility.

`volatile` gives visibility and ordering for a variable, but not compound atomicity.

Final fields help with safe initialization when objects are constructed correctly.

For interviews, focus on practical correctness:

- visibility
- reordering
- happens-before
- volatile vs synchronized
- safe publication
- why unsynchronized shared mutable state is dangerous

