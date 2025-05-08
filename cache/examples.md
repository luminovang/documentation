# Cache Implementation Examples

***

## Overview

This documentation provides a clear and concise guide on implementing caching, covering initialization, storing, and retrieving content for both Memcached and File System cache.

***

## Introduction

In this guide, we demonstrate how to efficiently use Luminova's [File System Cache](/cache/file.md) and [Memory Cache](/cache/memory.md) to store and retrieve reusable data, reducing the need for frequent database or API calls.

### Initialization

You can initialize the `FileCache` or `MemoryCache` using the global `cache()` helper function. Specify the cache storage name and folder for file storage, or the persistent ID for Memcached:

```php
<?php
$cache = cache('filesystem', 'storage_name', 'cache_folder_name'); // FileCache
$cache = cache('memcached', 'storage_name', 'cache_id'); // MemoryCache
```

Alternatively, you can directly instantiate the cache classes:

```php
<?php
use \Luminova\Cache\FileCache;
use \Luminova\Cache\MemoryCache;

$fileCache = new FileCache('storage_name', 'cache_folder_name');
$memoryCache = new MemoryCache('storage_name', 'cache_id');
```

***

### Dynamic Initialization

If the storage name is not specified during initialization, you can set it later before performing any cache operations:

```php
<?php
$cache->setStorage('cache_name');
```

***

### Storing and Retrieving

Use the `onExpired()` method to retrieve content from the cache or execute a callback if the cache has expired or does not exist:

```php
<?php
$content = $cache->onExpired('cache_key', static function() {
    return databaseLoadContent();
});
```

***

### Getting Cached Content

Retrieve the cached content using the `getItem()` method:

```php
<?php
$content = $cache->getItem('cache_key');
```

***

### Delayed Retrieval

To retrieve multiple items asynchronously, use the `execute()` method with an optional callback:

```php
<?php
use \Luminova\Base\BaseCache;

$cache->execute(['key1', 'key2'], false, function(BaseCache $instance, array &$result) {
    // Modify the cache result if needed
    $result['value'] = strtoupper($result['value']);
});

print_r($cache->getItems());
print_r($cache->getNext());
```

***

### Replacing Cache Content

Replace the content of an existing cache entry using the `replace()` method:

```php
<?php
$data = 'New content to update';

if ($cache->replace('cache_key', $data)) {
    // Cache updated successfully
}
```

***

### Saving Content

Save content to the cache. The `lock` parameter determines whether the cache should prevent automatic expiration and deletion:

```php
<?php
$data = 'Content to store';

if ($cache->setItem('cache_key', $data, 1200, 1200, true)) {
    // Cache saved successfully
}
```

You can also use predefined options for saving content:

```php
<?php
$data = 'Content to store';
$cache->setExpiry(1200);
$cache->setLock(true);

if ($cache->set('cache_key', $data)) {
    // Cache saved successfully
}
```

***

### Delete Cache Content:

To delete a specific cache item.

```php
<?php
if ($cache->deleteItem('cache_key')) {
    // Cache item deleted
}
```

***

### Delete Multiple Items:

To delete a specified cache keys.

```php
<?php
if ($cache->deleteItems(['key1', 'key2'])) {
    // Cache items were deleted
}
```

***

### Clear All Cache Content:

To clear all items in the cache storage.

```php
<?php
if ($cache->clear()) {
    // All cache items cleared
}
```

***

### Flush All Cache Content:

To clear all items in the cache directory or cache server.

```php
<?php
if ($cache->flush()) {
    // All cache items flushed
}
```

***

### Check if Cache Exists:

To check if a specific cache key exists.

```php
<?php
if ($cache->hasItem('cache_key')) {
    // Cache item exists
}
```