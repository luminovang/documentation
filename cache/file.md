# File Cache

***

## Overview

File Cache offers an efficient way to store reusable data in the file system, reducing the need for frequent database operations or third-party API calls.

***

## Introduction

The Luminova `FileCache` system allows you to store reusable data in the file system, reducing the need for repeated database queries or API requests. By caching responses, you can enhance performance and efficiency when the same content is requested multiple times.

Additionally, cache can also be useful for third-party API requests. By caching responses, you can enhance performance and efficiency when the same content is requested multiple times.

> **Note:**  
> The Luminova Database `Builder` class already includes a built-in caching mechanism. You only need to implement additional caching if you are working with prepared statements or directly with the `Database` connection object.

***

* Class namespace: `\Luminova\Cache\FileCache`
* This class is marked as **final** and can't be subclassed
* This class is a **Final class**

***

## Constants

| Constant | Visibility | Type | Value |
|:---------|:-----------|:-----|:------|
|`TTL_7DAYS`|public|int|7 * 24 * 60 * 60|
|`TTL_24HR`|public|int|24 * 60 * 60|
|`TTL_30MIN`|public|int|30 * 60|
|`PHP`|public|string|&quot;.catch.php&quot;|
|`JSON`|public|string|&quot;.json&quot;|
|`TEXT`|public|string|&quot;.txt&quot;|

## Methods

### constructor

Initialize cache constructor, with optional storage name and subfolder.

```php
public __construct(string|null $storage = null, string $folder = ''): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$storage` | **string&#124;null** | cache storage filename to hash. |
| `$folder` | **string** | cache storage sub folder. |

***

### getInstance

Get static Singleton Class.

```php
public static getInstance(string|null $storage = null, string $folder = ''): static
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$storage` | **string&#124;null** | cache storage filename to hash |
| `$folder` | **string** | cache storage sub folder. |

***

### setPath

Set the new cache directory path.

```php
public setPath(string $path): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$path` | **string** | cache directory must end with. |

**Return Value:**

`self` - Return class instance.

***

### setStorage

Sets the new cache file name.

```php
public setStorage(string $storage): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$storage` | **string** | cache storage filename, |

**Return Value:**

`self` - Return class instance.

***

### setExtension

Set the cache file extension type to use in storing cache.

```php
public setExtension(string $extension): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$extension` | **string** | The file extension. |

**Return Value:**

`self` - Return class instance.

***

### setExpire

Set the expiration time of the cache item.

```php
public setExpire(\DateTimeInterface|null $expiration): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$expiration` | **\DateTimeInterface&#124;null** | The expiration time of the cache item. |

**Return Value:**

`self` - The current instance.

***

### expiresAfter

Sets the expiration time of the cache item relative to the current time.

```php
public expiresAfter(int|\DateInterval|null $time): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$time` | **int&#124;\DateInterval&#124;null** | The expiration time in seconds or as a DateInterval. |

**Return Value:**

`self` - The current instance.

***

### setLock

Sets the cache lock mode, to avoid deletion even when cache has expired.

```php
public setLock(bool $lock): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$lock` | **bool** | lock flag to be used. |

**Return Value:**

`self` - Return class instance.

***

### enableBase64

Enable the cache to store data in base64 encoded.

```php
public enableBase64(bool $encoding): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$encoding` | **bool** | Enable base64 encoding or disable. |

**Return Value:**

`self` - Return class instance.

***

### enableDeleteExpired

Enable automatic cache deletion if expired.

```php
public enableDeleteExpired(bool $allow): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$allow` | **bool** | Auto deletion flag. |

**Return Value:**

`self` - Return class instance.

***

### enableSecureAccess

Enable cache file access security for caches stored in `PHP` file extension.

```php
public enableSecureAccess(bool $secure): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$secure` | **bool** | The secure flag to use. |

**Return Value:**

`self` - Return class instance.

***

### getPath

Gets Combines directory, filename and extension into a full filepath.

```php
public getPath(): string
```

**Return Value:**

`string` - Return full cache path.

***

### onExpired

Read cache content, of update the content with new item if it has expired.

```php
public onExpired(string $key, \Closure $callback): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string** | cache key. |
| `$callback` | **\Closure** |  |

**Return Value:**

`mixed` - Return cache content currently stored under key.

**Throws:**

- [\Luminova\Exceptions\ErrorException](/exceptions/classes.md#errorexception) - if the file cannot be saved

***

### refresh

Refresh cache content with new data and expiration if necessary.

```php
public refresh(string $key, mixed $content, int $expiration, int $expireAfter = null, bool $lock = true): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string** | cache key. |
| `$content` | **mixed** | New content to update. |
| `$expiration` | **int** | cache expiry time. |
| `$expireAfter` | **int** | cache expiry time after. |
| `$lock` | **bool** | lock catch to avoid deletion even when cache time expired. |

**Return Value:**

`bool` - Return true if item was successfully updated, otherwise false.

**Throws:**

- [\Luminova\Exceptions\ErrorException](/exceptions/classes.md#errorexception) - if the file cannot be saved

***

### getItem

Get cache content from disk

```php
public getItem(string $key, bool $onlyContent = true): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string** | cache key |
| `$onlyContent` | **bool** | Weather to return only cache content or with metadata (default: true). |

**Return Value:**

`mixed` - Returns data if key is valid and not expired, NULL otherwise

**Throws:**

- [\Luminova\Exceptions\ErrorException](/exceptions/classes.md#errorexception) - if the file cannot be saved

***

### create

Creates, Reloads and retrieve cache once class is created

```php
public create(): self
```

**Return Value:**

`self` - Return cache class instance.

**Throws:**

- [\Luminova\Exceptions\ErrorException](/exceptions/classes.md#errorexception) - if there is a problem loading the cache

***

### hasItem

Checks if cache key exist.

```php
public hasItem(string $key): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string** | The cache key. |

**Return Value:**

`bool` - Return true if cache key exists, otherwise false.

***

### deleteIfExpired

Remove expired cache by key

```php
public deleteIfExpired(): int
```

**Return Value:**

`int` - Return number of deleted expired items.

***

### deleteItem

Deletes cache item associated with a given key.

```php
public deleteItem(string $key): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string** | The cache key to delete. |

**Return Value:**

`bool` - Return true if cache was successfully deleted, otherwise false.

***

### deleteItems

Delete cache by array keys.

```php
public deleteItems(iterable $keys): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$keys` | **iterable** | The array of cache keys. |

**Return Value:**

`bool` - Return true if cache was successfully deleted, otherwise false.

***

### removeList

Remove a list of items from the collection.

```php
public removeList(iterable&lt;int,string&gt; $iterable): \Generator
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$iterable` | **iterable<int,string>** | An iterable list of keys representing items to be removed. |

**Return Value:**

`\Generator` - Yields the result of the delete operation for each item.

**Throws:**

- [\Luminova\Exceptions\ErrorException](/exceptions/classes.md#errorexception) - if the file cannot be saved.

***

### hasExpired

Checks if the cache timestamp has expired by key

```php
public hasExpired(string $key): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string** | cache key |

**Return Value:**

`bool` - Return true if cache has expired, otherwise false.

***

### setItem

Builds cache data and save it.

```php
public setItem(string $key, mixed $data, int|\DateTimeInterface|null $expiration, int|\DateInterval|null $expireAfter = null, bool $lock = false): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string** | The cache keys. |
| `$data` | **mixed** | The cache contents. |
| `$expiration` | **int&#124;\DateTimeInterface&#124;null** | cache expiration time. |
| `$expireAfter` | **int&#124;\DateInterval&#124;null** | cache expiration time after. |
| `$lock` | **bool** | Lock catch to avoid deletion even when cache time expired. |

**Return Value:**

`bool` - Return true if cache was saved, otherwise false.

**Throws:**

- [\Luminova\Exceptions\ErrorException](/exceptions/classes.md#errorexception) - if the file cannot be saved

***

### clear

Wipes clean the entire cache's.

```php
public clear(bool $clearDisk = false): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$clearDisk` | **bool** | Whether to clear all cache disk |

**Return Value:**

`bool` - Return true if cache was successfully cleared, false otherwise.

***

### clearStorage

Remove current cache file

```php
public clearStorage(): bool
```

**Return Value:**

`bool` - Return true if file path exist else false

***

### delete

Remove cached storage file from disk with full path.

```php
public delete(string $storage, string $extension = self::JSON): bool
```

This will use the current storage path

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$storage` | **string** | cache storage names |
| `$extension` | **string** | cache file extension type |

**Return Value:**

`bool` - Return true on success, false on failure.

***

### deleteDisk

Remove cache file from disk with full path

```php
public static deleteDisk(string $path, string $storage, string $extension = self::JSON): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$path` | **string** | cache full path / |
| `$storage` | **string** | cache file array names |
| `$extension` | **string** | cache file extension type |