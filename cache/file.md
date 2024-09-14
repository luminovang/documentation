# File System Caching Module

***

## Overview

File cache, offers an efficient way to store reusable data in the file system, reducing the need for frequent database operations or third-party API calls.

***

## Introduction

The Luminova `FileCache` class is designed to simplify caching by storing reusable data directly in the file system. This reduces the need for repeated database queries or API requests, enhancing performance when the same content is requested multiple times. It is particularly useful for caching third-party API responses, ensuring efficient data retrieval.

Unlike the `MemoryCache` that uses PHP’s `Memcached` server to store items either locally or remotely, `FileCache` stores all items in a JSON format within the `/writeable/caches/filesystem/` directory. The cache location is fixed, but you can specify a `subfolder` within this directory to organize your items.

All cache items are serialized before storage. The `FileCache` class uses `igbinary_serialize` if available, which provides a compact binary representation of the data. If `igbinary_serialize` is not enabled, the class falls back to PHP's native `serialize`, which is less efficient. For environments where `igbinary_serialize` is not available, it is recommended to enable base64 encoding by setting method `enableBase64` to true for large string content such as `HTML` and `MARKDOWN`, as this helps handle content with new lines more effectively.

> **Note:**  
> The Luminova Database `Builder` class already includes a built-in caching mechanism. You can specify your preferred cache driver in the environment variable file. Additional caching implementations are only necessary if you're working directly with prepared statements or the `Database` connection object.

***

* Class namespace: `\Luminova\Cache\FileCache`
* Parent class: `\Luminova\Base\BaseCache`
* This class is marked as **final** and can't be subclassed

***

## Constants

These are defined constant variables for cache expiration.

| Constant | Visibility | Type | Value |
|:---------|:-----------|:-----|:------|
|`TTL_7DAYS`|public|int|7 * 24 * 60 * 60|
|`TTL_24HR`|public|int|24 * 60 * 60|
|`TTL_30MIN`|public|int|30 * 60|

***

## Methods

You can initialize the cache using the global `cache` helper function:

```php
<?php 
$cache = cache('filesystem');
```

For more information on implementing cache features in your application, refer to the [Cache Examples Documentation](/cache/examples.md).

***

### constructor

Initialize cache constructor, with optional storage name and subfolder.

```php
public __construct(string|null $storage = null, string|null $subfolder = null): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$storage` | **string&#124;null** | The cache storage name (default: null). |
| `$subfolder` | **string&#124;null** | Optional cache storage sub-directory (default: null). |

**Throws:**

- [\Luminova\Exceptions\CacheException](/running/exceptions.md#cacheexception) - if there is a problem loading the cache.

> **Note:** 
> If your didn't specify the storage name on initialization, then you must call `setStorage` method later before accessing caches.

***

### getInstance

Get a static singleton instance of cache class.

```php
public static getInstance(string|null $storage = null, string|null $subfolder = null): static
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$storage` | **string&#124;null** | The cache storage name (default: null). |
| `$subfolder` | **string&#124;null** | Optional cache storage sub directory (default: null). |

**Throws:**

- [\Luminova\Exceptions\CacheException](/running/exceptions.md#cacheexception) - if there is a problem loading the cache.

***

### getPath

Gets the cache full storage filename.

```php
public getPath(): string
```

**Return Value:**

`string` - Return the cache storage filename.

***

### getRoot

Gets the cache storage root directory.

```php
public getRoot(): string
```

**Return Value:**

`string` - Return the cache storage directory.

***

### setPath

Set cache storage sub directory path to store cache items.

```php
public setPath(string $subfolder): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$subfolder` | **string** | The cache storage root directory. |

**Return Value:**

`self` - Return instance of file cache class.

**Throws:**

- [\Luminova\Exceptions\InvalidArgumentException](/running/exceptions.md#invalidargumentexception) - Throws if invalid sub directory is provided.

***

### setStorage

Sets the cache storage name.

```php
public setStorage(string $storage): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$storage` | **string** | The cache storage name. |

**Return Value:**

`self` - Return instance of memory cache class.

**Throws:**

- [\Luminova\Exceptions\CacheException](/running/exceptions.md#cacheexception) - Throws if cannot load cache or unable to process items.
- [\Luminova\Exceptions\InvalidArgumentException](/running/exceptions.md#invalidargumentexception) - Throws if empty cache storage name is provided.

***

### setExpire

Sets the expiration time of the cache item.

```php
public setExpire(\DateTimeInterface|int|null $expiration): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$expiration` | **\DateTimeInterface&#124;int&#124;null** | The expiration time of the cache item. |

**Return Value:**

`self` - Return instance of memory cache class.

> **Note:** When set to null, the item will never expire, when set to 0, it automatically expires immediately.

***

### expiresAfter

Sets the expiration time of the cache item relative to the current time.

```php
public expiresAfter(\DateInterval|int|null $after): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$after` | **\DateInterval&#124;int&#124;null** | The expiration time in seconds or as a DateInterval. |

**Return Value:**

`self` - Return instance of memory cache class.

***

### setLock

Sets the cache lock to avoid deletion even when cache has expired.

```php
public setLock(bool $lock): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$lock` | **bool** | lock flag to be used. |

**Return Value:**

`self` - Return instance of memory cache class.

***

### hashStorage

Generate hash storage name for cache.

```php
public static hashStorage(string $storage): string
```

This method will generate a hash value of storage name which is same as the one used in storing cache items.

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$storage` | **string** | The cache storage name to hash. |

**Return Value:**

`string` - Return a hashed value of the specified storage name.

**Throws:**

- [\Luminova\Exceptions\InvalidArgumentException](/running/exceptions.md#invalidargumentexception) - Throws if empty cache storage name is provided.

***

### enableBase64

Set enable or disable if the cache content should be encoded in base64.

```php
public enableBase64(bool $encode): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$encode` | **bool** | Enable or disable base64 encoding. |

**Return Value:**

`self` - Return instance of file cache class.

***

### enableDeleteExpired

Set enable or disable automatic deletion for expired caches.

```php
public enableDeleteExpired(bool $allow, bool $includeLocked = false): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$allow` | **bool** | The deletion flag. |
| `$includeLocked` | **bool** | Optional specify whether to also delete locked caches (default: true). |

**Return Value:**

`self` - Return instance of file cache class.

***

### onExpired

Retrieve the cache content, using the closure callback to update the content with new item if cache has expired.
The closure is used to update new content when cache is expired, so the closure won't be executed until cache expires or empty.

```php
public onExpired(string $key, \Closure $callback): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string** | The cache key, non-empty string. |
| `$callback` | **\Closure** | Callback function to refreshed cache when expired.<br />-   The callback must return content to be cached. |

**Return Value:**

`mixed` - Return cache content currently stored under key.

**Throws:**

- [\Luminova\Exceptions\CacheException](/running/exceptions.md#cacheexception) - Throws if called without any storage name specified or unable to store cache.
- [\Luminova\Exceptions\InvalidArgumentException](/running/exceptions.md#invalidargumentexception) - Throws if empty cache key is provided.

> **Note:** 
> This method uses the expiration set using `setExpire`, `expiresAfter` and locking for `setLock` method.
> Additionally, this method will return only the the cache contents, metadata will be included.

***

### execute

Retrieve cache items for the given keys. If items are successfully retrieved, an optional callback can be invoked
with the retrieved data. Use `getItems` or `getNext` to access the retrieved items, or call `reset` to reset the iterator position to the start.

If `callback` provided, it will be called with each retrieved item. 
The callback should accept two parameters: the cache `instance` and an `array` representing the retrieved cache item.

```php
public execute(array&lt;int,string&gt; $keys, bool $withCas = false, callable|null $callback = null): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$keys` | **array<int,string>** | The array of cache keys to retrieve. |
| `$withCas` | **bool** | Optional memcached specific feature. If true, CAS tokens will be used for retrieval (default: false). |
| `$callback` | **callable&#124;null** | Optional callback function (default: null). |

**Return Value:**

`bool` - Returns true if the items were successfully retrieved, false otherwise.

**Throws:**

- [\Luminova\Exceptions\InvalidArgumentException](/running/exceptions.md#invalidargumentexception) - Throws if keys is empty array.

> **Note:**
> The callback function should accept two parameters: the cache instance and an array of the retrieved cache item.
> To modify the item and have the changes reflected in `getItems` or `getNext`, pass the item array by reference.

***

### replace

Replace cache content with new data and expiration if necessary.
This method replaces the existing cache item identified by the provided key with new content.

```php
public replace(string $key, mixed $content): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string** | The cache key to replace content. |
| `$content` | **mixed** | The new content to update. |

**Return Value:**

`bool` - Return true if item was successfully updated, otherwise false.

**Throws:**

- [\Luminova\Exceptions\CacheException](/running/exceptions.md#cacheexception) - Throws if called without any storage name specified or unable to store cache.
- [\Luminova\Exceptions\InvalidArgumentException](/running/exceptions.md#invalidargumentexception) - Throws if empty cache key is provided.

> **Note:** This method uses the expiration set using `setExpire`, `expiresAfter` and locking for `setLock` method.

***

### getItem

Retrieve cache content from storage.
This method retrieves the cache content associated with the specified key.
Optionally, you can choose to return only the cached data or include metadata.

```php
public getItem(string $key, bool $onlyContent = true): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string** | The cache key to retrieve content for. |
| `$onlyContent` | **bool** | Whether to return only the cache content or include metadata (default: true). |

**Return Value:**

`mixed` - Returns the cache content if the key is valid and not expired otherwise, returns null if `$onlyContent` is true.

**Throws:**

- [\Luminova\Exceptions\CacheException](/running/exceptions.md#cacheexception) - Throws if called without any storage name specified or error occurred while reading cache.
- [\Luminova\Exceptions\InvalidArgumentException](/running/exceptions.md#invalidargumentexception) - Throws if empty cache key is provided.

***

### getItems

Retrieves all items from the delay `execute` method.
This method will return an array of all items if the iterator contains items.

```php
public getItems(): array|false
```

**Return Value:**

`array|false` - Return an array of items or false if no items are available.

> **Note:** Before calling this method, you must first call the `execute` method.

***

### getNext

Retrieves the next item from the delay `execute` method if available.

```php
public getNext(): array|false
```

**Return Value:**

`array|false` - Return the next item or false if there are no more items.

> **Note:** before calling this method, you must first call the `execute` method.

***

### reset

Resets the execute iterator to the first item.

```php
public reset(): void
```

***

### set

Save item to cache storage.

```php
public set(string $key, mixed $content): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string** | The cache key to use. |
| `$content` | **mixed** | The content to store in cache. |

**Return Value:**

`bool` - Return true if cache was saved, otherwise false.

**Throws:**

- [\Luminova\Exceptions\CacheException](/running/exceptions.md#cacheexception) - Throws if called without any storage name specified or unable to store cache.
- [\Luminova\Exceptions\InvalidArgumentException](/running/exceptions.md#invalidargumentexception) - Throws if empty cache key is provided.

> **Note:** This method uses the options set using `setExpire`, `expiresAfter` and locking for `setLock` method.

***

### setItem

Save an item to cache storage.
This method stores the specified contents in the cache associated with the given key, expiration and optional locking to avoid deletion.

```php
public setItem(
    string $key, 
    mixed $content, 
    \DateTimeInterface|int|null $expiration, 
    \DateInterval|int|null $expireAfter = null, 
    bool $lock = false
): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string** | The cache key to use. |
| `$content` | **mixed** | The content to store in the cache. |
| `$expiration` | **\DateTimeInterface&#124;int&#124;null** | The cache expiration time.<br />If is provided, it will set the cache to expire after the specified duration. <br />If 0, the cache does not expire. |
| `$expireAfter` | **\DateInterval&#124;int&#124;null** | Specifies the exact cache expiration time after a certain period. <br />If null, it uses the $expiration parameter. |
| `$lock` | **bool** | Whether to lock the cache item to prevent deletion even when it expires (default: false). |

**Return Value:**

`bool` - Returns true if the cache was successfully saved; otherwise, false.

**Throws:**

- [\Luminova\Exceptions\CacheException](/running/exceptions.md#cacheexception) - Throws if called without any storage name specified or unable to store cache.
- [\Luminova\Exceptions\InvalidArgumentException](/running/exceptions.md#invalidargumentexception) - Throws if empty cache key is provided.

***

### hasItem

Check if a cache key exists in storage.
This method verifies if the cache item identified by the given key exists in the cache storage,
it doesn't check expiration.

```php
public hasItem(string $key): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string** | The cache key to check. |

**Return Value:**

`bool` - Returns true if the cache key exists, otherwise false.

***

### isLocked

Check if a cache item is locked to prevent deletion.

This method determines if the cache item identified by the given key is locked,
which prevents it from being deleted. If the cache item does not exist, it is considered locked by default.

```php
public isLocked(string $key): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string** | The cache key to check. |

**Return Value:**

`bool` - Returns true if the item is locked or does not exist, otherwise, false.

***

### hasExpired

Determine if a cache item has expired.
This method checks if the cache item identified by the given key has expired
based on its timestamp and expiration settings.

```php
public hasExpired(string $key): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string** | The cache key to check for expiration. |

**Return Value:**

`bool` - Returns true if the cache item has expired, otherwise false.

> **Note:** If the cache key does not exist, it is considered expired and return true.

***

### deleteItem

Delete a specific cache item by key.
This method removes the cache item associated with the given key.

```php
public deleteItem(string $key, bool $includeLocked = false): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string** | The cache key of the item to delete. |
| `$includeLocked` | **bool** | Whether to delete this item if it's locked (default: false). |

**Return Value:**

`bool` - Returns true if the cache item was successfully deleted, otherwise false.

**Throws:**

- [\Luminova\Exceptions\CacheException](/running/exceptions.md#cacheexception) - Throws if called without any storage name specified or unable to store cache.

***

### deleteItems

Delete multiple cache items by their keys.
This method removes multiple cache items specified by an array of keys.

```php
public deleteItems(iterable&lt;int,string&gt; $keys, bool $includeLocked = false): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$keys` | **iterable<int,string>** | The array of cache keys to delete. |
| `$includeLocked` | **bool** | Whether to delete the item if it's locked (default: false).<br />- Setting this parameter to true will apply to all keys. |

**Return Value:**

`bool` - Returns true if at least one cache item was successfully deleted; otherwise, false.

**Throws:**

- [\Luminova\Exceptions\CacheException](/running/exceptions.md#cacheexception) - Throws if called without any storage name specified or unable to store cache.

***

### flush

Invalidate all cached items in the current cache storage directory and reset the internal pre-cached instance array to an empty state.

```php
public flush(): bool
```

**Return Value:**

`bool` - Returns true if the cache was successfully flushed, otherwise false.

***

### clear

Clear all cached items in the current storage name and reset the internal pre-cached instance array to an empty state.

```php
public clear(): bool
```

**Return Value:**

`bool` - Returns true if the cache was successfully cleared, otherwise false.

***

### delete

Delete multiple cache items associated with given keys from a specified storage.
This method removes cache items based on their keys from the specified storage, which is hashed to ensure proper storage distribution.

```php
public delete(string $storage, string[] $keys): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$storage` | **string** | The storage identifier for which cache items should be deleted. |
| `$keys` | **string[]** | The array of cache keys to be deleted. |

**Return Value:**

`bool` - Returns true if the cache items were successfully deleted; otherwise, false.

**Throws:**

- [\Luminova\Exceptions\CacheException](/running/exceptions.md#cacheexception) - Throws if error occurred while deleting items.

> **Note:** This method will remove item even if its was locked to prevent deletion.