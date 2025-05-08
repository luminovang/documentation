# Date and Time Tasks Util

***

## Overview

Task class simplifies date and time-related tasks in Luminova applications. It offers convenient methods for checking task statuses, performing pre-expiration checks, and calculating time intervals. 

***

## Introduction

The Task class is a utility class shipped with Luminova framework, designed to provide convenient methods for working with date and time-related operations in your application. By encapsulating common date and time functionality into static methods, to simplify the process of performing tasks like expiration checking, and interval calculations.

***

## Features

#### Status Checking

With the `isOpen` and `expired` methods, developers can easily determine whether a task is currently open or has already expired, based on specified opening and closing times or expiration dates in a specific timezone.

#### Pre-Expiration Checks

The `before` method allows developer to perform pre-expiration checks, useful for sending notifications or taking actions before a task's expiration date.

#### Time Interval Calculation

Developers can leverage the `passed` method to determine if a certain amount of time has elapsed since a given timestamp, enhancing time-based logic and decision-making in applications.

***

* Class namespace: `\Luminova\Time\Task`

## Methods

### isOpen

Check if the current time is between opening and closing times.

```php
public static isOpen(string $openDatetime, string $closeDatetime, null|\DateTimeZone|string $timezone = 'UTC'): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$openDatetime` | **string** | Opening date and time (e.g., '2023-09-25 08:00AM'). |
| `$closeDatetime` | **string** | Closing date and time (e.g., '2023-09-25 05:00PM'). |
| `$timezone` | **null&#124;\DateTimeZone&#124;string** | Optional timezone string. |

**Return Value:**

`bool` - Returns true if the task is still open, false otherwise.

> This utility function is useful for checking business opening and closing hours.

***

### expired

Check if a given date and time has passed.

```php
public static expired(string $datetime, null|\DateTimeZone|string $timezone = 'UTC'): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$datetime` | **string** | The expiration date and time (e.g., '2023-09-25 08:00AM'). |
| `$timezone` | **null&#124;\DateTimeZone&#124;string** | Optional timezone string. |

**Return Value:**

`bool` - Returns true if the task has expired, false otherwise.

> Useful for checking if a deal or promo code has expired.

***

### before

Check if the given date is within a specified number of days before expiration.

```php
public static before(string $datetime, int $days = 2, null|\DateTimeZone|string $timezone = 'UTC'): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$datetime` | **string** | Expiration date and time (e.g., '2023-09-25 08:00AM'). |
| `$days` | **int** | Number of days to check before expiration (default: 2). |
| `$timezone` | **null&#124;\DateTimeZone&#124;string** | Optional timezone string. |

**Return Value:**

`bool` - Returns true if it's within the specified number of days before expiration.

**Throws:**

- [\Luminova\Exceptions\InvalidArgumentException](/running/exceptions.md#invalidargumentexception) - If invalid days was passed.

> This method is useful to check and send notification days before subscription expiration.