# Database Connection Management

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
protected \Luminova\Interface\DriversInterface|null $db
```

***

## Methods

### constructor

Initializes the Connection class constructor based on configuration in the `ENV` file.

```php
$conn = new Connection();
```

**Throws:**

- [\Luminova\Exceptions\DatabaseException](/running/exceptions.md#databaseexception) - Throws if all retry attempts fail, the maximum connection limit is reached, an invalid database driver is provided, an error occurs during connection, or an invalid driver interface is detected.
- [\Luminova\Exceptions\InvalidArgumentException](/running/exceptions.md#invalidargumentexception) - If an invalid connection configuration or driver is passed.

***

### database

Retrieves the database driver connection instance.

```php
public database(): \Luminova\Interface\DriversInterface|null
```

**Return Value:**

`DriversInterface|null` - Return the driver connection instance, or null if not connected.

**Usage:**

To get the raw database connection instance of `PDO` or `mysqli`.

```php
<?php 
$conn->database()->raw()
```

***

### getInstance

Retrieves the shared singleton instance of the Connection class.

```php
public static getInstance(): self
```

**Return Value:**

`self` - Connection class instance.

**Throws:**
- [\Luminova\Exceptions\DatabaseException](/running/exceptions.md#databaseexception) - Throws if all retry attempts fail, the maximum connection limit is reached, an invalid database driver is provided, an error occurs during connection, or an invalid driver interface is detected.
- [\Luminova\Exceptions\InvalidArgumentException](/running/exceptions.md#invalidargumentexception) - If an invalid connection configuration or driver is passed.

***

### newInstance

Retrieves a new database driver instance based on the provided configuration.

If no configuration is provided, the default configuration will be used.

```php
public static newInstance(\Luminova\Base\CoreDatabase|null $config = null): \Luminova\Interface\DriversInterface|null
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$config` | **\Luminova\Base\CoreDatabase&#124;null** | Database configuration (default: null). |

**Return Value:**

`DriversInterface|null` - Database driver instance.

**Throws:**

- [\Luminova\Exceptions\DatabaseException](/running/exceptions.md#databaseexception) - Throws if all retry attempts fail, the maximum connection limit is reached, an invalid database driver is provided, an error occurs during connection, or an invalid driver interface is detected.
- [\Luminova\Exceptions\InvalidArgumentException](/running/exceptions.md#invalidargumentexception) - If an invalid database driver is provided.

***

### connect

Connects to the database, either returning a connection instance or reusing a previous connection from the pool if available.

```php
public connect(): \Luminova\Interface\DriversInterface|null
```

**Return Value:**

`DriversInterface|nul` - Database driver instance (either MySqliDriver or PdoDriver).

**Throws:**

- [\Luminova\Exceptions\DatabaseException](/running/exceptions.md#databaseexception) - Throws if all retry attempts fail, the maximum connection limit is reached, an invalid database driver is provided, an error occurs during connection, or an invalid driver interface is detected.
- [\Luminova\Exceptions\InvalidArgumentException](/running/exceptions.md#invalidargumentexception) - If an invalid connection configuration or driver is passed.

> Optionally retries failed connections based on the retry attempt value set in the .env file (`database.connection.retry`).

### disconnect

Frees up the statement cursor and close current database connection.

```php
public disconnect(): bool
```
**Return Value:**

`bool` - Always return true.

> **Note:** To close all connections including pools use `purge` method instead.

***

### retry

Retries the database connection with optional backup server fallback.

```php
public retry(int|null $retry = 1): \Luminova\Interface\DriversInterface|null
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$retry` | **int&#124;null** | Number of retry attempts (default: 1). |

**Return Value:**

`DriversInterface|null` - Return connection instance or null if all retry attempts fail.

**Throws:**

- [\Luminova\Exceptions\DatabaseException](/running/exceptions.md#databaseexception) - Throws if all retry attempts fail, the maximum connection limit is reached, an invalid database driver is provided, an error occurs during connection, or an invalid driver interface is detected.
- [\Luminova\Exceptions\InvalidArgumentException](/running/exceptions.md#invalidargumentexception) - If an invalid connection configuration or driver is passed.

> If the retry parameter is set to null, retries the connection with backup servers if available.

***

### release

Releases a connection back to the connection pool.

```php
public release(\Luminova\Interface\DriversInterface|null $connection): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$connection` | **\Luminova\Interface\DriversInterface&#124;null** | The connection to release. |

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

`bool` - Always true when connection are closed.

> If the conn parameter is true, the database connection will be closed; otherwise, only the pool connections will be closed.
