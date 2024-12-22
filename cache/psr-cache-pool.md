# PSR Cache Pool Implementation

***

## Overview

Cache Pool is a caching adapter that implements the PSR CacheItemPoolInterface. It provides various methods for effectively managing cache items in your applications.

***

## Introduction

**Cache Pool** is a design pattern in caching systems that enables efficient and standardized management of cache items. It follows the **PSR-6 (PHP-FIG)** caching interface, which defines guidelines for implementing caching in a consistent way. This allows applications to use the same cache pool regardless of changes to the underlying cache mechanism. In the Luminova framework, the cache pool supports multiple storage drivers, such as file system or Memcached.

---

### Installation

To use Luminova caching with [PSR interface implementations](https://www.php-fig.org/psr/psr-6/), install the [PSR cache module](https://github.com/nanoblocktech/psr-cache), which supports both `CachePool` and `SimpleCache` interfaces. This module is available via Composer:

```bash
composer require nanoblocktech/psr-cache
```

---

## Concepts of Cache Pool:

The **cache pool** class is responsible for managing multiple cache items. It acts as a central repository where cache items are stored, retrieved, and managed. The pool encapsulates the entire process of storing and retrieving cached data, making it easier for developers to interact with cache systems through a common interface.

### Initializing Cache Pool

Create an instance of the cache pool by specifying an optional cache storage name, a storage subfolder (for file-based caching), or a persistent ID (for the `Memcached` driver). You can also select the storage driver type.

#### File System Driver Initialization

```php
use \Luminova\Psr\Cache\CachePool;

// Create a new instance with file-based caching
$pool = new CachePool('my_cache_storage', 'cache_subfolder', CachePool::FILECACHE);

// Or use the factory method
$pool = CachePool::withFileCache('my_cache_storage', 'cache_subfolder');
```

---

#### Memcached Driver Initialization

```php
use \Luminova\Psr\Cache\CachePool;

// Create a new instance with Memcached
$pool = new CachePool('my_cache_storage', 'my_persistent_id', CachePool::MEMCACHED);

// Or use the factory method
$pool = CachePool::withMemCache('my_cache_storage', 'my_persistent_id');
```

---

### Usage Example

In this example, the **CachePool** manages cached items, with the underlying storage could be a file system cache driver or a memory-based cache driver (Memcached).

#### Check if a Cache Item Exists Before Storing:

Check whether a cache item exists. If not, retrieve the data from the database (or another source), set the cache item with an expiration time, and save it to the cache pool. If the cache item exists, use the cached data directly.

```php
<?php
use \DateInterval;

// Fetch a cache item by key
$item = $pool->getItem('cache_key');

// If cache miss, load data and store it in the cache
if (!$item->isHit()) {
    // Cache miss - retrieve data from a database or other source
    $data = 'new cache pool value';
    
    // Set the cache item and its expiration time (e.g., 1 hour)
    $item->set($data);
    $item->expiresAfter(new DateInterval('PT1H')); 
    
    // Save the updated item to the cache pool
    $pool->save($item);
} else {
    // Cache hit - use the existing cached data
    $data = $item->get();
}
```

#### Retrieve a Cache Item Value:

Simply fetch the stored data from the cache using the cache key.

```php
$data = $pool->getItem('cache_key')->get();
```

---

# Cache Pool Class Definition

* Class namespace: `\Luminova\Psr\Cache\CachePool`
* This class implements:
[\Psr\Cache\CacheItemPoolInterface](https://www.php-fig.org/psr/psr-6/#cacheitempoolinterface)

---

## Constants

The following constants define the cache storage types available in the **CachePool**. These are used to specify which caching driver should be used when initializing the cache pool.

| Constant    | Type   | Value         | Description                                      |
|:------------|:-------|:--------------|:-------------------------------------------------|
| `MEMCACHED` | string | `'memcached'` | Represents the Memcached-based cache storage.    |
| `FILECACHE` | string | `'filesystem'`| Represents the file system-based cache storage.   |

***

## Cache Pool Methods

### constructor

Initializes psr cache pool instance using either a file-based cache, a memory-based cache (Memcached),
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
| `$driver` | **string&#124;null** | Optional cache driver. Defaults to `CachePool::FILECACHE` if not provided.<br />Accepts `CachePool::FILECACHE` or `CachePool::MEMCACHED`. |

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

`\Luminova\Psr\Cache\CachePool` - Returns a singleton instance of the class with a file-based cache engine.

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

`\Luminova\Psr\Cache\CachePool` - Returns a singleton instance of the class with a memory-based cache engine.

***

### getItem

Returns a Cache Item representing the specified key.

```php
public getItem(string $key): \Psr\Cache\CacheItemInterface
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string** | The key for which to return the corresponding Cache Item. |

**Return Value:**

`\Psr\Cache\CacheItemInterface` - Return the corresponding Cache Item.

**Throws**

`\Luminova\Psr\Cache\Exceptions\InvalidArgumentException` - Throws if the `$key` string is not a legal value.

***

### getItems

Returns a traversable set of cache items.
A Cache item will be returned for each key, even if that key is not found. 
However, if no keys are specified then an empty traversable is returned instead.

```php
public getItems(array<int,string> $keys = []): iterable
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$keys` | **array<int,string>** | An indexed array of keys of items to retrieve. |

**Return Value:**

`iterable` - Return an iterable collection of Cache Items keyed by the cache keys of each item.

**Throws**

`\Luminova\Psr\Cache\Exceptions\InvalidArgumentException` - Throws if any of the keys in `$keys` are not a legal value.

***

### hasItem

Confirms if the cache contains specified cache item.

```php
public hasItem(string $key): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string** | The key for which to check existence. |

**Return Value:**

`bool` - Return true if item exists in the cache, false otherwise.

**Throws**

`\Luminova\Psr\Cache\Exceptions\InvalidArgumentException` - Throws if the `$key` string is not a legal value.

> **Note:** This method MAY avoid retrieving the cached value for performance reasons.
> This could result in a race condition with `CacheItem::get()`. 
> To avoid such situation use `CacheItem::isHit()` instead.

***

### clear

Deletes all items in the pool.

```php
public clear(): bool
```

**Return Value:**

`bool` - Return true if the pool was successfully cleared. False if there was an error.

***

### deleteItem

Removes the item from the pool.

```php
public deleteItem(string $key): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string** | The key to delete. |

**Return Value:**

`bool` - Return true if the item was successfully removed. False if there was an error.

**Throws**

`\Luminova\Psr\Cache\Exceptions\InvalidArgumentException` - Throws if the `$key` string is not a legal value.

***

### deleteItems

Removes multiple items from the pool.

```php
public deleteItems(array<int,string> $keys): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$keys` | **array<int,string>** | An array of keys that should be removed from the pool. |

**Return Value:**

`bool` - Return true if the items were successfully removed. False if there was an error.

**Throws**

`\Luminova\Psr\Cache\Exceptions\InvalidArgumentException` - Throws if any of the keys in `$keys` are not a legal value.

***

### save

Persists a cache item immediately.

```php
public save(\Psr\Cache\CacheItemInterface $item): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$item` | **\Psr\Cache\CacheItemInterface** | The cache item to save. |

**Return Value:**

`bool` - Return true if the item was successfully persisted. False if there was an error.

***

### saveDeferred

Sets a cache item to be persisted later.

```php
public saveDeferred(\Psr\Cache\CacheItemInterface $item): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$item` | **\Psr\Cache\CacheItemInterface** | The cache item to save. |

**Return Value:**

`bool` - Return false if the item could not be queued or if a commit was attempted and failed. True otherwise.

***

### commit

Persists any deferred cache items.

```php
public commit(): bool
```

**Return Value:**

`bool` - Return true if all not-yet-saved items were successfully saved or there were none. False otherwise.

***

### rollback

Rollback deferred transaction if any was failed

```php
public rollback(): bool
```

**Return Value:**

`bool` - Return true if rollback was successful, otherwise false.

***

## Concepts of Cache Item:

The **Cache Item** class encapsulates a single piece of cached data. It stores both the cache value (the actual data) and its associated metadata, such as the cache key, whether the item was found, valid or not (cache hit), and expiration information. Cache items can be saved to, retrieved from, or deleted from the cache pool.

### Initializing the Cache Item Class

To create a new `CacheItem` instance, specify the cache key, the content to store, and optionally the hit state. This allows flexible handling of individual cache items.

```php
<?php
use \Luminova\Psr\Cache\CacheItem;

$key = 'cache_key';
$content = 'This is the cached content';
$isHit = true; // Optional, whether the cache item was a hit or miss

// Create a new CacheItem instance
$item = new CacheItem($key, $content, $isHit);
```

---

# Cache Item Class Definition

* Class namespace: `\Luminova\Psr\Cache\CacheItem`
* This class implements:
[\Psr\Cache\CacheItemInterface](https://www.php-fig.org/psr/psr-6/#cacheiteminterface)

***

## Methods

### constructor

CacheItem Constructor.

```php
public __construct(string $key, mixed $content = null, ?bool $isHit = null): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string** | The unique identifier for the cache item. |
| `$content` | **mixed** | The value to be stored in the cache item (default: null). |
| `$isHit` | **bool\|null** | Determines whether the cache item is considered a "hit" (i.e., the item exists in the cache).<br/> If null, the hit status is automatically determined based on whether the content is null or not. |

***

### getKey

Returns the key for the current cache item.

```php
public getKey(): string
```

**Return Value:**

`string` - Return the key string for this cache item.

***

### getExpiresAt

Gets the absolute expiration time for this cache item.

```php
public getExpiresAt(): ?\DateTimeInterface
```

**Return Value:**

`\DateTimeInterface|null` - DateTimeInterface|null Returns the exact expiration time as a DateTimeInterface object, or null if the item has no expiration set.

***

### getExpiresAfter

Gets the relative expiration time for this cache item.

```php
public getExpiresAfter(): \DateInterval|int|null
```

**Return Value:**

`\DateInterval|int|null` - Returns the expiration time relative to the current time. It may return:

- a `DateInterval` object for specific intervals,
- an integer representing seconds,
- or null if no expiration is set.

***

### get

Retrieves the value of the item from the cache associated with this object's key.

```php
public get(): mixed
```

**Return Value:**

`mixed` - Returns the value corresponding to this cache item's key, or null if not found.

> **Note:** If `isHit` is `false`, this method will return `null`.

***

### isHit

Confirms if the cache item lookup resulted in a cache hit.

```php
public isHit(): bool
```

**Return Value:**

`mixed` - Returns true if the request resulted in a cache hit. False otherwise.

***

### set

Sets the value represented by this cache item.

```php
public set(mixed $value): static
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$value` | **mixed** | The serializable value to be stored. |

**Return Value:**

`\Luminova\Psr\Cache\CacheItem` - Returns instance if cache item.

***

### expiresAt

Sets the expiration time for this cache item.

```php
public expiresAt(?\DateTimeInterface $expiration): static
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$expiration` | **\DateTimeInterface\|null** | The point in time after which the item MUST be considered expired.<br/>If null is passed explicitly, the `expiresAfter` will be used if not null. If `0` is set, the value will be stored permanently. |

**Return Value:**

`\Luminova\Psr\Cache\CacheItem` - Returns instance if cache item.

***

### expiresAfter

Sets the expiration time for this cache item.

```php
public expiresAfter(\DateInterval|int|null $time): static
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$time` | **int&#124;\DateInterval&#124;null** | The period of time from the present after which the item MUST be considered expired.<br/>An integer time in seconds or Date Interval object until expiration. |

**Return Value:**

`\Luminova\Psr\Cache\CacheItem` - Returns instance if cache item.
