---
layout: default
title: Date And Time API
parent: Java Fundamentals
grand_parent: Book
nav_order: 43
---

# Date And Time API

## Simple Explanation

Java has a modern date and time API in the `java.time` package.

The most important types are:

```text
LocalDate     -> date only, no time
LocalTime     -> time only, no date
LocalDateTime -> date and time, no timezone
ZonedDateTime -> date and time with timezone
Instant       -> exact moment in UTC
Duration      -> amount of time between moments
Period        -> amount of date-based time
```

Example:

```java
LocalDate today = LocalDate.now();
Instant now = Instant.now();
```

Use `java.time` in modern Java.

Avoid old `Date` and `Calendar` unless you work with legacy code.

## Why It Exists

Date and time are harder than they look.

You need to deal with:

- time zones,
- daylight saving time,
- date-only values,
- exact instants,
- formatting,
- parsing,
- durations,
- business dates.

Old Java date APIs were confusing and mutable.

`java.time` gives clearer, immutable types.

## Common Types

### LocalDate

Use `LocalDate` for date-only values.

```java
LocalDate birthDate = LocalDate.of(1995, 5, 20);
```

Good for:

- birthdays,
- invoice dates,
- booking dates,
- deadlines without time.

### LocalDateTime

Use `LocalDateTime` for date and time without timezone.

```java
LocalDateTime meeting = LocalDateTime.of(2026, 5, 26, 10, 30);
```

Be careful: this is not an exact global moment.

`2026-05-26 10:30` in Warsaw is not the same instant as `2026-05-26 10:30` in New York.

### ZonedDateTime

Use `ZonedDateTime` when timezone matters.

```java
ZonedDateTime warsawTime = ZonedDateTime.now(ZoneId.of("Europe/Warsaw"));
```

Good for user-facing scheduled events.

### Instant

Use `Instant` for an exact moment in time, usually in UTC.

```java
Instant createdAt = Instant.now();
```

Good for:

- audit timestamps,
- event timestamps,
- database created/updated times,
- distributed systems.

## Basic Code Example

```java
import java.time.Instant;
import java.time.LocalDate;
import java.time.ZoneId;
import java.time.ZonedDateTime;

public class DateTimeExample {
    public static void main(String[] args) {
        LocalDate today = LocalDate.now();
        Instant now = Instant.now();
        ZonedDateTime warsaw = now.atZone(ZoneId.of("Europe/Warsaw"));

        System.out.println(today);
        System.out.println(now);
        System.out.println(warsaw);
    }
}
```

This shows date-only, exact UTC moment, and timezone-aware date-time.

## Formatting And Parsing

```java
DateTimeFormatter formatter = DateTimeFormatter.ofPattern("yyyy-MM-dd");

LocalDate date = LocalDate.parse("2026-05-26", formatter);
String text = date.format(formatter);
```

For ISO formats, Java often works without a custom formatter:

```java
LocalDate date = LocalDate.parse("2026-05-26");
Instant instant = Instant.parse("2026-05-26T10:15:30Z");
```

## Common Interview Questions

### What is the difference between `LocalDateTime` and `Instant`?

Good answer:

```text
LocalDateTime is a date and time without timezone, so it is not a precise global
moment. Instant represents an exact moment on the UTC timeline and is better for
timestamps like createdAt or event time.
```

### When should you use `ZonedDateTime`?

Use `ZonedDateTime` when the timezone is part of the meaning.

Example:

```text
Meeting starts at 10:00 Europe/Warsaw.
```

### Are `java.time` classes immutable?

Yes, the main `java.time` classes are immutable.

Methods return new values:

```java
LocalDate tomorrow = today.plusDays(1);
```

`today` is not changed.

### What should you use for timestamps in backend systems?

Often `Instant`.

It represents an exact point in time and avoids many timezone problems internally.

Convert to user timezone at the edges, such as UI/API formatting.

## Common Mistakes

Mistake 1: Using `LocalDateTime` for exact timestamps.

Bad for audit time:

```java
LocalDateTime createdAt;
```

Better:

```java
Instant createdAt;
```

Mistake 2: Ignoring time zones.

If users are in different countries, timezone matters.

Mistake 3: Using old `Date` and `Calendar` in new code.

Prefer `java.time`.

Mistake 4: Storing formatted dates instead of proper date/time types.

Store date/time as proper types in code and database when possible.

Format only for display.

## Mid/Senior Notes

Time bugs are production bugs.

They show up in:

- billing,
- subscriptions,
- scheduled jobs,
- reports,
- audit logs,
- global applications,
- daylight saving time changes.

Good backend rule:

```text
Store exact moments as Instant/UTC.
Convert to user timezone at the boundary.
```

For business dates, use `LocalDate`.

Example:

```text
Invoice date: 2026-05-26
```

This is a date, not an exact moment.

For scheduled user events, store enough timezone information:

```text
2026-05-26 10:00 Europe/Warsaw
```

Senior-level answer:

```text
I avoid using LocalDateTime as a universal timestamp. For exact moments I prefer
Instant, for date-only business values I use LocalDate, and when timezone is part
of the meaning I use ZonedDateTime or store the zone separately.
```

## Quick Summary

Use `java.time`.

`LocalDate` is date only.

`LocalDateTime` has no timezone.

`ZonedDateTime` includes timezone.

`Instant` is an exact UTC moment.

Use `Instant` for technical timestamps.

Use `LocalDate` for business dates.

