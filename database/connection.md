# Working with Database Connection Objects

***

## Overview

Initializes database connections with built-in support for efficient data handling, connection pooling, sharding, and automatic fallback to backup servers.

***

## Introduction

Databases are essential in application development for storing and managing data. Luminova's **Connection** class simplifies database connectivity, offering support for **connection pooling** and **sharding** to boost performance and scale with your application.

### Getting Started

Establishing a connection in Luminova is straightforward. Define your connection settings in the `.env` file or in the `App\Config\Database` class. For a comprehensive guide, see [Database Example](/examples/database-integration.md).

**Example `.env` configuration:**

```bash
database.hostname = localhost
database.port = 3306
database.connection = PDO
database.pdo.version = mysql
database.username = root
database.name = my_db
database.password = password
```

### Additional Notes

* **CLI Access (MySQL)**:
  If you're running commands via the CLI and using MySQL, specify your socket path using:

  ```
  database.mysql.socket.path = /path/to/mysql.sock
  ```

* **SQLite Configuration**:
  To use SQLite, define the full database path in:

  ```
  database.sqlite.path = writeable/database/production.sqlite
  ```

  > Make sure the file is located under `/writeable/database/`.

---

### Configuration Class

This configuration class allows you set set backup database connection servers or server locations for sharding if enabled. For more information see [Database Connection Configuration](/configs/database.md).

```php
// /app/Config/Database.php

namespace App\Config;

use Luminova\Foundation\Core\Database as CoreDatabase;

class Database extends CoreDatabase
{
    public static bool $connectionSharding = true;
    public static bool $shardFallbackOnError = false;

    protected static array $databaseServers = [
        'NG' => [
            'host'     => 'ng.db.server',
            'port'     => 3306,
            'database' => 'my_gn_db',
            'username' => 'user_ng',
            'password' => 'secret',
            'charset'  => 'utf8mb4',
            'version'  => 'mysql',
        ],
        'US' => [
            'host' => 'us.db.server',
            // ...
        ],
        'DEFAULT' => [
            'host' => 'main.db.server',
            // ...
        ]
    ];

    public static function getShardServerKey(): string
    {
        // Example
        return request()->getAny('region', 'DEFAULT');
    }
}
```

> When a connection is initialized using `new Connection` or `Connection::getInstance()`,
> and sharding is enabled, the system calls `getShardServerKey()` to determine the appropriate shard (based on a region, user ID, etc.).

---

### Basic Initialization

Create a new connection with optional connection pooling:

```php
use Luminova\Database\Connection;

$enablePool = true;
$maxConnections = 5;
$autoConnect = true;

$conn = new Connection($enablePool, $maxConnections, $autoConnect);
```

---

### Sharded Instance

Create or retrieve a singleton connection instance with auto sharding support:

```php
use Luminova\Database\Connection;

$conn = Connection::getInstance(...);
```

---

### Manual Shard Connection

Manually connect to a specific shard by server ID:

```php
use Luminova\Database\Connection;

$conn = Connection::shard(
    'NG',     /* Shard key (e.g., region or user-specific ID) */
    false,    /* Fallback on connection error */
    null,     /* Use default host from env config */
    null,     /* Use default database name */
    false     /* Whether to reuse an existing connection */
);
```

***

## Class Definition

* Class namespace: `Luminova\Database\Connection`
* This class implements: [Luminova\Interface\LazyObjectInterface](/interface/classes.md#lazyobjectinterface), [\Countable](https://www.php.net/manual/en/class.countable.php)

***

## Properties

Database connection driver instance.

```php
protected ?Luminova\Interface\DatabaseInterface $db = null;
```

***

## Methods

### constructor

Create a new database connection instance.

Initializes the connection with optional settings for pooling and maximum connections.

If not explicitly provided, values are loaded from environment variables:
- `database.connection.pool` for pooling.
- `database.max.connections` for maximum connections.

When `$autoConnect` is true, the connection is automatically established on instantiation.

```php
public __construct(?bool $pool = null, ?int $maxConnections = null, bool $autoConnect = true)
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$pool` | **bool&#124;null** | Whether to enable connection pooling.<br/>Overrides the `database.connection.pool` environment setting if set. |
| `$maxConnections` | **int&#124;null** | Maximum number of pooled connections.<br/>Overrides `database.max.connections` from the environment if set. |
| `$autoConnect` | **bool** | Whether to immediately initiate the database connection (default: `true`). |

**Throws:**

- [Luminova\Exceptions\DatabaseException](/error-handlers/exceptions.md#databaseexception) - If connection retries fail, the connection limit is exceeded, an invalid driver is specified, or any error occurs during connection.

***

### getInstance

Returns the shared singleton instance of the connection class.

Creates a new instance if one does not already exist, optionally configuring connection pooling and maximum allowed connections. 

Settings fall back to environment values if not provided:
 - `database.connection.pool` for connection pooling.
 - `database.max.connections` for connection limits.

 If `$autoConnect` is true, the database connection is established immediately.

```php
public static getInstance(?bool $pool = null, ?int $maxConnections = null, bool $autoConnect = true): static
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$pool` | **bool&#124;null** | Enables or disables connection pooling. |
| `$maxConnections` | **int&#124;null** | Maximum number of allowed connections. |
| `$autoConnect` | **bool** | Whether to auto-connect on initialization (default: `true`). |

**Return Value:**

`Luminova\Database\Connection` - Return the singleton instance of the Connection class.

**Throws:**
- [Luminova\Exceptions\DatabaseException](/error-handlers/exceptions.md#databaseexception) - If connection retries fail, max connection limit is reached, an invalid driver is detected, or a connection error occurs.

***

### shard

Initialize a database connection for a specific shard server.

This static initializer creates (or reuses) an instance of the connection class,
optionally assigning it to a specific shard server identified by `$locationId`.
If the selected shard is unreachable, it can fallback to available backup servers.

```php
public static shard(
  string $locationId, 
  bool $fallbackOnError = false,
  ?bool $pool = null, 
  ?int $maxConnections = null,
  bool $sharedInstance = false
): static
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$locationId` | **string** | Shard identifier (e.g., region name or server key). |
| `$fallbackOnError` | **bool** | Fallback to a backup server if shard server connection is unavailable. (default: `false`). |
| `$pool` | **bool&#124;null** | Enables or disables connection pooling (if applicable). |
| `$maxConnections` | **int&#124;null** | Maximum number of connections allowed in the pool. |
| `$sharedInstance` | **bool** | Reuse a shared static instance if set to true (default: `false`). |

**Return Value:**

`Luminova\Database\Connection` - Connection Returns an initialized database connection instance.

**Throws:**
- [Luminova\Exceptions\DatabaseException](/error-handlers/exceptions.md#databaseexception) - If connection retries fail, max connection limit is reached, an invalid driver is detected, or a connection error occurs.

***

### getPool

Retrieves a free connection from the pool. Optionally fetches the first available valid connection.

If `$anyFree` is set to `true`, the method returns the first free connection that is connected and valid, removing it from the pool. Otherwise, it fetches the first connection in the pool and returns it if valid, or `null` if no valid connection exists.

```php
public getPool(bool $anyFree = false): ?DatabaseInterface
```
**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$anyFree` | **bool** | If `true`, returns the first valid connection from the pool (default: `false`). |

**Return Value:**

`Luminova\Interface\DatabaseInterface|null` - Return the first valid connection from the pool or `null` if none are available.

***

### newInstance

Retrieves a new database driver instance based on the provided configuration.

If no configuration is provided, the default configuration will be used.

```php
public static newInstance(?Luminova\Foundation\Core\Database $config = null): ?DatabaseInterface
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$config` | **Luminova\Foundation\Core\Database&#124;null** | Database configuration (default: null). |

**Return Value:**

`Luminova\Interface\DatabaseInterface|null` - Return the database driver instance, or null if connection fails.

**Throws:**

- [Luminova\Exceptions\DatabaseException](/error-handlers/exceptions.md#databaseexception) - If all retry attempts fail, the maximum connection limit is reached, an invalid database driver is provided, an error occurs during connection, or an invalid driver interface is detected.

***

### database

Retrieves the database driver connection object.

```php
public database(): ?DatabaseInterface
```

**Return Value:**

`Luminova\Interface\DatabaseInterface|null` - Return the driver connection instance, or null if not connected.

**Examples:**

Accessing the raw `PDO` or `MySQLi` connection instance.

#### Using the Driver Object

```php
$driver = $conn->database(); // Get the driver instance

$driver->query('SELECT * FROM users');
print_r($driver->fetchAll());
```

#### Using the Native Connection (PDO/MySQLi)

```php
$db = $conn->database()     // Get the driver
    ->raw()           // Get the raw connection wrapper
    ->getConn();      // Get the native PDO or MySQLi object

$stmt = $db->query('SELECT * FROM users');
print_r($stmt->fetchAll());
```

> Use `raw()->getConn()` when you need direct access to low-level methods or driver-specific functionality.

***

### connect

Establish a database connection.

This method either returns a connection object or reusing a previous connection from the pool if available. Optionally, it retries failed connections based on the retry attempt value set in the `.env` configuration (`database.connection.retry`) or backup database connections defined withing the `App\Config\Database` class.

```php
public connect(): ?DatabaseInterface
```

**Return Value:**

`Luminova\Interface\DatabaseInterface|null` -Return the database driver instance (either `MySqliDriver` or `Luminova\`), or null if connection fails.

**Throws:**

- [Luminova\Exceptions\DatabaseException](/error-handlers/exceptions.md#databaseexception) - If all retry attempts fail, the maximum connection limit is reached, an invalid database driver is provided, an error occurs during connection, or an invalid driver interface is detected.

***

### disconnect

Frees up the statement cursor and close current database connection.

```php
public disconnect(): bool
```
**Return Value:**

`bool` - Return true if disconnected, false otherwise.

> **Note:** To close all connections including pools use `purge` method instead.

***

### retry

Attempts to reconnect to the database with optional fallback to backup servers.

If `$retry` is set to `null`, the method will attempt to connect using backup databases (if available). 
Otherwise, it will attempt to reconnect based on the specified retry count.

```php
public retry(int|null $retry = 1): ?DatabaseInterface
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$retry` | **int&#124;null** | The number of retry attempts (default: 1). Pass `null` to attempt fallback to backup servers. |

**Return Value:**

`Luminova\Interface\DatabaseInterface|null` - Returns a database connection if successful, or `null` if all attempts fail.

**Throws:**

- [Luminova\Exceptions\DatabaseException](/error-handlers/exceptions.md#databaseexception) - If all retry attempts fail, the maximum connection limit is reached, an invalid database driver is provided, or an error occurs during connection.

***

### release

Releases a connection back to the connection pool.

```php
public release(DatabaseInterface $connection, string $id): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$connection` | **Luminova\Interface\DatabaseInterface** | The connection to release to pools. |
| `$id` | **string** | An identifier for the current connection pool. |

**Throws:**

- [Luminova\Exceptions\DatabaseException](/error-handlers/exceptions.md#databaseexception) - Throws if max connections are reached.

> If the connection pool is not full, adds the provided connection to the pool else closes the provided connection.

***

### purge

Purges all pooled connections and optionally closes the current database connection.

```php
public purge(bool $closeCurrent = false): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$closeCurrent` | **bool** | If true, close the current database connection also (default: false). |

**Return Value:**

`bool` - Return true when connections are closed, otherwise false.

> **Note:** If the conn parameter is true, the database connection will be closed; otherwise, only the pool connections will be closed.

***

### count

Count the number of connections in pools.

```php
public count(): int
```

**Return Value:**

`int` - Return the number of connection pools.
