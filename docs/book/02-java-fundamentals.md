---
layout: default
title: Java Fundamentals
parent: Book
nav_order: 2
---

# Java Fundamentals

Java fundamentals are the part of the language that interviewers expect you to understand without panic.

This does not mean you need to remember every small detail from the Java Language Specification. That would be useless for most interviews.

But you should understand how Java code becomes a running program, how objects work, how memory is roughly organized, how collections behave, and how the language encourages certain design choices.

This chapter starts from the ground level.

## How Java Code Runs: Source Code, Bytecode, JVM

### Simple Explanation

Java code does not usually run directly on your operating system.

First, you write source code in a `.java` file.

Then the Java compiler turns that source code into bytecode, stored in a `.class` file.

Then the JVM runs that bytecode.

The flow looks like this:

```text
Hello.java  ->  javac compiler  ->  Hello.class  ->  JVM  ->  running program
```

Think about it like this:

- `.java` is what humans write,
- `.class` is what the compiler produces,
- JVM is the machine that understands and runs `.class` files.

This is one of the reasons Java is portable.

The same bytecode can run on different operating systems, as long as there is a compatible JVM installed there.

That is the idea behind the classic phrase:

```text
Write once, run anywhere.
```

It does not mean every application magically works everywhere without configuration. Real applications still depend on files, networks, operating system behavior, environment variables, containers, and external services.

But the core Java bytecode model makes Java much more portable than languages that compile directly into one platform-specific binary by default.

### Why It Exists

Java was designed to solve a practical problem: developers wanted to write code once and run it on many machines.

Without the JVM model, you often need to compile a separate version of your program for each target platform.

Java adds a middle layer:

```text
Java source code -> platform-independent bytecode -> platform-specific JVM
```

The JVM hides many operating system differences from your application.

This is why the JVM is so important. It is not only "the thing that runs Java." It is the runtime environment that manages execution, memory, garbage collection, class loading, JIT compilation, threads, and many other low-level details.

### Basic Code Example

Create a file called `Hello.java`:

```java
public class Hello {
    public static void main(String[] args) {
        System.out.println("Hello, Java");
    }
}
```

Compile it:

```bash
javac Hello.java
```

This creates:

```text
Hello.class
```

Run it:

```bash
java Hello
```

Output:

```text
Hello, Java
```

Important detail:

You run `java Hello`, not `java Hello.class`.

You give the JVM the class name, not the file name.

### What Happens Step By Step

When you run this program, a simplified version of the process looks like this:

1. You write `Hello.java`.
2. `javac` checks the code and compiles it.
3. The compiler creates `Hello.class`.
4. The JVM starts.
5. The JVM loads the `Hello` class.
6. The JVM looks for the `main` method.
7. The JVM executes the bytecode.
8. The program prints text to the console.

This is simplified, but it is enough for most interviews.

### Common Interview Questions

#### What is bytecode?

Bytecode is the compiled form of Java source code.

It is not normal machine code for Windows, Linux, or macOS. It is an intermediate format that the JVM understands.

Good interview answer:

```text
Bytecode is the output of Java compilation. The compiler turns `.java` files into
`.class` files containing bytecode. The JVM then executes that bytecode, which is
one reason Java programs can run on different platforms with compatible JVMs.
```

#### Why is Java called platform-independent?

Java is called platform-independent because Java source code is compiled into bytecode, and bytecode can run on any platform that has a compatible JVM.

The JVM itself is platform-specific, but the bytecode is not tied to one operating system in the same direct way as a native executable.

#### Is Java interpreted or compiled?

Java uses both compilation and runtime execution techniques.

First, Java source code is compiled by `javac` into bytecode.

Then the JVM runs that bytecode. Modern JVMs also use JIT compilation, which means frequently executed bytecode can be compiled into native machine code at runtime for better performance.

Good interview answer:

```text
Java is compiled to bytecode first. Then the JVM executes that bytecode.
Modern JVMs also use JIT compilation, so hot parts of the program can be compiled
to native machine code while the application is running.
```

#### What is the role of the JVM?

The JVM runs Java bytecode.

But in practice, it does more than just execute instructions. It also handles class loading, memory management, garbage collection, thread management, runtime optimizations, and safety checks.

#### What is the `main` method?

The `main` method is the standard entry point for a basic Java application.

```java
public static void main(String[] args)
```

It is where execution starts when you run a simple Java program from the command line.

### Common Mistakes

Mistake 1: Saying that Java source code runs directly on the JVM.

More accurate:

```text
Java source code is compiled to bytecode first. The JVM runs the bytecode.
```

Mistake 2: Saying that JVM means Java compiler.

The compiler is usually `javac`.

The JVM is the runtime that executes bytecode.

Mistake 3: Thinking platform independence means zero platform problems.

Real applications can still depend on operating system paths, native libraries, environment variables, file permissions, time zones, network setup, and container configuration.

Java gives you portability at the bytecode/runtime level, not a guarantee that every production deployment will be effortless.

### Mid/Senior Notes

At mid and senior levels, interviewers may expect more than the simple `.java -> .class -> JVM` story.

You should know that the JVM is a serious runtime platform, not only a Java launcher.

Important deeper points:

- The JVM loads classes dynamically when they are needed.
- The JVM verifies bytecode before execution for safety.
- The JVM manages memory and garbage collection.
- The JVM can optimize code while the program runs.
- JIT compilation can make frequently used code faster over time.
- Many languages besides Java can run on the JVM, for example Kotlin, Scala, and Groovy.

This matters because backend performance is often affected by runtime behavior.

For example, a Java application may start slower than a tiny native binary, but after warm-up the JVM can optimize hot paths very aggressively.

That is why performance discussions around Java should be careful. "Java is slow" is an outdated and lazy statement. A better answer is:

```text
Java has JVM startup and warm-up costs, but modern JVMs can optimize running code
very well. Performance depends on the workload, garbage collector, memory usage,
JIT behavior, application design, and infrastructure.
```

### When To Use This Knowledge

You use this knowledge when:

- explaining Java portability,
- debugging classpath or runtime issues,
- understanding build tools like Maven and Gradle,
- discussing JVM performance,
- working with containers,
- preparing for questions about JDK, JRE, JVM, bytecode, and compilation.

This is one of those topics that looks basic, but weak answers here make a bad first impression.

### Quick Summary

Java source code is written in `.java` files.

The compiler turns `.java` files into `.class` files containing bytecode.

The JVM runs bytecode.

This bytecode model is one of the main reasons Java is portable.

Modern JVMs also manage memory, load classes, run garbage collection, and optimize code at runtime using JIT compilation.

## JDK vs JRE vs JVM

### Simple Explanation

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

### Why It Exists

Different people need different parts of the Java platform.

A developer needs tools to write, compile, debug, and package code.

A machine that only runs an application does not always need all developer tools.

That is the conceptual difference:

- development needs the JDK,
- running needs a runtime environment,
- bytecode execution happens inside the JVM.

### Basic Example

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

### Common Interview Questions

#### What is the difference between JDK, JRE, and JVM?

Good interview answer:

```text
The JVM executes Java bytecode. The JRE is the runtime environment needed to run
Java applications. The JDK is for developers and includes tools like the compiler,
debugger, and other utilities. In practice, modern developers usually install a JDK.
```

#### Do you need a JDK to run Java code?

To compile Java code, yes.

To only run an already compiled Java application, conceptually you only need a runtime. In modern setups, many teams still use a JDK or a slim/custom runtime image in production, especially in containers.

#### Is JVM platform-independent?

No.

The JVM is platform-specific. There is a JVM build for Windows, Linux, macOS, and different CPU architectures.

Java bytecode is the platform-independent part.

That distinction is important.

### Common Mistakes

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

### Mid/Senior Notes

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

### Quick Summary

JVM runs bytecode.

JRE is the runtime environment.

JDK is the developer kit and includes tools like `javac`.

Modern developers usually install a JDK, and production systems often use containers or custom runtime images instead of old-style standalone JRE installations.

## Java Project Structure

### Simple Explanation

A Java project is not only a pile of `.java` files.

Real Java projects have structure.

That structure helps developers, build tools, IDEs, and test frameworks understand where things are.

In many modern Java projects, especially Maven and Gradle projects, you will often see a layout like this:

```text
my-app/
  pom.xml
  src/
    main/
      java/
        com/
          example/
            app/
              Application.java
      resources/
        application.properties
    test/
      java/
        com/
          example/
            app/
              ApplicationTest.java
      resources/
```

The basic idea:

- production Java code goes into `src/main/java`,
- production resources go into `src/main/resources`,
- test Java code goes into `src/test/java`,
- test resources go into `src/test/resources`,
- build configuration lives in files like `pom.xml` or `build.gradle`.

This layout is not random. It is a convention.

And conventions are useful because everyone knows where to look.

### Why It Exists

Without project structure, every project would organize files differently.

That sounds flexible, but in teams it becomes annoying fast.

Imagine joining a project and needing one hour just to find:

- source code,
- tests,
- configuration,
- resources,
- dependencies,
- build commands.

Standard layout solves that.

It makes Java projects easier to build, test, package, and understand.

### Basic Example

A small Maven project may look like this:

```text
calculator/
  pom.xml
  src/
    main/
      java/
        com/
          example/
            calculator/
              Calculator.java
    test/
      java/
        com/
          example/
            calculator/
              CalculatorTest.java
```

Production class:

```java
package com.example.calculator;

public class Calculator {
    public int add(int a, int b) {
        return a + b;
    }
}
```

Test class:

```java
package com.example.calculator;

import org.junit.jupiter.api.Test;

import static org.junit.jupiter.api.Assertions.assertEquals;

class CalculatorTest {
    @Test
    void addsTwoNumbers() {
        Calculator calculator = new Calculator();

        assertEquals(5, calculator.add(2, 3));
    }
}
```

The test mirrors the package of the production class.

That makes the relationship easy to see.

### Common Interview Questions

#### Where does production Java code usually live in a Maven or Gradle project?

Good answer:

```text
Usually in `src/main/java`. Resources like configuration files usually go into
`src/main/resources`. Tests usually go into `src/test/java`.
```

#### What is the difference between `src/main` and `src/test`?

`src/main` contains application code and resources used by the real application.

`src/test` contains test code and test resources used when running tests.

Test code should not be packaged as part of the production application in the same way as main code.

#### What is `pom.xml`?

`pom.xml` is Maven's project configuration file.

It defines project metadata, dependencies, plugins, build settings, and sometimes profiles.

For Gradle, similar configuration usually lives in `build.gradle` or `build.gradle.kts`.

### Common Mistakes

Mistake 1: Putting everything into one folder.

That may work for a tiny exercise, but it does not scale into real projects.

Mistake 2: Mixing tests with production code.

Tests should have their own source tree. That keeps production packaging cleaner and avoids accidental dependencies on test-only tools.

Mistake 3: Treating project structure as unimportant.

Structure is communication. A messy structure makes the project harder to understand before anyone even reads the code.

### Mid/Senior Notes

At mid and senior levels, project structure becomes an architecture topic.

A simple package layout is fine for a small application. But as the codebase grows, structure should reflect boundaries.

For example, this is often weak:

```text
controller/
service/
repository/
entity/
dto/
```

This is common, but it groups code by technical layer only.

For small apps, that is acceptable.

For larger apps, grouping by business capability can be easier to maintain:

```text
orders/
  OrderController.java
  OrderService.java
  OrderRepository.java
  Order.java

payments/
  PaymentController.java
  PaymentService.java
  PaymentRepository.java
  Payment.java
```

The second structure makes business boundaries more visible.

There is no universal winner. The important senior-level point is this:

```text
Project structure should make change easier.
```

If a new feature touches files scattered across ten unrelated folders, the structure may be working against you.

### Quick Summary

Java projects usually follow a standard source layout.

Production code commonly lives in `src/main/java`.

Tests commonly live in `src/test/java`.

Build tools like Maven and Gradle rely on conventions, which make projects easier to build and understand.

At higher levels, project structure should reflect maintainable boundaries, not only technical layers.

## Packages And Imports

### Simple Explanation

A package is a namespace for Java classes.

It helps organize code and avoid name conflicts.

An import lets you use a class from another package without writing its full name every time.

Example:

```java
package com.example.orders;

import java.time.LocalDate;

public class Order {
    private LocalDate createdAt;
}
```

Here:

- `com.example.orders` is the package,
- `java.time.LocalDate` is imported,
- `Order` belongs to the `com.example.orders` package.

### Why It Exists

Large applications can contain thousands of classes.

Without packages, names would collide quickly.

For example, many systems can have a class called `User`:

```text
com.example.auth.User
com.example.billing.User
com.example.crm.User
```

The package tells us which `User` we mean.

Packages also help organize code by feature, layer, or domain.

### Basic Code Example

File path:

```text
src/main/java/com/example/shop/Product.java
```

Code:

```java
package com.example.shop;

public class Product {
    private String name;

    public Product(String name) {
        this.name = name;
    }

    public String getName() {
        return name;
    }
}
```

Another class can import it:

```java
package com.example.orders;

import com.example.shop.Product;

public class OrderLine {
    private Product product;

    public OrderLine(Product product) {
        this.product = product;
    }
}
```

Without the import, you would need to write:

```java
private com.example.shop.Product product;
```

That is valid, but ugly if used often.

### Common Interview Questions

#### What is a package in Java?

Good answer:

```text
A package is a namespace used to organize Java classes and avoid name conflicts.
It also affects access control because package-private members are visible only
inside the same package.
```

#### What is an import?

An import tells the compiler which class you want to use from another package.

It does not copy code into your file. It only lets you refer to that class by its simple name.

#### Are packages only folders?

Not exactly.

In normal source projects, package names match folder structure, but the package is a Java language concept. The folder structure is how source files are usually organized to match that concept.

For example:

```java
package com.example.orders;
```

Usually lives in:

```text
com/example/orders/
```

### Common Mistakes

Mistake 1: Thinking `import` makes code slower.

Imports do not execute code. They are compile-time convenience.

Mistake 2: Using wildcard imports everywhere.

```java
import java.util.*;
```

This is not always terrible, but explicit imports are usually clearer in production code:

```java
import java.util.List;
import java.util.Map;
```

Mistake 3: Creating meaningless packages.

Package names should help people understand the codebase.

Names like `misc`, `utils`, and `common` often become dumping grounds. Sometimes a utility package is fine, but if everything ends up there, the structure is telling you the design is weak.

### Mid/Senior Notes

Package structure is not only cosmetic.

It can support or damage architecture.

For example, if every class is public and every package imports every other package freely, boundaries are weak.

Good package design can make the intended dependencies visible:

```text
orders -> payments
orders -> inventory
orders -> shared-kernel
```

Bad package design hides coupling until changing one feature breaks three others.

In larger systems, packages should usually follow business boundaries, not only technical categories.

Also remember package-private access. If a class, method, or constructor has no access modifier, it is visible only within the same package.

That can be useful for hiding implementation details without making everything `private`.

### Quick Summary

Packages organize Java classes and avoid name conflicts.

Imports let you use classes from other packages with shorter names.

Package names usually match folder paths.

Good package structure makes a codebase easier to understand and change.

## Classes And Objects

### Simple Explanation

A class is a blueprint.

An object is a real thing created from that blueprint.

Example:

```text
Class:  Car
Object: myCar, yourCar, companyCar
```

The class describes what a car has and what a car can do.

An object is one specific car in memory.

In Java, most code is organized around classes and objects.

### Why It Exists

Programs need to represent real or abstract things:

- users,
- orders,
- payments,
- invoices,
- products,
- connections,
- services,
- requests,
- responses.

Classes let us describe those things using data and behavior together.

Data is stored in fields.

Behavior is written as methods.

### Basic Code Example

```java
public class User {
    private String email;

    public User(String email) {
        this.email = email;
    }

    public String getEmail() {
        return email;
    }
}
```

Create objects:

```java
User firstUser = new User("anna@example.com");
User secondUser = new User("tom@example.com");

System.out.println(firstUser.getEmail());
System.out.println(secondUser.getEmail());
```

`User` is the class.

`firstUser` and `secondUser` are object references pointing to two different `User` objects.

### Important Detail: Object vs Reference

This is where many beginners get confused.

In Java, a variable that holds an object does not contain the object directly.

It contains a reference to the object.

Simple mental model:

```text
User user = new User("anna@example.com");

user  ---- points to ---->  User object in memory
```

This matters because two variables can point to the same object:

```java
User first = new User("anna@example.com");
User second = first;
```

Now `first` and `second` refer to the same object.

They are two references, but one object.

### Common Interview Questions

#### What is the difference between a class and an object?

Good answer:

```text
A class is a blueprint that defines fields and methods. An object is an instance
created from that class. For example, `User` is a class, and `new User(...)`
creates a specific User object.
```

#### What does `new` do?

`new` creates a new object in memory and calls a constructor to initialize it.

Then it returns a reference to that object.

#### Does a Java variable store the object itself?

For object types, the variable stores a reference to the object, not the object itself.

That is why two variables can refer to the same object.

### Common Mistakes

Mistake 1: Thinking a class and an object are the same thing.

They are connected, but not the same.

The class is the definition. The object is the instance.

Mistake 2: Forgetting that object variables store references.

This causes confusion when changing an object through one reference also appears visible through another reference.

Mistake 3: Creating classes that only hold random data with no clear responsibility.

Not every class needs complicated behavior, but a class should still represent a clear concept.

### Mid/Senior Notes

At mid and senior levels, classes are not only syntax. They are design units.

A weak class often has one of these problems:

- it does too many things,
- it has unclear responsibility,
- it exposes too much internal state,
- it depends on too many unrelated classes,
- it is hard to test without starting half the application.

A better class has a clear reason to exist.

For example, this is vague:

```java
public class UserHelper {
}
```

This is more meaningful:

```java
public class PasswordResetService {
}
```

The second name tells us what business capability the class supports.

A senior developer should think less about "how many classes do I need?" and more about:

```text
Where should this responsibility live?
What should this class know?
What should this class not know?
How painful will this be to change later?
```

### Quick Summary

A class is a blueprint.

An object is an instance of a class.

Object variables store references to objects.

Classes should not only compile. They should represent clear responsibilities.

## Fields, Methods, And Constructors

### Simple Explanation

Fields store data.

Methods define behavior.

Constructors create and initialize objects.

Example:

```java
public class User {
    private String email;          // field

    public User(String email) {    // constructor
        this.email = email;
    }

    public String getEmail() {     // method
        return email;
    }
}
```

Simple mental model:

```text
Field       -> what the object knows
Method      -> what the object can do
Constructor -> how the object starts its life
```

### Why It Exists

Objects need state and behavior.

For example, a `BankAccount` may know its balance and allow deposits:

```java
public class BankAccount {
    private int balance;

    public BankAccount(int initialBalance) {
        this.balance = initialBalance;
    }

    public void deposit(int amount) {
        balance += amount;
    }

    public int getBalance() {
        return balance;
    }
}
```

The field stores the balance.

The constructor sets the initial balance.

The methods control how the balance can be read or changed.

### Basic Code Example

```java
public class Product {
    private String name;
    private int priceInCents;

    public Product(String name, int priceInCents) {
        this.name = name;
        this.priceInCents = priceInCents;
    }

    public String getName() {
        return name;
    }

    public int getPriceInCents() {
        return priceInCents;
    }

    public boolean isExpensive() {
        return priceInCents > 10_000;
    }
}
```

This class has:

- two fields: `name`, `priceInCents`,
- one constructor,
- three methods.

### Common Interview Questions

#### What is a field?

A field is a variable that belongs to a class or object.

Instance fields store object state.

Static fields belong to the class itself.

#### What is a method?

A method is a block of behavior that can receive input, do work, and optionally return a result.

#### What is a constructor?

A constructor is a special block used to create and initialize an object.

It has the same name as the class and no return type.

#### Can a class have multiple constructors?

Yes.

That is called constructor overloading.

Example:

```java
public class User {
    private String email;
    private boolean active;

    public User(String email) {
        this(email, true);
    }

    public User(String email, boolean active) {
        this.email = email;
        this.active = active;
    }
}
```

### Common Mistakes

Mistake 1: Making fields public by default.

This exposes internal state and makes the object harder to control.

Better:

```java
private String email;
```

Mistake 2: Creating constructors that do too much.

A constructor should initialize the object. It usually should not call external APIs, send emails, or start complicated workflows.

Mistake 3: Writing methods that change state unexpectedly.

If a method looks like a simple getter but changes the object, that is confusing design.

### Mid/Senior Notes

Constructors protect object validity.

This means an object should not be created in a broken state.

Weak version:

```java
public class User {
    public String email;
}
```

This allows:

```java
User user = new User();
user.email = null;
```

Better version:

```java
public class User {
    private final String email;

    public User(String email) {
        if (email == null || email.isBlank()) {
            throw new IllegalArgumentException("Email must not be blank");
        }

        this.email = email;
    }

    public String getEmail() {
        return email;
    }
}
```

This class protects itself.

That is an important design idea:

```text
Do not create objects that are invalid from the beginning.
```

At mid and senior levels, fields, methods, and constructors are not only syntax. They are tools for controlling state, protecting invariants, and making code easier to reason about.

### Quick Summary

Fields store data.

Methods define behavior.

Constructors initialize objects.

Good constructors help prevent invalid objects.

Good methods make behavior clear and predictable.

## Access Modifiers

### Simple Explanation

Access modifiers control who can see and use a class, field, method, or constructor.

They are Java's basic visibility rules.

The four main levels are:

```text
public          -> visible from everywhere
protected       -> visible in the same package and subclasses
package-private -> visible only in the same package
private         -> visible only inside the same class
```

Package-private means there is no modifier:

```java
class PackageOnlyClass {
}
```

Access modifiers exist because not everything in your code should be visible to everything else.

Good code hides details.

Bad code exposes too much and becomes hard to change.

### Why It Exists

Imagine every field and method in every class is public.

Then any part of the application can touch anything.

At first, that feels convenient.

Later, it becomes a trap.

If many classes depend on internal details, changing those details becomes risky.

Access modifiers help you say:

```text
This is part of the public contract.
This is only for this class.
This is only for this package.
This is only for subclasses.
```

This is one of the basic tools for encapsulation.

### Basic Code Example

```java
public class BankAccount {
    private int balance;

    public BankAccount(int initialBalance) {
        if (initialBalance < 0) {
            throw new IllegalArgumentException("Initial balance cannot be negative");
        }

        this.balance = initialBalance;
    }

    public void deposit(int amount) {
        if (amount <= 0) {
            throw new IllegalArgumentException("Amount must be positive");
        }

        balance += amount;
    }

    public int getBalance() {
        return balance;
    }
}
```

Here:

- `BankAccount` is public, so other code can use it,
- `balance` is private, so other code cannot change it directly,
- `deposit` is public, so other code can add money through controlled behavior,
- the constructor protects the object from invalid initial state.

This is much better than:

```java
public class BankAccount {
    public int balance;
}
```

Because this allows any code to do:

```java
account.balance = -999999;
```

That is not freedom. That is broken design.

### Common Interview Questions

#### What are Java access modifiers?

Good answer:

```text
Access modifiers control visibility. `public` is visible everywhere, `private`
only inside the same class, `protected` in the same package and subclasses, and
package-private only inside the same package.
```

#### What is package-private access?

Package-private access is used when no access modifier is written.

The member or class is visible only inside the same package.

Example:

```java
class InternalCalculator {
}
```

#### Is `protected` visible only to subclasses?

No.

This is a common misunderstanding.

In Java, `protected` is visible to classes in the same package and also to subclasses.

### Common Mistakes

Mistake 1: Making fields public.

This breaks encapsulation because outside code can change object state directly.

Mistake 2: Making everything public "just in case."

That creates a larger API surface. The more public things you expose, the more things other code can depend on.

Mistake 3: Misunderstanding `protected`.

`protected` is not only subclass access. Same-package classes can also access it.

### Mid/Senior Notes

Access modifiers are not only syntax. They are design tools.

The public API of a class should be small and intentional.

If everything is public, the class has no clear boundary.

A useful rule:

```text
Start as private as possible. Open visibility only when there is a real reason.
```

This makes refactoring easier.

If a method is private, you can change it freely inside the class.

If a method is public, other code may depend on it. Changing it can break callers.

In libraries, this matters even more. A public method can become a long-term contract.

### Quick Summary

Access modifiers control visibility.

`private` is the most restrictive.

`public` is the most open.

Package-private means no modifier.

Good visibility choices protect encapsulation and make code easier to change.

## Static Vs Instance Members

### Simple Explanation

Instance members belong to an object.

Static members belong to the class.

That is the core difference.

Example:

```java
public class User {
    private String email;           // instance field
    private static int userCount;   // static field
}
```

Each `User` object has its own `email`.

But `userCount` belongs to the `User` class itself, not to one specific object.

Simple mental model:

```text
instance -> per object
static   -> per class
```

### Why It Exists

Sometimes data or behavior belongs to one object.

Example:

```text
user.email
order.totalPrice
account.balance
```

Sometimes behavior does not need a specific object.

Example:

```java
Math.max(10, 20);
Integer.parseInt("123");
```

You do not need to create a `Math` object to calculate the maximum of two numbers.

That is a good use case for static methods.

### Basic Code Example

```java
public class Counter {
    private int instanceCount = 0;
    private static int globalCount = 0;

    public void increment() {
        instanceCount++;
        globalCount++;
    }

    public int getInstanceCount() {
        return instanceCount;
    }

    public static int getGlobalCount() {
        return globalCount;
    }
}
```

Usage:

```java
Counter first = new Counter();
Counter second = new Counter();

first.increment();
first.increment();
second.increment();

System.out.println(first.getInstanceCount());   // 2
System.out.println(second.getInstanceCount());  // 1
System.out.println(Counter.getGlobalCount());   // 3
```

Each object has its own `instanceCount`.

All objects share one `globalCount`.

### Common Interview Questions

#### What is the difference between static and instance members?

Good answer:

```text
Instance members belong to a specific object. Static members belong to the class
itself and are shared across instances.
```

#### Can a static method access instance fields directly?

No.

A static method does not belong to a specific object, so it cannot directly access instance fields.

This will not compile:

```java
public class User {
    private String email;

    public static void printEmail() {
        System.out.println(email);
    }
}
```

Why?

Because which user's email should it print?

There is no specific object.

#### What is a common use case for static methods?

Utility methods, factory methods, constants, and behavior that does not require object state.

Example:

```java
int value = Integer.parseInt("42");
```

### Common Mistakes

Mistake 1: Using static everywhere because it is easy.

This often creates procedural code hidden inside classes.

It can make testing harder and state management more confusing.

Mistake 2: Using mutable static state carelessly.

Mutable static fields are shared globally inside the JVM. If multiple parts of the app modify them, bugs become harder to track.

Mistake 3: Thinking static means faster or better.

Static is about ownership, not automatically about performance.

### Mid/Senior Notes

Static methods are fine when used intentionally.

For example:

- pure utility methods,
- constants,
- stateless factory methods,
- simple converters.

But static state should make you careful.

This is risky:

```java
public class CurrentUserHolder {
    public static User currentUser;
}
```

In a web application with many users, this is a disaster waiting to happen. Requests from different users can interfere with each other.

In Spring applications, you usually let Spring manage object lifecycles instead of building global mutable static state.

Senior-level thought:

```text
Static methods can be useful. Static mutable state is often dangerous.
```

### Quick Summary

Instance members belong to objects.

Static members belong to classes.

Static methods cannot directly use instance fields.

Use static for things that truly do not belong to one object.

Be careful with mutable static state.

## Stack Vs Heap Basics

### Simple Explanation

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

### Why It Exists

Programs need memory for different things.

Method calls are temporary.

When a method starts, it gets a stack frame.

When the method ends, that stack frame is removed.

Objects often need to live longer than one method call, so they are stored on the heap and managed by the garbage collector.

### Basic Code Example

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

### Common Interview Questions

#### What is stored on the stack?

Method call frames, local primitive variables, and references to objects are commonly described as stack data.

Good simple answer:

```text
The stack stores method calls and local variables. For object variables, the local
reference is on the stack, while the object itself is usually on the heap.
```

#### What is stored on the heap?

Objects are stored on the heap and managed by the garbage collector.

#### Why can stack overflow happen?

Stack overflow usually happens when method calls are nested too deeply, often because of infinite or too-deep recursion.

Example:

```java
public void callForever() {
    callForever();
}
```

Each call needs a new stack frame. Eventually, the stack runs out of space.

#### Why can out of memory happen?

Out of memory can happen when the application needs more heap memory than available, often because too many objects are created or references prevent garbage collection.

### Common Mistakes

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

### Mid/Senior Notes

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

### Quick Summary

The stack stores method calls and local variables.

The heap stores objects.

Local variables can hold references to heap objects.

Stack overflow is usually caused by too many nested method calls.

Out of memory is usually connected to heap pressure or memory leaks.

The simple model is useful, but the JVM can optimize internally.

## Primitive Types

### Simple Explanation

Primitive types are Java's basic value types.

They are not objects.

Java has eight primitive types:

```text
byte
short
int
long
float
double
char
boolean
```

They store simple values directly:

```java
int age = 30;
boolean active = true;
double price = 19.99;
char grade = 'A';
```

### Why It Exists

Primitive types are efficient and simple.

You do not need a full object just to store a number or a boolean flag.

For example:

```java
int count = 10;
```

This is simpler and usually more efficient than using an object wrapper:

```java
Integer count = 10;
```

### Basic Code Example

```java
public class PrimitiveExample {
    public static void main(String[] args) {
        int usersCount = 100;
        long totalRequests = 5_000_000L;
        double averageResponseTime = 123.45;
        boolean serviceHealthy = true;

        System.out.println(usersCount);
        System.out.println(totalRequests);
        System.out.println(averageResponseTime);
        System.out.println(serviceHealthy);
    }
}
```

Notice the `L` after the long literal:

```java
long totalRequests = 5_000_000L;
```

That tells Java this number should be treated as a `long`.

### Common Interview Questions

#### What are primitive types in Java?

Good answer:

```text
Primitive types are Java's basic non-object value types, like `int`, `long`,
`double`, and `boolean`. They store simple values and are different from wrapper
classes like `Integer` or `Long`.
```

#### Is `String` a primitive type?

No.

`String` is a class.

This is a very common beginner mistake.

#### What is the default value of primitive fields?

Primitive fields have default values:

```text
int     -> 0
long    -> 0L
double  -> 0.0
boolean -> false
char    -> '\u0000'
```

But local variables must be initialized before use.

This will not compile:

```java
public void test() {
    int number;
    System.out.println(number);
}
```

### Common Mistakes

Mistake 1: Thinking primitives can be `null`.

They cannot.

This does not compile:

```java
int age = null;
```

If you need a nullable number, you need a wrapper type:

```java
Integer age = null;
```

Mistake 2: Comparing floating-point values carelessly.

Floating-point numbers can have precision issues.

This is risky:

```java
double result = 0.1 + 0.2;
System.out.println(result == 0.3);
```

For money, prefer `BigDecimal`, not `double`.

Mistake 3: Forgetting integer division.

```java
System.out.println(5 / 2); // 2
```

Both numbers are integers, so Java performs integer division.

Use:

```java
System.out.println(5.0 / 2); // 2.5
```

### Mid/Senior Notes

Primitive types matter for performance, memory usage, and API design.

For example, `int[]` is much more memory-efficient than `Integer[]`, because `Integer[]` stores references to wrapper objects.

In high-performance code, this can matter a lot.

But in normal business code, readability and correct null handling often matter more.

Important practical rule:

```text
Use primitives when a value is required.
Use wrappers when nullability, generics, or object behavior is needed.
```

Example:

```java
private int retryCount;        // always has a value
private Integer age;           // may be unknown
```

### Quick Summary

Java has eight primitive types.

Primitives are not objects.

Primitives cannot be `null`.

`String` is not a primitive.

Use primitives for required simple values, and wrappers when you need nullability or object behavior.

## Wrapper Classes

### Simple Explanation

Wrapper classes are object versions of primitive types.

Examples:

```text
int     -> Integer
long    -> Long
double  -> Double
boolean -> Boolean
char    -> Character
```

Why do they exist?

Because sometimes Java needs values to be objects.

For example, collections and generics work with objects, not primitive types:

```java
List<Integer> numbers = List.of(1, 2, 3);
```

You cannot write:

```java
List<int> numbers = List.of(1, 2, 3);
```

That does not compile.

### Why It Exists

Java has both primitives and objects.

Primitives are efficient.

Objects are more flexible.

Wrapper classes connect these two worlds.

They allow primitive-like values to be used:

- in collections,
- with generics,
- as nullable values,
- with utility methods,
- where an object is required.

### Basic Code Example

```java
Integer age = 30;
Long requests = 1000L;
Boolean active = true;

int parsed = Integer.parseInt("123");
String text = Integer.toString(456);
```

Wrapper classes also contain useful constants:

```java
System.out.println(Integer.MAX_VALUE);
System.out.println(Integer.MIN_VALUE);
```

### Common Interview Questions

#### What is a wrapper class?

Good answer:

```text
A wrapper class is an object representation of a primitive type, like `Integer`
for `int` or `Long` for `long`. Wrappers are useful when we need objects, for
example in collections, generics, or nullable fields.
```

#### Why do collections use `Integer` instead of `int`?

Java generics work with reference types, not primitive types.

That is why we write:

```java
List<Integer> numbers = new ArrayList<>();
```

Not:

```java
List<int> numbers = new ArrayList<>();
```

#### Can wrapper types be `null`?

Yes.

That is one major difference from primitives.

```java
Integer age = null;
```

But this can create `NullPointerException` if Java tries to unbox it into an `int`.

### Common Mistakes

Mistake 1: Forgetting wrappers can be null.

This code compiles:

```java
Integer value = null;
int number = value;
```

But it fails at runtime with `NullPointerException`, because Java tries to unbox `null`.

Mistake 2: Comparing wrapper objects with `==`.

This can be confusing:

```java
Integer a = 1000;
Integer b = 1000;

System.out.println(a == b);      // false in many cases
System.out.println(a.equals(b)); // true
```

Use `.equals()` for value comparison with wrapper objects.

Mistake 3: Using wrappers everywhere without reason.

If a field is required and cannot be missing, a primitive may communicate that better:

```java
private boolean active;
```

Instead of:

```java
private Boolean active;
```

Unless `null` has a real meaning.

### Mid/Senior Notes

Wrapper classes look simple, but they matter in real systems.

Nullability is the biggest design issue.

For example:

```java
private Boolean emailVerified;
```

This can have three states:

```text
true
false
null
```

That may be useful if `null` means "unknown".

But if your business logic only has two states, `boolean` is better:

```java
private boolean emailVerified;
```

Wrappers also matter in performance-sensitive code because they add object overhead and can create many allocations when used heavily.

Most business code should not obsess over this, but a mid/senior developer should know the tradeoff.

### Quick Summary

Wrapper classes are object versions of primitive types.

Collections and generics use wrappers, not primitives.

Wrappers can be `null`.

Use `.equals()` for wrapper value comparison.

Use wrappers when you need nullability or object behavior. Use primitives when the value is always required.
