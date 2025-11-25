# Database Connection Configs and Failover

***

## Overview

Define connection servers to establish the primary database connection, and configure failover to automatically reconnect to the next available server when the main database fails. Sharding is also...

***

## Introduction

The **CoreDatabase** abstract class manages your database connection settings and behavior. When multiple database servers are defined in the `$databaseServers` array property, they can serve as fallback options or be used for sharding—depending on how the keys are configured and if database sharding is enabled.

If the primary database (as set in your `.env` file) fails to connect, the framework will automatically attempt to connect to the next available server in the list. This ensures your application stays available, even during outages.

To learn more about database management, automatic failover and query builder, see the [Database Connection](/database/connection.md) or [Database Builder](/database/query-builder.md) documentation.

---

### Example

Here is an example on how you can extend and use the base server.

```php 
// /app/Config/Database.php

namespace App\Config;

use Luminova\Foundation\Core\Database as CoreDatabase;

class Database extends CoreDatabase
{
    protected static array $databaseServers = [
        'NG' => [
            'host'          => 'ng.db.server',
            'port'          => 3306,
            'database'      => 'my_db_ng',
            'connection'    => 'pdo', 
            'pdo_version'   => 'mysql',
            'username'      => 'user_ng',
            'password'      => 'secret',
            'charset'       => 'utf8mb4',
            'version'       => 'mysql',
            'persistent'    => true,
            'emulate_prepares' => true,
            'socket'        => false,
            'timeout'       => 0,
            'socket_path'   => '',
            'sqlite_path'   => '', /* Only used if version is sqlite */
        ],
        'US' => [...],
        'UK' => [...],
        /* More connection servers ... */
    ];
}
```

***

## Class Definition

* Class namespace: `Luminova\Foundation\Core\Database`
* This class is an **Abstract class**
* This class implements: [Luminova\Interface\LazyObjectInterface](/interface/classes.md#lazyobjectinterface)

***

## Properties

### shardFallbackOnError

Whether to fallback to an available backup server if the selected shard is unreachable.

When enabled, the system will attempt to use a backup configuration from `$databaseServers` if the main shard server fails or is offline.

```php
protected static bool $shardFallbackOnError = false;
```

> This is useful in distributed environments where high availability is required.

---

### databaseServers

Enable or disable connection-level database sharding.

When set to true, the application will attempt to route database connections based on shard configurations defined by the shard location identifier `getShardServerKey`. 

This allows for distributing database load across multiple shard servers  for better scalability and isolation (e.g., multi-tenant or geo-based setups).

```php
protected static bool $connectionSharding = false;
```

> If false, all connections will be made to the default primary database configuration.

---

### databaseServers

List of database connection servers.

**This serves two purposes:**

- Provides alternative connections for **sharded environments** when a shard is unavailable.
- Acts as a **failover mechanism** for non-sharded environments when the main database fails.

Each server should follow the same structure as the primary configuration.

```php
protected static array<int,mixed> $databaseServers = [];
```

> Optional servers to connect to when main server fails, an associative array with each database configuration keys and values.

---

## Methods

***

### constructor

Initialize database configuration with backup connection details.

```php
public __construct(array<string,mixed> $config = []): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$config` | **array<string,mixed>** | Optional database configuration to preload. |

***

### getServers

Retrieve all database connection servers.

```php
public static getServers(): array
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$expression` | **string** | The cron expression. |

**Return Value:**

`array<string|int,mixed>` - Return database connection servers.

***

### getValue

Get database configuration property value.

```php
public getValue(string $key, mixed $default = null): mixed 
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string** | The property configuration key. |
| `$default` | **mixed** | Optional default value (default: null). |

**Return Value:**

`mixed` - Return database connection property value.