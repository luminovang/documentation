# PSR Cache Interface

***

## Overview

Luminova's PSR cache implementations for both cache pool and simple cache.

***

## Introduction

To utilize Luminova `FileCache` with [PSR implementations](https://www.php-fig.org/psr/psr-6/), you must first install our [PSR-compatible package](https://github.com/nanoblocktech/psr-cache/), which supports both `CachePool` and `SimpleCache`.

***

### Installation

Installation is available via Composer.

```bash
composer require nanoblocktech/psr-cache
```

***

### Cache Pool Examples

Below are examples demonstrating how to set up and use the cache pool which adhara to `PSR` cache standard.

**Initialize the Cache Pool:**

First, create a cache pool instance by specifying a cache name and storage folder name.

```php
<?php
use \Luminova\Psr\Cache\CachePool;

$pool = new CachePool('my_cache', 'my_cache_folder_name');
```

**Set a Cache Item:**

Store data in the cache by creating a cache item, setting its value, and saving it to the cache pool.

```php 
<?php
$item = $pool->getItem('cache_key');
$item->set('This is my cache data');
$pool->save($item);
```

**Get a Cache Item:**

Retrieve the stored data from the cache using the cache key.

```php
<?php
$data = $pool->getItem('cache_key')->get();
```

**Check if the Cache Item Exists:**

Check whether a cache item exists and, if not, load the data from the database, set the cache item with an expiration time, and save it to the cache pool. 
If the cache item exists, use the cached data directly.

```php
<?php
use \DateInterval;

$item = $pool->getItem('cache_key');

if (!$item->isHit()) {
    // Data is not in the cache, load from the database
    $data = databaseLoadData();
    
    // Set the cache item with the loaded data and expiration time
    $item->set($data);
    $item->expiresAfter(new DateInterval('PT1H')); // Cache expires in 1 hour
    
    // Save the cache item to the cache pool
    $pool->save($item);
} else {
    // Data exists in the cache, use it directly
    $data = $item->get();
}
```

**Retrieve an Item from the Cache:**

Fetch a single cache item using its key.

```php
$pool->getItem('cache_key'): CacheItem;
```

**Retrieve Multiple Cache Items:**

Fetch multiple cache items at once using an array of keys.

```php
<?php
$pool->getItems(['key1', 'key2']): iterable<key, CacheItem>;
```

**Check if an Item Exists in the Cache:**

Determine whether a cache item exists using its key.

```php
<?php
if($pool->hasItem('cache_key')){
    // Cache item exists
}
```

**Persist a Cache Item Immediately:**

Save a cache item immediately.

```php
<?php
$pool->save(CacheItemInterface $item): bool;
```

**Save a Deferred Cache Item:**

Save a cache item for later persistence.

```php
<?php
$pool->saveDeferred(CacheItemInterface $item);
```

**Commit Deferred Cache Items:**

Persist any deferred cache items that have been saved.

```php
<?php
$pool->commit();
```

**Rollback Deferred Cache Items:**

Rollback if any deferred commit failed to save, without recommitting.

```php
<?php
$pool->rollback();
```

**Delete an Item from the Cache:**

Remove a single cache item using its key.

```php
<?php
$pool->deleteItem('cache_key');
```

**Delete Multiple Items from the Cache:**

Remove multiple cache items at once using an array of keys.

```php
<?php
$pool->deleteItems(['key1', 'key2']);
```

**Clear All Cached Entries:**

Remove all items from the cache.

```php
<?php
$pool->clear();
```

***

### Simple Cache Examples

The Luminova `SimpleCache` class provides an easy-to-use interface for caching operations, adhering to the `PSR-16` Simple Cache standard.

**Initialize the SimpleCache:**

Create a new instance of the `SimpleCache` class by specifying a cache name and folder.

```php
<?php
use \Luminova\Psr\Cache\SimpleCache;

$simple = new SimpleCache('my_cache', 'my_cache_folder_name');
```

**Set a Cache Item:**

Store data in the cache by setting a cache item.

```php
<?php
$data = $simple->get('cache_key', 'DEFAULT_NO_DATA');
if($data === 'DEFAULT_NO_DATA'){
    $data = 'This is my cache data';
    $simple->set('cache_key', $data);
}
echo $data;
```

**Get a Cache Item:**

Retrieve a cache item using its key, with an optional default value if the key does not exist.

```php
<?php
$data = $simple->get('cache_key', 'default_value');
```

**Delete a Cache Item:**

Remove a cache item from the cache.

```php
<?php
$simple->delete('cache_key');
```

**Clear All Cache Items:**

Remove all items from the cache.

```php
<?php
$simple->clear();
```

**Check if a Cache Item Exists:**

Determine whether a cache item exists.

```php
<?php
if ($simple->has('cache_key')) {
    // Cache item exists
}
```

**Set Multiple Cache Items:**

Store multiple cache items at once.

```php
<?php
$simple->setMultiple([
    'key1' => 'value1',
    'key2' => 'value2',
], new DateInterval('PT1H'));
```

**Get Multiple Cache Items:**

Retrieve multiple cache items at once, with optional default values.

```php
<?php
$items = $simple->getMultiple(['key1', 'key2'], 'default_value');
```

**Delete Multiple Cache Items:**

Remove multiple cache items at once.

```php
<?php
$simple->deleteMultiple(['key1', 'key2']);
```

***

### CacheItem Methods

The `CacheItem` class allows you to handle individual cache items with ease. Below are the methods available and their descriptions:

**Initialize the Class:**

Create a new instance of the `CacheItem` class with a specified key, content to save, and optionally, the hit state.

```php
$item = new CacheItem($key, $content, $isHit);
```

**Retrieve the Key of the Cache Item:**

Get the key associated with the cache item.

```php
$item->getKey();
```

**Retrieve the Value of the Cache Item:**

Get the value stored in the cache item.

```php
$item->get();
```

**Check if the Cache Item is a Hit:**

Determine if the cache item is available in the cache.

```php
$item->isHit();
```

**Set the Value of the Cache Item:**

Assign a value to the cache item.

```php
$item->set($value);
```

**Set the Expiration Time of the Cache Item:**

Specify the exact expiration time for the cache item.

```php
$item->expiresAt($expiration);
```

**Set the Expiration Time Relative to the Current Time:**

Define the expiration time for the cache item relative to the current time.

```php
$item->expiresAfter($time);
```