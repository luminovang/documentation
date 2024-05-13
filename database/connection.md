## Database Connection

***

## Overview

Initializes Database Connections with efficient data management, database connection pool, and backup server connectivity.

***

This class allows you to initiate a database connection, Comprehensive Guide to Using the Database Connection Class for Efficient Data Management to get started, see [Database Example](/database/examples).

* Class namespace: `\Luminova\Database\Connection`

## Properties

Database connection instance

```php
protected \Luminova\Interface\DriversInterface|null $db
```

***

## Methods

### constructor

Initializes the Connection class constructor based on configuration in the .env file.

```php
new Connection()
```

* The constructor method is **final**.

**Throws:**

- [DatabaseException](/exceptions/database) - If all retry attempts fail or an error occurs during connection.
- [DatabaseLimitException](/exceptions/database-limit) - When the maximum connection limit is reached.
- [InvalidArgumentException](/exceptions/invalid-argument) - If an invalid connection configuration or driver is passed.

***

### database

Retrieves the database connection instance.

```php
public database(): \Luminova\Interface\DriversInterface|null
```

**Return Value:**

`DriversInterface|null` - The database driver connection instance if connected; otherwise, returns null.

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
- [DatabaseException](/exceptions/database) - If all retry attempts fail or an error occurs during connection.
- [DatabaseLimitException](/exceptions/database-limit) - When the maximum connection limit is reached.
- [InvalidArgumentException](/exceptions/invalid-argument) - If an invalid connection configuration or driver is passed.

***

### newInstance

Retrieves a new database driver instance based on the provided configuration.

If no configuration is provided, the default configuration will be used.

```php
public static newInstance(\Luminova\Base\BaseDatabase|null $config = null): \Luminova\Interface\DriversInterface|null
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$config` | **\Luminova\Base\BaseDatabase&#124;null** | Database configuration (default: null). |

**Return Value:**

`DriversInterface|null` - Database driver instance.

**Throws:**

- [DatabaseException](exceptions/database) - If all retry attempts fail, an error occurs during connection, or an invalid driver interface is detected.
- [DatabaseLimitException](exceptions/database-limit) - When the maximum connection limit is reached.
- [InvalidArgumentException](/exceptions/invalid-argument) - If an invalid database driver is provided.

***

### connect

Connects to the database, either returning a connection instance or reusing a previous connection from the pool if available.

```php
public connect(): \Luminova\Interface\DriversInterface|null
```

**Return Value:**

`DriversInterface|nul` - Database driver instance (either MySqliDriver or PdoDriver).

**Throws:**

- [DatabaseException](exceptions/database) - If all retry attempts fail or an error occurs during connection.
- [DatabaseLimitException](/exceptions/database-limit) - When the maximum connection limit is reached.
- [InvalidArgumentException](/exceptions/Invalid-argument) - If an invalid connection configuration or driver is passed.

> Optionally retries failed connections based on the retry attempt value set in the .env file (`database.connection.retry`).

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

`DriversInterface|null` - Connection instance or null if all retry attempts fail.

**Throws:**

- [DatabaseException](exceptions/database) - If all retry attempts fail or an error occurs during connection.
- [DatabaseLimitException](/exceptions/database-limit) - When the maximum connection limit is reached.
- [InvalidArgumentException](/exceptions/Invalid-argument) - If an invalid connection configuration or driver is passed.

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

Purges all stacked pool connections and optionally closes the database connection.

```php
public purge(bool $conn = false): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$conn` | **bool** | If true, close the database connection. Default is false. |

> If the conn parameter is true, the database connection will be closed; otherwise, only the pool connections will be closed.