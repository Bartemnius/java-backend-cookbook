---
layout: default
title: Book Checklist
nav_order: 99
nav_exclude: true
search_exclude: true
---

# Java Developer Interview Book - 2026 Checklist

## Purpose

This document is the working checklist for a full English PDF book/course that prepares a Java backend developer for technical interviews.

The final material should be:

- written in simple English,
- practical and interview-focused,
- accurate for 2026,
- structured like a book,
- rich in examples,
- honest about what matters at junior, mid, and senior levels,
- useful both for learning and for revision before interviews.

The goal is not to create a random list of facts. The goal is to build understanding: why things exist, how they work under the hood, when they are useful, and what interviewers usually expect.

## Standard Topic Format

Every important topic should follow this format:

```text
Topic Name

1. Simple Explanation
   Explain the concept in very simple language.

2. Why It Exists
   Explain the problem this concept solves.

3. Basic Code Example
   Show a small Java or related code example.

4. Common Interview Questions
   Add frequently asked questions with clear answers.

5. Common Mistakes
   Explain what developers often misunderstand.

6. Mid/Senior Notes
   Explain deeper tradeoffs, production usage, design implications,
   performance concerns, and alternatives.

7. When To Use It / When Not To Use It
   Explain practical decision-making.

8. Quick Summary
   Short revision block.
```

Example:

```text
Inheritance

Simple explanation:
Inheritance means that one class receives behavior and state from another class.
It is like saying: "A Dog is an Animal, so it can reuse Animal behavior."

Code example:
class Animal {
    void eat() {
        System.out.println("Eating");
    }
}

class Dog extends Animal {
    void bark() {
        System.out.println("Barking");
    }
}

Mid/Senior:
Inheritance is useful, but in many real applications composition is preferred.
Composition means building objects from smaller objects instead of creating deep
parent-child hierarchies. It usually creates code that is easier to test, change,
and extend.
```

## Book Structure

### 1. Introduction

- [x] What this book is about
- [x] Who this book is for
- [x] How to use this book
- [x] What a Java backend interview usually tests
- [x] Junior vs Mid vs Senior expectations
- [x] How to study with this book
- [x] How to practice answering questions out loud
- [x] How to avoid passive learning

### 2. Java Fundamentals

- [x] How Java code runs: source code, bytecode, JVM
- [x] JDK vs JRE vs JVM
- [x] Java project structure
- [x] Packages and imports
- [x] Classes and objects
- [x] Fields, methods, constructors
- [x] Access modifiers: public, private, protected, package-private
- [x] Static vs instance members
- [x] Stack vs heap basics
- [x] Primitive types
- [x] Wrapper classes
- [x] Autoboxing and unboxing
- [x] Strings and immutability
- [x] StringBuilder and StringBuffer
- [x] Arrays
- [x] var keyword
- [x] final keyword
- [x] enum
- [x] Records
- [x] Sealed classes
- [x] Object class methods: equals, hashCode, toString
- [x] Method overloading
- [x] Method overriding
- [x] Inheritance
- [x] Composition over inheritance
- [x] Polymorphism
- [x] Abstraction
- [x] Interfaces
- [x] Abstract classes
- [x] Exceptions: checked vs unchecked
- [x] try/catch/finally
- [x] try-with-resources
- [x] Generics
- [x] Collections overview
- [x] List, Set, Map
- [x] ArrayList vs LinkedList
- [x] HashMap internals
- [x] HashSet internals
- [x] Comparable vs Comparator
- [x] Iterators
- [ ] Streams basics
- [ ] Optional
- [ ] Date and time API
- [ ] Basic input/output
- [ ] Serialization basics

### 3. Intermediate and Advanced Java

- [ ] JVM architecture
- [ ] Class loading
- [ ] Memory model overview
- [ ] Garbage collection basics
- [ ] Common garbage collectors
- [ ] JVM tuning basics
- [ ] Reflection
- [ ] Annotations
- [ ] Lambdas
- [ ] Functional interfaces
- [ ] Stream API deeper usage
- [ ] Parallel streams
- [ ] Immutability in real applications
- [ ] Defensive copying
- [ ] SOLID principles in Java
- [ ] Common design patterns in Java
- [ ] Dependency injection concept without Spring
- [ ] Multithreading basics
- [ ] Thread lifecycle
- [ ] Runnable vs Callable
- [ ] ExecutorService
- [ ] Future and CompletableFuture
- [ ] synchronized
- [ ] volatile
- [ ] Locks
- [ ] Race conditions
- [ ] Deadlocks
- [ ] Thread safety
- [ ] Concurrent collections
- [ ] Java Memory Model
- [ ] Virtual threads
- [ ] Structured concurrency basics
- [ ] Performance profiling basics

### 4. Spring and Spring Boot

- [ ] What Spring solves
- [ ] Spring vs Spring Boot
- [ ] Inversion of Control
- [ ] Dependency Injection
- [ ] ApplicationContext
- [ ] Beans
- [ ] Bean scopes
- [ ] Component scanning
- [ ] Common annotations: Component, Service, Repository, Controller
- [ ] Configuration classes
- [ ] Auto-configuration
- [ ] Properties and profiles
- [ ] REST controllers
- [ ] Request mapping
- [ ] DTOs
- [ ] Validation
- [ ] Exception handling
- [ ] Filters and interceptors
- [ ] Spring Security overview
- [ ] Authentication vs authorization
- [ ] Sessions vs JWT
- [ ] OAuth2 and OpenID Connect basics
- [ ] Password hashing
- [ ] CORS
- [ ] Transactions
- [ ] @Transactional internals
- [ ] Proxies in Spring
- [ ] AOP basics
- [ ] Spring Data JPA
- [ ] Repositories
- [ ] Entity lifecycle
- [ ] Lazy vs eager loading
- [ ] N+1 problem
- [ ] Testing Spring applications
- [ ] Unit tests vs integration tests
- [ ] Testcontainers
- [ ] Actuator
- [ ] Observability basics

### 5. Algorithms and Problem Solving

- [ ] Big O notation
- [ ] Time complexity
- [ ] Space complexity
- [ ] Arrays
- [ ] Strings
- [ ] Hash maps
- [ ] Sets
- [ ] Two pointers
- [ ] Sliding window
- [ ] Prefix sums
- [ ] Binary search
- [ ] Recursion
- [ ] Backtracking
- [ ] Divide and conquer
- [ ] Sorting
- [ ] Stacks
- [ ] Queues
- [ ] Linked lists
- [ ] Trees
- [ ] Binary search trees
- [ ] Heaps
- [ ] Graphs
- [ ] BFS
- [ ] DFS
- [ ] Dynamic programming basics
- [ ] Greedy algorithms
- [ ] Interview problem-solving method
- [ ] How to explain a solution clearly
- [ ] 2 fully explained easy problems
- [ ] 2 fully explained medium problems
- [ ] 2 fully explained harder medium problems

### 6. Databases

- [ ] Why databases exist
- [ ] SQL vs NoSQL
- [ ] Relational model
- [ ] Tables, rows, columns
- [ ] Primary keys
- [ ] Foreign keys
- [ ] Indexes
- [ ] Joins
- [ ] Normalization
- [ ] Denormalization
- [ ] Transactions
- [ ] ACID
- [ ] Isolation levels
- [ ] Locks
- [ ] Deadlocks in databases
- [ ] Query execution plans
- [ ] Pagination
- [ ] Optimistic locking
- [ ] Pessimistic locking
- [ ] Database migrations
- [ ] PostgreSQL basics
- [ ] MySQL basics
- [ ] MongoDB basics
- [ ] Redis basics
- [ ] When to use Redis
- [ ] Caching strategies
- [ ] Eventual consistency
- [ ] CAP theorem basics
- [ ] Common database interview questions

### 7. Linux and Command Line Basics

- [ ] Why backend developers need Linux
- [ ] File system basics
- [ ] Paths
- [ ] Common commands: ls, cd, pwd, mkdir, rm, cp, mv
- [ ] Reading files: cat, less, head, tail
- [ ] Searching: grep, find
- [ ] Processes
- [ ] ps, top, kill
- [ ] Environment variables
- [ ] Permissions
- [ ] chmod
- [ ] ssh basics
- [ ] curl
- [ ] logs
- [ ] systemd basics
- [ ] Basic shell scripting

### 8. Frontend Basics for Backend Developers

- [ ] Why backend developers should understand frontend basics
- [ ] HTTP from browser perspective
- [ ] HTML basics
- [ ] CSS basics
- [ ] JavaScript basics
- [ ] TypeScript basics
- [ ] JSON
- [ ] Fetch API
- [ ] REST API communication
- [ ] Forms
- [ ] Browser storage
- [ ] Cookies
- [ ] CORS from frontend perspective
- [ ] SPA basics
- [ ] Backend-for-Frontend concept
- [ ] API contracts

### 9. Networking and Web Fundamentals

- [ ] What happens when you enter a URL in the browser
- [ ] DNS
- [ ] IP addresses
- [ ] Ports
- [ ] TCP
- [ ] UDP
- [ ] HTTP
- [ ] HTTPS
- [ ] TLS/SSL
- [ ] Certificates
- [ ] Request and response structure
- [ ] Headers
- [ ] Status codes
- [ ] Cookies
- [ ] Sessions
- [ ] REST
- [ ] Idempotency
- [ ] Rate limiting
- [ ] Load balancing
- [ ] Reverse proxy
- [ ] Ingress
- [ ] Egress
- [ ] CDN

### 10. Architecture, Cloud, and DevOps Basics

- [ ] Monolith
- [ ] Modular monolith
- [ ] Microservices
- [ ] When microservices are a bad idea
- [ ] Clean architecture basics
- [ ] Hexagonal architecture basics
- [ ] Layered architecture
- [ ] Domain-driven design basics
- [ ] Synchronous vs asynchronous communication
- [ ] Message queues
- [ ] Kafka basics
- [ ] RabbitMQ basics
- [ ] Event-driven architecture
- [ ] Caching
- [ ] Distributed systems basics
- [ ] Scalability
- [ ] Availability
- [ ] Reliability
- [ ] Observability
- [ ] Logging
- [ ] Metrics
- [ ] Tracing
- [ ] Docker
- [ ] Dockerfile
- [ ] Docker Compose
- [ ] Kubernetes basics
- [ ] Pods
- [ ] Services
- [ ] Deployments
- [ ] ConfigMaps and Secrets
- [ ] Ingress in Kubernetes
- [ ] AWS basics
- [ ] EC2
- [ ] S3
- [ ] RDS
- [ ] IAM
- [ ] Load balancers
- [ ] CI/CD basics

### 11. Interview Question Bank

- [ ] Java fundamentals questions
- [ ] Object-oriented programming questions
- [ ] Collections questions
- [ ] JVM questions
- [ ] Multithreading questions
- [ ] Spring questions
- [ ] Spring Security questions
- [ ] Database questions
- [ ] SQL questions
- [ ] NoSQL questions
- [ ] Architecture questions
- [ ] Cloud and DevOps questions
- [ ] Docker and Kubernetes questions
- [ ] Linux questions
- [ ] Networking questions
- [ ] Frontend basics questions
- [ ] Behavioral questions for developers
- [ ] 300 total questions with clear answers

### 12. Recommended LeetCode Practice List

- [ ] How to train LeetCode without wasting time
- [ ] Best easy problems for Java interviews
- [ ] Best medium problems for backend interviews
- [ ] Problems grouped by pattern
- [ ] Arrays and hash maps
- [ ] Two pointers
- [ ] Sliding window
- [ ] Binary search
- [ ] Trees
- [ ] Graphs
- [ ] Dynamic programming
- [ ] Backtracking
- [ ] How to review failed attempts
- [ ] How to explain solutions during interviews

## Production Plan

### Phase 1 - Structure

- [x] Create the master checklist
- [ ] Decide final chapter order
- [ ] Decide writing style rules
- [ ] Decide PDF/book formatting rules
- [ ] Create chapter template
- [ ] Create question template

### Phase 2 - Content Drafting

- [x] Draft Introduction
- [ ] Draft Java Fundamentals
- [ ] Draft Advanced Java
- [ ] Draft Spring
- [ ] Draft Algorithms
- [ ] Draft Databases
- [ ] Draft Linux
- [ ] Draft Frontend Basics
- [ ] Draft Networking
- [ ] Draft Architecture and DevOps
- [ ] Draft Question Bank
- [ ] Draft LeetCode List

### Phase 3 - Review and Expansion

- [ ] Review technical accuracy
- [ ] Add missing interview topics
- [ ] Improve weak explanations
- [ ] Add code examples
- [ ] Add diagrams
- [ ] Add practical scenarios
- [ ] Add junior/mid/senior expectations
- [ ] Add common mistakes

### Phase 4 - PDF Production

- [ ] Choose PDF generation tool
- [ ] Create book layout
- [ ] Add table of contents
- [ ] Add page numbers
- [ ] Add code block styling
- [ ] Add diagrams
- [ ] Export draft PDF
- [ ] Review PDF readability
- [ ] Export final PDF

## Writing Rules

- Use simple English.
- Prefer short sentences.
- Explain jargon before using it heavily.
- Every topic must answer: "Why should I care?"
- Every theoretical concept should have at least one practical example.
- Interview answers should sound natural, not memorized.
- Avoid fake certainty. If something depends on context, say so.
- For 2026 accuracy, verify version-sensitive topics before finalizing them.
- Prefer official documentation for Java, Spring, PostgreSQL, Docker, Kubernetes, and AWS.

## Current Decisions

- Final PDF language: English.
- Working discussion language: Polish is fine.
- Main audience: Java backend developer preparing for interviews.
- Level range: junior to mid, with senior notes where useful.
- Style: simple explanations first, deeper notes later.

## Open Decisions

- [ ] Should Linux be before databases, before Spring, or stay as a support chapter?
- [ ] Should networking be before Spring Security?
- [ ] Should architecture come before Docker/Kubernetes/AWS, or should those tools be explained first?
- [ ] Should question bank be one big chapter or placed after each chapter?
- [ ] Should the final PDF be generated from Markdown, AsciiDoc, or LaTeX?
- [ ] Should code examples use Java 21 as the default baseline?
