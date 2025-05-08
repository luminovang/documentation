# Working with Database Connection Objects

***

## Overview

Initializes Database Connections with efficient data management, database connection pool, and backup server connectivity.

***

## Introduction

Databases are crucial in application development for storing and managing data. The Luminova `Database` class simplifies initiating a database connection, enabling efficient data management. For a comprehensive guide, see [Database Example](/database/examples.md).

With Luminova, establishing a database connection is easy and requires minimal configuration. Simply define your database `host-name`, `username`, and `password` in the `ENV` file.

To allow connections through the CLI, specify your `MYSQL` socket path in the `ENV` file using `database.mysql.socket.path`. For `SQLITE`, specify your database path and file in `database.sqlite.path`, such as `writeable/database/production.sqlite`, ensuring it is located in `/writeable/database/`.

***

## Class Definition

* Class namespace: `\Luminova\Database\Connection`
* This class implements: [\Luminova\Interface\LazyInterface](/interface/classes.md#lazyinterface), [\Countable](https://www.php.net/manual/en/class.countable.php)

***

## Properties

Database connection driver instance.

```php
protected ?\Luminova\Interface\DatabaseInterface $db = null;
```

***

## Methods

### constructor

Initializes a database connection based on provided parameters or default to `.env` configuration.

- Configures `maxConnections` and `pool` properties from the provided arguments or environment variables.
- If `$pool` or `$max` are provided, they override the corresponding environment variable values.

```php
public __construct(?bool $pool = null, ?int $maxConnections = null)
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$pool` | **bool&#124;null** | Optional. Weather to enables or disables connection pooling.<br/> Defaults to the value of `database.connection.pool` from the environment. |
| `$maxConnections` | **int&#124;null** | Optional. Specifies the maximum number of database connections.<br/>Defaults to the value of `database.max.connections` from the environment. |

**Throws:**

- [\Luminova\Exceptions\DatabaseException](/running/exceptions.md#databaseexception) -  If connection retries fail, the max connection limit is exceeded, an invalid driver or driver interface is detected, or a connection error occurs.

***

### getInstance

Retrieves the shared singleton instance of the Connection class.

```php
public static getInstance(?bool $pool = null, ?int $maxConnections = null): self
```
**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$pool` | **bool&#124;null** | Optional. Weather to enables or disables connection pooling. |
| `$maxConnections` | **int&#124;null** | Optional. Specifies the maximum number of database connections. |

**Return Value:**

`self` - Return the singleton instance of the Connection class.

**Throws:**
- [\Luminova\Exceptions\DatabaseException](/running/exceptions.md#databaseexception) - If all retry attempts fail, the maximum connection limit is reached, an invalid database driver is provided, an error occurs during connection, or an invalid driver interface is detected.

***

### getPool

Retrieves a free connection from the pool. Optionally fetches the first available valid connection.

If `$any_free` is set to `true`, the method returns the first free connection that is connected and valid, removing it from the pool. Otherwise, it fetches the first connection in the pool and returns it if valid, or `null` if no valid connection exists.

```php
public getPool(bool $any_free = false): ?\Luminova\Interface\DatabaseInterface
```
**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$any_free` | **bool** | If `true`, returns the first valid connection from the pool (default: `false`). |

**Return Value:**

`DatabaseInterface` - Return the first valid connection from the pool or `null` if none are available.

***

### newInstance

Retrieves a new database driver instance based on the provided configuration.

If no configuration is provided, the default configuration will be used.

```php
public static newInstance(?\Luminova\Base\CoreDatabase $config = null): ?\Luminova\Interface\DatabaseInterface
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$config` | **\Luminova\Base\CoreDatabase&#124;null** | Database configuration (default: null). |

**Return Value:**

`DatabaseInterface|null` - Return the database driver instance, or null if connection fails.

**Throws:**

- [\Luminova\Exceptions\DatabaseException](/running/exceptions.md#databaseexception) - If all retry attempts fail, the maximum connection limit is reached, an invalid database driver is provided, an error occurs during connection, or an invalid driver interface is detected.

***

### database

Retrieves the database driver connection instance.

```php
public database(): ?\Luminova\Interface\DatabaseInterface
```

**Return Value:**

`DatabaseInterface|null` - Return the driver connection instance, or null if not connected.

**Usage:**

To get the raw database connection instance of `PDO` or `mysqli`.

```php
<?php 
$db = $conn->database()->raw()
```

***

### connect

Connects to the database, either returning a connection instance or reusing a previous connection from the pool if available. This method also optionally retries failed connections based on the retry attempt value set in the `.env` configuration (`database.connection.retry`) or backup database connections defined withing the `App\Config\Database` class.

```php
public connect(): ?\Luminova\Interface\DatabaseInterface
```

**Return Value:**

`DatabaseInterface|null` -Return the database driver instance (either `MySqliDriver` or `PdoDriver`), or null if connection fails.

**Throws:**

- [\Luminova\Exceptions\DatabaseException](/running/exceptions.md#databaseexception) - If all retry attempts fail, the maximum connection limit is reached, an invalid database driver is provided, an error occurs during connection, or an invalid driver interface is detected.

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
public retry(int|null $retry = 1): ?\Luminova\Interface\DatabaseInterface
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$retry` | **int&#124;null** | The number of retry attempts (default: 1). Pass `null` to attempt fallback to backup servers. |

**Return Value:**

`DatabaseInterface|null` - Returns a database connection if successful, or `null` if all attempts fail.

**Throws:**

- [\Luminova\Exceptions\DatabaseException](/running/exceptions.md#databaseexception) - If all retry attempts fail, the maximum connection limit is reached, an invalid database driver is provided, or an error occurs during connection.

***

### release

Releases a connection back to the connection pool.

```php
public release(DatabaseInterface $connection, string $id): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$connection` | **\Luminova\Interface\DatabaseInterface** | The connection to release to pools. |
| `$id` | **string** | An identifier for the current connection pool. |

**Throws:**

- [\Luminova\Exceptions\DatabaseException](/running/exceptions.md#databaseexception) - Throws if max connections are reached.

> If the connection pool is not full, adds the provided connection to the pool else closes the provided connection.

***

### purge

Purges all pooled connections and optionally closes the current database connection.

```php
public purge(bool $close_current = false): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$close_current` | **bool** | If true, close the current database connection also (default: false). |

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
