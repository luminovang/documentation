# In-Memory Caching Module

***

## Overview

The MemoryCache is an enhanced extension of Memcached, designed to simplify caching operations by leveraging local and remote servers to reduce the need for repetitive database queries.

***

## Introduction

The Luminova `MemoryCache` class extends PHP's `Memcached` and simplifies caching by storing and retrieving cached content on the system's hard disk. This class enables you to cache reusable data locally or remotely, reducing the need for repetitive database queries or API calls. By caching responses, you can improve performance and efficiency when the same content is requested multiple times.

The `MemoryCache` class shares core methods with the `FileCache` class, making it easy to switch between memory and file system caching without altering existing code. However, the `MemoryCache` class includes some unique methods not available in `FileCache`, and vice versa.

> **Note:**  
> The Luminova Database `Builder` class already includes a built-in caching mechanism. You can specify your preferred cache driver in the environment variable file. Additional caching implementations are only necessary if you're working directly with prepared statements or the `Database` connection object.

***

* Class namespace: `\Luminova\Cache\MemoryCache`
* Parent class: `Luminova\Base\BaseCache`
* This class is marked as **final** and can't be subclassed

***

## Setups
To setup the default memcached connection server, these are the [environment variable keys](/running/env.md) required:

- `memcached.persistent.id = null` **(string|null)** - Your application persistent Id.
- `memcached.key.prefix = null` **(string|null)** - Your application cache key prefix.
- `memcached.host = localhost` **(string)** - Your application cache hostname or IP address.
- `memcached.port = 11211` **(int)** - Your application port number on which the cache server is listening.
- `memcached.server.weight = 0` **(int)** - The server connection weight.

***

## Methods

You can initialize the cache using the global `cache` helper function:

```php
<?php 
$cache = cache('memcached');
```

For more information on implementing cache features in your application, refer to the [Cache Examples Documentation](/cache/examples.md).

***

### constructor

Initializes memcache extension class instance with an optional storage name and an optional persistent ID.

```php
public __construct(string|null $storage = null, string|null $persistent_id = null): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$storage` | **string&#124;null** | The cache storage name (default: null). If null, you must call `create` method later. |
| `$persistent_id` | **string&#124;null** | Optional unique ID for persistent connections (default: null).<br />- If null is specified, will used the default persistent id from environment variables.<br />- If not set in environment variables `default` will be used instead. |

**Throws:**

- [\Luminova\Exceptions\CacheException](/running/exceptions.md#cacheexception) - if there is an issue loading the cache.

***

### getInstance

Retrieves or creates a singleton instance of the memcache extension class.

```php
public static getInstance(string|null $storage = null, string|null $persistent_id = null): static
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$storage` | **string&#124;null** | The cache storage name (default: null). If null, you must call `create` method later. |
| `$persistent_id` | **string&#124;null** | Optional unique ID for persistent connections (default: null).<br />- If null is specified, will used the default persistent id from environment variables.<br />- If not set in environment variables `default` will be used instead. |

**Return Value:**

`static` - The singleton instance of the cache.

**Throws:**

- [\Luminova\Exceptions\CacheException](/running/exceptions.md#cacheexception) - if there is an issue loading the cache.

***

### getConn

Retrieves the current connection instance of the cache Memcached being used.

```php
public getConn(): \Memcached|null
```

**Return Value:**

`\Memcached|null` - Return the instance of Memcached, otherwise null.

***

### getId

Retrieves the current connection persistent id being used.

```php
public getId(): string|null
```

**Return Value:**

`string|null` - Return the persistent d=id, otherwise null.

***

### setServer

Adds a server to the cache configuration.

```php
public setServer(string $host, int $port, int $weight): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$host` | **string** | The server hostname or IP address. |
| `$port` | **int** | The server port number. |
| `$weight` | **int** | Optional weight for the server (default: 0). |

**Return Value:**

`self` - Returns the memory cache instance.

> **Note:** After setting server you should call `reconnect` method to connect to new servers.

***

### setServers

Sets multiple servers in the cache configuration.

```php
public setServers(array&lt;int,string|int&gt; $config): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$config` | **array<int,string&#124;int>** | An array of server configurations where each element is an array [host, port, weight]. |

**Return Value:**

`self` - Returns the memory cache instance.

> **Note:** After setting servers you should call `reconnect` method to connect to new servers.

***

### setOption

Sets an option for the cache.

```php
public setOption(int $option, mixed $value): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$option` | **int** | The option to set. |
| `$value` | **mixed** | The value for the option. |

**Return Value:**

`self` - Returns the memory cache instance.

***

### connect

Connects to the Memcached server(s) using the configured settings.
Initializes a connection to the Memcached server(s) with the configured host, port, and weight, or using the provided server configuration.

```php
public connect(): bool
```

**Return Value:**

`bool` - Returns true if the connection is successful, false otherwise.

***

### reconnect

Reconnect, closes the connection to the memcached server and initialize a new connection.

```php
public reconnect(): bool
```

**Return Value:**

`bool` - Returns true if the connection is successful, otherwise false.

***

### disconnect

Closes the connection to the memcached server.
If no connection exists, the method will return `true` immediately.

```php
public disconnect(): bool
```

**Return Value:**

`bool` - Returns `true` if the disconnection is successful or if no connection was open.

***

### is

Check if result code matches with a given code.

```php
public is(int $resultCode = Memcached::RES_SUCCESS): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$resultCode` | **int** | The memcached result code (default: Memcached::RES_SUCCESS). |

**Return Value:**

`bool` - Returns true if the result code matches, otherwise false.

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

Invalidate all cached items in the current memcached server and reset the internal pre-cached instance array to an empty state.

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
This method removes cache items based on their keys from the specified storage, which
is hashed to ensure proper storage distribution.

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