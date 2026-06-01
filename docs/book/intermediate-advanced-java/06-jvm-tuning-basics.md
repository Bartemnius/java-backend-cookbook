---
layout: default
title: JVM Tuning Basics
parent: Intermediate And Advanced Java
grand_parent: Book
nav_order: 6
---

# JVM Tuning Basics

## Simple Explanation

JVM tuning means changing JVM settings to improve how an application runs.

Common tuning areas:

```text
heap size
garbage collector
GC logging
thread stack size
container memory behavior
startup options
```

But tuning should not be random.

Good tuning starts with measurement.

## Why It Exists

Default JVM settings are often good, but not always perfect for every application.

Your application may have:

- memory limits in Docker/Kubernetes,
- latency requirements,
- high allocation rate,
- large caches,
- many threads,
- batch workload,
- low startup time requirements.

JVM tuning helps adapt runtime behavior to the workload.

## Heap Size

The heap stores objects.

Common flags:

```text
-Xms -> initial heap size
-Xmx -> maximum heap size
```

Example:

```bash
java -Xms512m -Xmx2g -jar app.jar
```

This starts with 512 MB heap and allows up to 2 GB.

If heap is too small, GC may run too often.

If heap is too large, pauses may be longer and memory may be wasted.

## GC Logging

GC logs help you understand garbage collection behavior.

They can show:

- how often GC runs,
- how long pauses take,
- how heap changes over time,
- whether memory is being reclaimed.

Modern Java uses unified logging:

```bash
java -Xlog:gc* -jar app.jar
```

You do not need to memorize all logging options for interviews.

You should know that GC logs are one of the first tools for GC investigation.

## Container Awareness

Many Java applications run in Docker or Kubernetes.

The JVM must respect container memory limits.

Modern JVMs are container-aware, but you still need to think about:

- heap memory,
- metaspace,
- thread stacks,
- direct buffers,
- native memory,
- container memory limit.

Important:

```text
Xmx is not the whole memory used by the process.
```

The JVM uses memory outside the Java heap too.

## Basic Tuning Process

Do not start with flags.

Start with questions:

```text
What problem are we solving?
High latency?
OutOfMemoryError?
Too much CPU?
Slow startup?
Too many GC pauses?
```

Then collect data:

- application metrics,
- memory usage,
- GC logs,
- thread dumps,
- heap dumps,
- profiling data.

Then change one thing at a time.

## Common Interview Questions

### What are `-Xms` and `-Xmx`?

Good answer:

```text
-Xms sets the initial Java heap size. -Xmx sets the maximum Java heap size.
They control heap memory, not the entire process memory.
```

### What is the first step in JVM tuning?

Measurement.

Good answer:

```text
First I identify the problem and collect data: metrics, GC logs, heap usage,
thread dumps, or profiling results. I avoid changing JVM flags blindly.
```

### Does increasing heap always improve performance?

No.

A larger heap can reduce GC frequency, but it may increase pause times and memory usage.

It depends on the workload and collector.

### Is heap the only memory used by a Java process?

No.

The process also uses memory for metaspace, thread stacks, direct buffers, JIT code cache, native libraries, and other native memory.

## Common Mistakes

Mistake 1: Tuning without metrics.

This is guessing.

Mistake 2: Setting `-Xmx` equal to the container memory limit.

Bad idea.

The JVM needs non-heap memory too.

Mistake 3: Changing many flags at once.

Then you do not know which change helped or hurt.

Mistake 4: Treating GC tuning as the first solution.

Sometimes the real problem is application code:

- loading too much data,
- unbounded cache,
- bad query,
- too many temporary objects,
- memory leak.

## Mid/Senior Notes

Practical JVM tuning is mostly investigation.

Useful tools and signals:

- GC logs,
- heap dump,
- thread dump,
- Java Flight Recorder,
- metrics from Actuator/Micrometer,
- container memory metrics,
- allocation profiling.

In Spring Boot apps, observability helps a lot:

```text
jvm.memory.used
jvm.gc.pause
jvm.threads.live
process.cpu.usage
```

Senior-level answer:

```text
I do not tune the JVM blindly. I first identify the symptom, collect metrics and
logs, then change one variable at a time. I also remember that heap is only part
of process memory, especially in containers.
```

## Quick Summary

JVM tuning changes runtime behavior.

`-Xms` controls initial heap.

`-Xmx` controls maximum heap.

Heap is not total process memory.

Measure before tuning.

Use GC logs and metrics to understand problems.

Change one thing at a time.
