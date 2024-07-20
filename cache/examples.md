# File Cache Examples

***

## Overview

This documentation provides a clear and concise guide on how to use the `FileCache` class, covering initialization, storing, and retrieving content.

***

## Introduction

In this documentation, we show the basic usages of the [Luminova FileCache](/cache/file.md), to efficiently store and retrieve reusable data in the file system, reducing the need for frequent database or API calls.

**Initialization:**

Create an instance of `FileCache` with specified cache storage and folder names.

```php
<?php
use \Luminova\Cache\FileCache;

$cache = new FileCache('cache_name', 'check_folder_name');
```

**Dynamic Initialization:**

If storage is not specified during initialization, set the storage name later and then call `create` method to enable the cache.

```php
<?php
use \Luminova\Cache\FileCache;

$cache = new FileCache();
$cache->setStorage('cache_name');
$cache->create();
```

**Store and Retrieve Content:**

Use the callback method to fetch content if the cache has expired or does not exist.

```php
<?php
$content = $cache->onExpired('cache_key', static function(){
    return databaseLoadContent();
});
```

**Get Cached Content:**

Retrieve content from the cache.

```php
<?php
$content = $cache->getItem('cache_key');
```

**Force Update Cache Content:**

Update cache content forcibly.

```php
<?php
$data = 'New content to update';

if ($cache->refresh('cache_key', $data, 60 * 20)) {
    // Cache updated
}
```

**Save Content:**

Save content to the cache. The fourth parameter `lock` indicates whether the cache should allow automatic expiration and deletion.

```php
<?php
$data = 'New content to update';

if ($cache->setItem('cache_key', $data, 60 * 20, 60 * 20, true)) {
    // Cache saved
}
```