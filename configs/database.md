# Database Fallbacks and Sharding Configuration

***

## Overview

Set up automatic database failover or sharding to keep your app running smoothly, even if a primary database goes offline.

***

## Introduction

The **Database Configuration** class allows you to define your primary database connection as well as optional shard or backup servers. These fallback servers automatically take over if the primary database fails. Each backup connection is defined using a nested associative array, with keys such as `host`, `port`, `username`, and `password`.

> This setup enables automatic failover and supports sharding strategies based on region, user ID, or custom application logic.

***

## Class Definition

* Class namespace: `\App\Config\Database`
* File path: `/app/Config/Database.php`
* Parent class: [Luminova\Foundation\Core\Database](/base/database-model.md)

***

## Properties

### connectionSharding

Enable or disable connection-level database sharding.

When set to true, the application will attempt to route database connections based on shard configurations defined by the shard location identifier `getShardServerKey`. This allows for distributing database load across multiple shard servers 
for better scalability and isolation (e.g., multi-tenant or geo-based setups).

```php
public static bool $connectionSharding = false;
```

> If false, all connections will be made to the default primary database configuration.

---

### shardFallbackOnError
    
Whether to fallback to an available backup server if the selected shard is unreachable.

When enabled, the system will attempt to use a backup configuration from `$databaseServers` if the main shard server fails or is offline.

```php
public static bool $shardFallbackOnError = false;
```

> This is useful in distributed environments where high availability is required.

---

### databaseServers

List of backup database configurations.

This serves two purposes:
- Provides alternative connections for **sharded environments** when a shard is unavailable.
- Acts as a **failover mechanism** for non-sharded environments when the main database fails.

Each backup should follow the same structure as the primary configuration.

```php
protected static array<string,array<string,mixed>> $databaseServers = [];
```

> Nested associative array with each database configuration keys and values.

**Supported Configuration Keys**

The following keys are supported when defining database connection settings. These settings apply to both `.env` configuration and the `App\Config\Database` class.

| Key                   | Type     | Description                                                                                             |
| --------------------- | -------- | ------------------------------------------------------------------------------------------------------- |
| **host**              | `string` | The hostname or IP address of the database server. Defaults to `'localhost'`.                           |
| **port**              | `int`    | The port number to connect to the database server. Default is `3306`.                                   |
| **connection**        | `string` | Type of connection interface. Supported values: `'pdo'`, `'mysqli'`. Default is `'pdo'`.                |
| **pdo\_version**       | `string` |  Describes the DBMS version or engine type for PDO connections. Common values: `'mysql'`, `'pgsql'`, `'sqlite'`. Default is `'mysql'`.            |
| **charset**           | `string` | Character encoding used by the connection. Default is `'utf8mb4'`.                                      |
| **username**          | `string` | Username for authenticating with the database. Default is `'root'`.                                     |
| **password**          | `string` | Password for the database user. Default is an empty string.                                             |
| **database**          | `string` | Name of the target database. Default is an empty string.                                                |
| **socket**            | `bool`   | Whether to force connection via a UNIX socket. Default is `false`.                                      |
| **socket\_path**      | `string` | The full path to the socket file, required if `socket` is enabled. Default is an empty string.          |
| **sqlite\_path**      | `string` | Path to the SQLite database file. Required for SQLite connections. Default is `null`.                   |
| **production**        | `bool`   | If set to `true`, enables production mode behavior (e.g., disables debug features). Default is `false`. |
| **persistent**        | `bool`   | Enables persistent connections. Useful for performance in high-load environments. Default is `true`.    |
| **emulate\_preparse** | `bool`   | Enables emulation of prepared statements for PDO. Useful for compatibility. Default is `true`.          |

---

> The default connection values can be defined in your [.env file](/global/env-variables.md) or directly in the `App\Config\Database` class.
> Example:
>
> ```env
> database.connection=pdo
> database.pdo.version=mysql
> database.host=127.0.0.1
> database.port=3306
> database.username=root
> database.password=secret
> database.name=my_database
> ```

**Example:**

```php
// /app/Config/Database.php

namespace App\Config;

use Luminova\Foundation\Core\Database as CoreDatabase;

class Database extends CoreDatabase
{
    protected static array<string,array<string,mixed>> $databaseServers = [
        'DEFAULT' => [
            'host' => 'main.db.server',
            'port' => 3306,
            'database' => 'main_db',
            'username' => 'user_main',
            'password' => 'secret',
            'charset' => 'utf8mb4',
            'version' => 'mysql',
            'socket' => false,
            'socket_path' => '',
            'sqlite_path' => '', // Only used if version is sqlite
        ],
        'NG' => [
            'host' => 'ng.db.server',
            // ...
        ],
        'US' => [
            'host' => 'us.db.server',
            // ...
        ]
    ];
}
```
> Always set the default connection as first index before other connection servers.