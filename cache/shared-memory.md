# Runtime Shared Memory

***

## Overview

Lightweight in-memory storage for global application key/value data. Works as a static runtime registry and does not save data permanently.

***

## Introduction

The **Shared Memory** is a lightweight in-memory storage for application key/value data. It allows you save and read values during a single request. It works like a small “notepad” in memory that your application can share while it is running.

This is **not** real saved storage. Once the request ends, everything is gone.
Think of it like temporary notes on a whiteboard that gets wiped clean after each request.

---

## What this storage can do

* Save a value under a key
* Read it back later
* Check if a key exists
* Remove a key
* Clear all keys
* Count how many items are stored

This storage is:

Useful when you want to share values between different parts of your application without using session or passing them around manually.

* In-memory only
* Lost after each request
* Not shared between workers
* Not saved to disk

---

## Usages

```php
use Luminova\Utility\Storage\SharedMemory;

// Store a value
SharedMemory::set('theme', 'dark');

// Get a value
$theme = SharedMemory::get('theme') ?? 'light';

// Check if it exists
if (SharedMemory::has('theme')) {
    // ...
}

// Remove one value
SharedMemory::remove('theme');

// Clear everything
SharedMemory::clear();

// Count how many keys are stored
$count = SharedMemory::count();
```

---

## Class Definition

* Class namespace: `\Luminova\Utility\Storage\SharedMemory`
* This class is marked as **final** and can't be subclassed

**See Also:**

* `Luminova\Funcs\shared()` - Global helper for quick access.

---

## Methods

### set

Set a shared value.

Stores the given value under the specified key.

```php
public static set(string $key, mixed $value): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string** | The key to store the value under. |
| `$value` | **mixed** | The value to store. |

**Return Value:**

`mixed` - Returns the stored value.

***

### get

Get a shared value.

Retrieves a value stored under the specified key.

```php
public static get(string $key): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string** | The key to retrieve. |

**Return Value:**

`mixed` - Returns the stored value or `null` if not found.

> **Note:**
> This method returns `null` if the key does not exist.

***

### has

Check if a key exists in the shared storage.

```php
public static has(string $key): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string** | The key to check. |

**Return Value:**

`bool` - Return true if the key exists, false otherwise.

***

### remove

Remove a key from the shared storage.

```php
public static remove(string $key): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string** | The key to remove. |

***

### clear

Clear all keys from the shared storage.

```php
public static clear(): void
```

***

### count

Count the number of keys currently stored.

```php
public static count(): int
```

**Return Value:**

`int` - Return the number of stored keys.