# PSR-Compliant Simple Cache Implementation

***

## Overview

The PSR simple cache interface defines the most basic operations for cache entries, which entails basic reading, writing, and deleting individual cache items.

***

## Introduction

**Simple Cache** is a straightforward caching interface defined by the PHP Framework Interop Group (PHP-FIG) as **PSR-16**. It provides a simplified way to manage cache items without requiring complex setups. In the Luminova framework, Simple Cache supports various storage drivers, including file systems and Memcached.

---

### Installation

To use Luminova caching with [PSR-16 Simple interface](https://www.php-fig.org/psr/psr-16/), install the [PSR Cache module](https://github.com/nanoblocktech/psr-cache) via Composer:

```bash
composer require nanoblocktech/psr-cache
```

---

## Concepts of Simple Cache:

Unlike the [PSR-6 Cache Pool Interface](/cache/psr-cache-pool.md), the **Simple Cache** class is designed for ease of implementation and use, making it suitable for developers seeking a quick caching solution.

### Initializing Cache Pool

Create an instance of the cache pool by specifying an optional cache storage name, a storage subfolder (for file-based caching), or a persistent ID (for the `Memcached` driver). You can also select the storage driver type.

#### File System Driver Initialization

```php
use Luminova\Psr\Cache\SimpleCache;

// Create a new instance with file-based caching
$simple = new SimpleCache('my_cache_storage', 'cache_subfolder', SimpleCache::FILECACHE);

// Or use the factory method
$simple = SimpleCache::withFileCache('my_cache_storage', 'cache_subfolder');
```

---

#### Memcached Driver Initialization

```php
use Luminova\Psr\Cache\SimpleCache;

// Create a new instance with Memcached
$simple = new SimpleCache('my_cache_storage', 'my_persistent_id', SimpleCache::MEMCACHED);

// Or use the factory method
$simple = SimpleCache::withMemCache('my_cache_storage', 'my_persistent_id');
```

---

### Usage Example

In this example, **SimpleCache** manages cached values, with the underlying storage being either a file system cache driver or a memory-based cache driver (like Memcached).

#### Check for Cache Existence Before Storing

To determine if a cache item exists, you can check if the returned data matches your custom `DEFAULT` or use method `SimpleCache::has('key')`. If it doesn't, retrieve the data from the database (or another source), set the cache content with an expiration time, and save it. If the cache item exists, use the cached data directly.

```php
<?php
use DateInterval;

if (!$simple->has('cache_key')) {
    // Data not found in cache, load from the database
    $data = 'This is my cache data';
    
    // Store the data in cache with a 1-hour expiration
    $simple->set('cache_key', $data, new DateInterval('PT1H'));
} else {
    // Retrieve data from cache
    $data = $simple->get('cache_key');
}
```

#### Retrieve a Cached Value

You can easily fetch the stored data from the cache using the cache key. If the item doesn't exist, a default value or `null` will be returned.

```php
<?php
$data = $simple->get('cache_key', 'DEFAULT_NO_DATA');
``` 

---

# Simple Cache Class Definition

* Class namespace: `Luminova\Psr\Cache\SimpleCache`
* This class implements:
[\Psr\Cache\CacheInterface](https://www.php-fig.org/psr/psr-16/#21-cacheinterface)

---

## Constants

The following constants define the cache storage types available in the **SimpleCache**. These are used to specify which caching driver should be used when initializing the simple cache.

| Constant    | Type   | Value         | Description                                      |
|:------------|:-------|:--------------|:-------------------------------------------------|
| `MEMCACHED` | string | `'memcached'` | Represents the Memcached-based cache storage.    |
| `FILECACHE` | string | `'filesystem'`| Represents the file system-based cache storage.   |

***

## Cache Pool Methods

### constructor

Initializes psr simple cache instance using either a file-based cache, a memory-based cache (Memcached),
or the driver specified by the `preferred.cache.driver` configuration if no driver is provided.

```php
public __construct(
    string|null $storage = 'psr_cache_storage', 
    string|null $subfolderOrId = 'psr', 
    string|null $driver = self::FILECACHE
): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$storage` | **string&#124;null** | The cache storage name used to distinguish different cache pools or spaces. |
| `$subfolderOrId` | **string&#124;null** | Optional subfolder for file-based cache or persistent ID for Memcached. |
| `$driver` | **string&#124;null** | Optional cache driver. Defaults to `SimpleCache::FILECACHE` if not provided.<br />Accepts `SimpleCache::FILECACHE` or `SimpleCache::MEMCACHED`. |

***

### withFileCache

Creates or returns a singleton instance of the class using file-based caching.

```php
public static withFileCache(?string $storage = null, ?string $subfolder = null): static
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$storage` | **string&#124;null** | Optional storage name to be used in the file cache. |
| `$subfolder` | **string&#124;null** | Optional subfolder name for file-based storage. |

**Return Value:**

`Luminova\Psr\Cache\SimpleCache` - Returns a singleton instance of the class with a file-based cache engine.

***

### withMemCache

Creates or returns a singleton instance of the class using Memcached.

```php
public static withMemCache(?string $storage = null, ?string $persistent_id = null): static
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$storage` | **string&#124;null** | Optional storage name to be used in the Memcached engine. |
| `$persistent_id` | **string&#124;null** | Optional persistent ID for Memcached sessions. |

**Return Value:**

`Luminova\Psr\Cache\SimpleCache` - Returns a singleton instance of the class with a memory-based cache engine.

***

### get

Fetches a value from the cache.

```php
public get(string $key, mixed $default = null): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string** | The unique key of this item in the cache. |
| `$default` | **mixed** | Default value to return if the key does not exist (default: null). |

**Return Value:**

`mixed` - Returns the value of the item from the cache, or `$default` in case of cache miss.

**Throws:**

`Luminova\Psr\Cache\Exceptions\InvalidArgumentException` - Throws if the `$key` string is not a legal value.

***

### set

Persists data in the cache, uniquely referenced by a key with an optional expiration `TTL` time.

```php
public set(string $key, mixed $value, \DateInterval|int|null $ttl = null): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string** | The key of the item to store. |
| `$value` | **mixed** | The value of the item to store. Must be serializable. |
| `$ttl` | **\DateInterval&#124;int&#124;null** | Optional TTL value of this item (default: 24 hours).|

**Return Value:**

`bool` - Returns true on success and false on failure.

**Throws:**

`Luminova\Psr\Cache\Exceptions\InvalidArgumentException` - Throws if the `$key` string is not a legal value.

***

### has

Determines whether an item is present in the cache.

```php
public has(string $key): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string** | The cache item key. |

**Return Value:**

`bool` - Returns true if cache key exists, otherwise false on failure.

**Throws:**

`Luminova\Psr\Cache\Exceptions\InvalidArgumentException` - Throws if the `$key` string is not a legal value.

> **NOTE:** It is recommended that `has()` is only to be used for cache warming type purposes and not to be used within your live applications operations for `get/set`, as this method is subject to a race condition where `has()` will return true and immediately after,another script can remove it, making the state of your app out of date.

***

### delete

Delete an item from the cache by its unique key.

```php
public delete(string $key): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string** | The unique cache key of the item to delete. |

**Return Value:**

`bool` - Returns true if the item was successfully removed. False if there was an error.

**Throws:**

`Luminova\Psr\Cache\Exceptions\InvalidArgumentException` - Throws if the `$key` string is not a legal value.

***

### clear

Wipes clean the entire cache's keys.

```php
public clear(): bool
```

**Return Value:**

`bool` - Returns true on success and false on failure.

***

### getMultiple

Obtains multiple cache items by their unique keys.

```php
public getMultiple(iterable $keys, mixed $default = null): iterable
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$keys` | **iterable** | A list of keys that can obtained in a single operation. |
| `$default` | **mixed** | Default value to return for keys that do not exist (default: null). |

**Return Value:**

`iterable` - Return list of `key => value` pairs. Cache keys that do not exist or are stale will have `$default` as value.

**Throws:**

`Luminova\Psr\Cache\Exceptions\InvalidArgumentException` - Throws if `$keys` is neither an array nor a Traversable, or if any of the `$keys` are not a legal value.

***

### setMultiple

Persists a set of `key => value` pairs in the cache, with an optional `TTL`.

```php
public setMultiple(iterable $values, \DateInterval|int|null $ttl = null): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$values` | **iterable** | A list of `key => value` pairs for a multiple-set operation. |
| `$ttl` | **\DateInterval&#124;int&#124;null** | Optional TTL value of this item (default: 24 hours). |

**Return Value:**

`bool` - Return true on success and false on failure.

**Throws:**

`Luminova\Psr\Cache\Exceptions\InvalidArgumentException` - Throws if `$values` is neither an array nor a Traversable, or if any of the $values are not a legal value.

***

### deleteMultiple

Deletes multiple cache items in a single operation.

```php
public deleteMultiple(iterable $keys): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$keys` | **iterable** | A list of string-based keys to be deleted. |

**Return Value:**

`bool` - Return true if the items were successfully removed. False if there was an error.

**Throws:**

`Luminova\Psr\Cache\Exceptions\InvalidArgumentException` - Throws if `$keys` is neither an array nor a Traversable, or if any of the $keys are not a legal value.