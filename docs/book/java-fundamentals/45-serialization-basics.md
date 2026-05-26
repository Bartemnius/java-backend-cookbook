---
layout: default
title: Serialization Basics
parent: Java Fundamentals
grand_parent: Book
nav_order: 45
---

# Serialization Basics

## Simple Explanation

Serialization means converting an object into a format that can be stored or sent somewhere.

Deserialization means converting that format back into an object.

Simple idea:

```text
object -> bytes/text -> object
```

Examples:

```text
Java object -> JSON -> Java object
Java object -> bytes -> Java object
Java object -> database row -> Java object
```

In backend development, you most often see serialization as JSON in APIs.

## Why It Exists

Objects live in memory.

But sometimes you need to:

- send data over HTTP,
- store data in a file,
- put data in a queue,
- cache data,
- send events,
- save session state.

To do that, the object must be transformed into a transferable format.

That is serialization.

## JSON Example

Java object:

```java
public record UserResponse(Long id, String email) {
}
```

JSON:

```json
{
  "id": 1,
  "email": "anna@example.com"
}
```

In Spring Boot, Jackson often handles this automatically:

```java
@GetMapping("/users/{id}")
public UserResponse getUser(@PathVariable Long id) {
    return new UserResponse(id, "anna@example.com");
}
```

The Java object is serialized to JSON in the HTTP response.

## Java Native Serialization

Java also has built-in binary serialization with `Serializable`.

Example:

```java
import java.io.Serializable;

public class User implements Serializable {
    private String email;
}
```

This allows Java to convert the object to bytes and later reconstruct it.

But modern backend applications usually avoid Java native serialization for APIs.

Why?

Because it is Java-specific, fragile across versions, and has a history of security risks when deserializing untrusted data.

For external communication, formats like JSON, Avro, Protocol Buffers, or plain database rows are usually more common.

## serialVersionUID

Serializable classes can define `serialVersionUID`:

```java
private static final long serialVersionUID = 1L;
```

It is used during deserialization to check whether the class version matches the serialized data.

If versions do not match, deserialization may fail.

You do not need to obsess over this for modern API work, but you should know what it is for interviews.

## Common Interview Questions

### What is serialization?

Good answer:

```text
Serialization is converting an object into a format that can be stored or sent,
such as JSON or bytes. Deserialization is converting that data back into an object.
```

### What is Java `Serializable`?

`Serializable` is a marker interface used by Java's built-in serialization mechanism.

It has no methods.

It tells Java that objects of this class can be serialized by the native Java serialization mechanism.

### What is `serialVersionUID`?

Good answer:

```text
serialVersionUID is a version identifier for Serializable classes. During
deserialization, Java uses it to check whether the serialized object matches the
current class version.
```

### Is Java native serialization commonly used for REST APIs?

No.

REST APIs usually use JSON.

In Spring Boot, Jackson commonly serializes Java objects to JSON and deserializes JSON into Java objects.

### Why can deserialization be dangerous?

Deserializing untrusted data can be dangerous because it may create unexpected objects or trigger unsafe behavior depending on the serialization mechanism and classpath.

Never blindly deserialize untrusted data with unsafe mechanisms.

## Common Mistakes

Mistake 1: Thinking serialization only means Java `Serializable`.

Serialization is a general concept.

JSON serialization is serialization too.

Mistake 2: Exposing internal entities directly as API JSON.

Bad:

```java
public UserEntity getUser() {
    return userRepository.findById(id);
}
```

Better:

```java
public UserResponse getUser() {
    return new UserResponse(id, email);
}
```

Use DTOs to control API shape.

Mistake 3: Deserializing untrusted data carelessly.

This can create security risks.

Mistake 4: Forgetting backward compatibility.

If clients depend on your JSON fields, changing field names or removing fields can break them.

## Mid/Senior Notes

In backend interviews, serialization connects to API design.

Important questions:

- What format do we expose?
- Is the API backward compatible?
- Are field names stable?
- Do we expose internal domain models?
- How do we handle unknown fields?
- How do we handle dates and time zones?
- Can clients deserialize this safely?

For public APIs, be conservative.

Changing JSON structure can break clients.

Example:

```json
{
  "userId": 1,
  "email": "anna@example.com"
}
```

If clients use `userId`, renaming it to `id` is a breaking change unless handled carefully.

Senior-level answer:

```text
Serialization is not only a technical conversion. It defines a contract between
systems. For APIs, I prefer explicit DTOs, stable JSON shapes, careful date formats,
and avoiding native Java serialization for untrusted external data.
```

## Quick Summary

Serialization converts objects into transferable or storable data.

Deserialization converts data back into objects.

JSON serialization is common in backend APIs.

Java `Serializable` is native Java binary serialization.

Be careful with deserialization of untrusted data.

Use DTOs for API boundaries.

