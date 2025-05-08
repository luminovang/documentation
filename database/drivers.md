# Database Connection Drivers and Interface Overview

***

## Overview

Database Driver Interface: Facilitating Database Connectivity, Query Execution, and Result Handling. Explore our Database Example to Kickstart Your Integration.

***

## Introduction

Database drivers Interface, provides methods for executing database queries, statement preparation, and result fetching. To get started, see [Database Example](/database/examples.md).

***

### Usages

The recommended way to connect to database in Luminova is by using the connection class [Database Connection](/database/connection.md). This allow you to easily switch between different database drivers in environment configuration file (`.env`), without manually updating your code.

```php
use Luminova\Database\Connection;

$connectionPool = true;
$maxConnections = 5;

$conn = new Connection($connectionPool, $maxConnections);
```

***

To manually initialize a specific database driver with application database connection configurations.

`Luminova\Database\Drivers\MysqliDriver` - Mysqli connection driver.

```php
use Luminova\Database\Drivers\MysqliDriver;
use App\Config\Database as DatabaseConfig;

$mysqli = new MySqliDriver(new DatabaseConfig([
    ...
]));
```

`Luminova\Database\Drivers\PdoDriver` - PDO connection driver.

```php
use Luminova\Database\Drivers\PdoDriver;
use App\Config\Database as DatabaseConfig;

$pdo = new PdoDriver(new DatabaseConfig([
    ...
]));
```

> **Note:** Manual initialization doesn't support connection pool, fallback-connection nor retry-connection.

***

### Class Definition

* Class namespaces: `Luminova\Database\Drivers\MysqliDriver`, `Luminova\Database\Drivers\PdoDriver`
* This class implements: [\Luminova\Interface\DatabaseInterface](/interface/classes.md#databaseinterface),

***

### constructor

Initialize database driver with application database configurations.

```php
public __constructor(\Luminova\Base\CoreDatabase $config)
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$config` | **\Luminova\Base\CoreDatabase** | Database configuration. |

**Throws:**

- [\Luminova\Exceptions\DatabaseException](/running/exceptions.md#databaseexception) - If the database connection fails.

***

## Methods

### isConnected

Check if the database is connection is already established.

```php
public isConnected(): bool
```

**Return Value:**

`bool` - Returns true if connected, false otherwise.

***

### getDriver

Get database connection driver name in use (e.g `sqlite`, `mysql`, `cubrid` etc...).

```php
public getDriver(): ?string
```

**Return Value:**

`string|null` - Return driver name if connection is open, otherwise null.

***

### getConfig

Retrieve a specific database configuration property.
 
This method allows fetching configuration values related to the database connection.
If the requested property does not exist, `null` is returned.

**Available Properties:**

- **connection** *(string)*: The connection method, typically `'pdo'` or other (default: `'pdo'`).
- **pdo_engine** *(string)*: The PDO driver to use (e.g., `'mysql'`, `'sqlite'`, etc.) (default: `'mysql'`).
- **charset** *(string)*: The character encoding for the connection (default: `'utf8mb4'`).
- **sqlite_path** *(string|null)*: Path to the SQLite database file (default: `null`).
- **production** *(bool)*: Indicates if the connection is in a production environment (default: `false`).
- **database** *(string)*: The name of the selected database (default: `''`).
- **persistent** *(bool)*: Whether to use persistent connections (default: `true`).
- **socket** *(bool)*: Whether to use a Unix socket instead of TCP/IP (default: `false`).
- **socket_path** *(string)*: The Unix socket path if `socket` is enabled (default: `''`).
- **emulate_preparse** *(bool)*: Enables query emulation before execution (default: `false`).

```php 
public getConfig(string $property): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$property` | **bool** | The name of the configuration property. |

**Return Value:**

`mixed` - Returns the property value if it exists, otherwise `null`.

***

### raw

Get the original database connection object of `PDO` or `mysqli` database.

```php
public raw(): ?Luminova\Interface\ConnInterface
```

**Return Value:**

`\Luminova\Interface\ConnInterface|null` - Returns the connection instance if connected, otherwise null.

***

### getStatement

Retrieve Mysqli or PDO prepared statement of the last executed query.

```php
public getStatement(): \PDOStatement|\mysqli_stmt|null
```

**Return Value:**

`PDOStatement|mysqli_stmt|null` - Retrieve `mysqli` or `PDO` prepared statement of the last executed query.

***

### setDebug

Sets the debug mode.

```php
public setDebug(bool $debug): self
```

**Return Value:**

`self` - Returns the current instance.

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$debug` | **bool** | Enable or disable debug mode. |

***

### error

Get the error information for the last executed statement.

```php
public error(): string
```

**Return Value:**

`string` - Returns the error information as a string.

***

### errors

Get all error information.

```php
public errors(): array
```

**Return Value:**

`array` - Returns an array of error information.

***

### dumpDebug

Dump debug information for the last executed statement.

```php
public dumpDebug(): bool
```

**Return Value:**

`bool` - Returns true if debug information is dumped, otherwise false.

***

### info

Get information about the last executed statement.

```php
public info(): array
```

**Return Value:**

`array` - Returns an array of statement execution information.

***

### prepare

Prepare a statement for execution.

```php
public prepare(string $query): self
```

**Return Value:**

`self` - Returns the current instance.

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$query` | **string** | The SQL query string to execute. |

***

### query

Execute a query without using placeholders.

```php
public query(string $query): self
```

**Return Value:**

`self` - Returns the current instance.

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$query` | **string** | The SQL query string to execute. |

***

### exec

Execute an SQL statement and return the number of affected rows.

```php
public exec(string $query): int
```

**Return Value:**

`int` - Returns the number of affected rows.

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$query` | **string** | The SQL statement to execute. |

***

### beginTransaction

Begins a transaction with optional read-only isolation level and `SAVEPOINT` for `PDO`.

```php
public beginTransaction(int $flags = 0, ?string $name = null): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$flags` | **int** | Optional flags to set transaction properties. (Default: `0`).<br>No predefined flags for `PDO`, specify `4` to create read-only isolation. |
| `$name` | **string&#124;null** | Optional name.<br> If provided in `PDO`, `SAVEPOINT` will be created with name instead. |

**Return Value:**

`bool` - Return true if transaction started successfully, otherwise false.

**Throws:**

- [\Luminova\Exceptions\DatabaseException](/running/exceptions.md#databaseexception) - Throws exception on `PDO` if failure to set transaction isolation level or create `savepoint`.

> **Note:** 
> - If `$flags` is set to `4` in `PDO`, which is equivalent to `MYSQLI_TRANS_START_READ_ONLY`, a read-only isolation level will be established. If the transaction starts successfully, it will return true.
> 
> - If `$name` is specified in `PDO`, a `SAVEPOINT` will be created. If the savepoint creation is successful, the transaction will return true.

***

### commit

Commits a transaction.

```php
public commit(int $flags = 0, ?string $name = null): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$flags` | **int** | Optional flags to set transaction properties. (Default: `0`).<br>Only supported in `MySQLi`. |
| `$name` | **string&#124;null** | Optional name.<br>Only supported in `MySQLi`. |

**Return Value:**

`bool` - Returns true if the transaction was successfully committed, otherwise false.

***

### rollback

Rolls back the current transaction or to a specific name while in `PDO` uses `SAVEPOINT`.

```php
public rollback(int $flags = 0, ?string $name = null): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$flags` | **int** | Optional flags to set transaction properties. (Default: `0`).<br/>Only supported in `MySQLi`. |
| `$name` | **string&#124;null** | Optional name.<br/>If provided in `PDO`, rolls back to the `SAVEPOINT` named |

**Return Value:**

`bool` - Return true if rolled back was successful, otherwise false.

**Throws:**

- [\Luminova\Exceptions\DatabaseException](/running/exceptions.md#databaseexception) - Throws exception on `PDO` if failure to create `SAVEPOINT`.

***

### inTransaction

Determine if there is any active transaction.

```php
public inTransaction(): bool
```

**Return Value:**

`bool` - Return true if any active transaction, otherwise false.

***

### getType

Returns the appropriate parameter type based on the value.

```php
public static getType(mixed $value): string|int|null
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$value` | **mixed** | The parameter value. |

**Return Value:**

`string|int|null` - The parameter type.

***

### bind

Binds a value to a parameter.

```php
public bind(string $param, mixed $value, ?int $type = null): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$param` | **string** | The parameter identifier. |
| `$value` | **mixed** | The parameter value. |
| `$type` | **int&#124;null** | The parameter type. |

**Return Value:**

`self` - Returns the current instance.

***

### param

Binds a variable to a parameter.

```php
public param(string $param, mixed &$value, ?int $type = null): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$param` | **string** | The parameter identifier. |
| `&$value` | **mixed** | The parameter value passed by reference. |
| `$type` | **int&#124;null** | The parameter type. |

**Return Value:**

`self` - Returns the current instance.

***

### execute

Executes the prepared statement.

```php
public execute(?array $params = null): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$params` | **array&#124;null** | An optional list array to bound parameters while executing statement. Each value is treated as a string. |

**Return Value:**

`bool` - Returns true on success, false on failure.

**Throws:**

- [\Luminova\Exceptions\DatabaseException](/running/exceptions.md#databaseexception) - Query statment  encounter error.

***

### ok

Check if query execution is completed successfully.

```php 
public function ok(): bool;
```

**Return Value:**

`bool` - Returns true on success, false on failure.

***

### rowCount

Returns the number of rows affected by the last statement execution.

```php
public rowCount(): int
```

**Return Value:**

`int|bool` - Returns the number of affected rows.

***

### getNext

Fetches a next single row as an object or array.

```php
public getNext(string $type = 'object'): array|object|bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$type` | **string** | The type of result to return ('object' or 'array'). |

**Return Value:**

`array|object|bool` - The result object based on mode, otherwise return false on failure.

***

### getAll

Fetches all rows as an array or objects.

```php
public getAll(string $type = 'object'): array|object|bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$type` | **string** | The type of result to return ('object' or 'array'). |

**Return Value:**

`array|object|bool` - The result object based on mode, otherwise return false on failure.

***

### getInt

Fetches selected rows as a 2D array of integers.

```php
public getInt(): array|false
```

**Return Value:**

`array|bool` -  2D array of integers else return false on failure.

***

### getCount

Fetches total count of selected rows as integer.

```php
public getCount(): int
```

**Return Value:**

`int` -  Returns the total count as an integer, or `0` on failure.

***

### getColumns

Get columns

```php
public getColumns(int $mode = FETCH_COLUMN): array
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$mode` | **int** | Fetch column mode [FETCH_COLUMN or FETCH_COLUMN_ASSOC] |

**Return Value:**

`array` -  Return and associative array or indexed array of columns depending on mode.

***

### fetch

Fetch result with a specific type and mode

```php
public fetch(string $type = 'all', int $mode = FETCH_OBJ): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$type` | **string** | The type of fetch method ('all' or 'next'). |
| `$mode` | **int** | Controls the contents of the returned |

**Return Value:**

`mixed` -  Return selected row item false on failure.

***

### fetchObject

Fetches the result set as an object of the specified class or stdClass.

```php
public fetchObject(string|null $class = 'stdClass', array $arguments = []): object|false
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$class` | **string&#124;null** | The name of the class to instantiate, defaults to stdClass. |
| `$arguments` | **array** | Additional arguments to pass to the class constructor. |

**Return Value:**

`object|false` - Returns the fetched object or false if no more rows are available or an error occurs.

***

### getLastInsertId

Returns the ID of the last inserted record or sequence value.

```php
public getLastInsertId(): string
```

**Return Value:**

`string` - The last insert ID.

***

### getResult

Retrieves a result item based on the specified mode and return type.

This method allows flexible result retrieval using a single interface, depending on the mode and return type provided.

**Available Modes:**

- RETURN_NEXT: Fetch the next row (same as `$db->getNext($return)`).
- RETURN_2D_NUM: Fetch a 2D numeric array (same as `$db->getInt()`).
- RETURN_INT: Fetch a single integer value (same as `$db->getCount()`).
- RETURN_ID: Fetch the last insert ID (same as `$db->getLastInsertId()`).
- RETURN_COUNT: Fetch the count of affected rows (same as `$db->rowCount()`).
- RETURN_COLUMN: Fetch specific columns (same as `$db->getColumns()`).
- RETURN_ALL: Fetch all rows (same as `$db->getAll($return)`).
- RETURN_STMT: Return the statement object itself (same as `$db->getStatement()`).
- RETURN_RESULT: Return the query result in mysqli or statement in PDO.

```php
public getResult(int $mode = RETURN_ALL string $return = 'object'): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$mode` | **int** | The mode of the result to return (e.g., `RETURN_*` constants). <br> See database [return modes ](/variables/helper.md). |
| `$return` | **string** | The return type when applicable (e.g., `array` or `object`).<br/>Used only with `RETURN_NEXT` and `RETURN_ALL` modes. |

**Return Value:**

`mixed` -  Return the result based on the specified mode and return type.

***

### free

Frees up the statement cursor and sets the statement object to null.

```php
public free(): void
```

***

### close

Frees up the statement cursor and close database connection.

```php
public close(): void
```