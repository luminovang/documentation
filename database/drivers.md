# Database Driver Interface Specification

***

## Overview

Database Driver Interface: Facilitating Database Connectivity, Query Execution, and Result Handling. Explore our Database Example to Kickstart Your Integration.

***

## Introduction

Database drivers Interface, provides methods for executing database queries, statement preparation, and result fetching. To get started, see [Database Example](/database/examples.md).

* Class namespace: `\Luminova\Interface\DriversInterface`

***

### constructor

Initialize database driver with your database connection configurations.

`\Luminova\Database\Drivers\MySqliDriver` - Mysqli connection driver.

```php
new MySqliDriver(\Luminova\Base\CoreDatabase $config): mixed
```

`\Luminova\Database\Drivers\PdoDriver` - PDO connection driver.

```php
new PdoDriver(\Luminova\Base\CoreDatabase $config): mixed
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

Checks if the database is connected.

```php
public isConnected(): bool
```

**Return Value:**

`bool` - True if connected, false otherwise.

***

### getDriver

Get database connection driver name in use (e.g `sqlite`, `mysql`, `cubrid` etc..).

```php
public getDriver(): ?string
```

**Return Value:**

`string|null` - Return driver name if connection is open, otherwise null.

***

### raw

Get the actual database connection object of `PDO` or `mysqli` database.

```php
public raw(): \Luminova\Interface\ConnInterface|null
```

**Return Value:**

`\Luminova\Interface\ConnInterface|null` - Connection instance if connected, null otherwise.

***

### statement

Get prepared statement of a query result.

```php
public statement(): PDOStatement|mysqli_stmt|mysqli_result|bool|null
```

**Return Value:**

`PDOStatement|mysqli_stmt|mysqli_result|bool|null` - Statement instance.

***

### setDebug

Sets the debug mode.

```php
public setDebug(bool $debug): self
```

**Return Value:**

`self` - The current instance.

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$debug` | **bool** | The debug mode. |

***

### error

Returns the error information for the last statement execution.

```php
public error(): string
```

**Return Value:**

`string` - The error information.

***

### errors

Returns all error information.

```php
public errors(): array
```

**Return Value:**

`array` - The error information.

***

### dumpDebug

Debug dumps statement information for the last statement execution.

```php
public dumpDebug(): bool|null
```

**Return Value:**

`bool|null` - True on success, false on failure, or null if debug mode is disabled.

***

### info

Returns information about the last statement execution.

```php
public info(): array
```

**Return Value:**

`array` - The statement execution information.

***

### prepare

Prepares a statement for execution.

```php
public prepare(string $query): self
```

**Return Value:**

`self` - The current instance.

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$query` | **string** | The SQL query. |

***

### query

Executes an sql query string.

```php
public query(string $query): self
```

**Return Value:**

`self` - The current instance.

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$query` | **string** | The SQL query. |

***

### exec

Execute an SQL statement and return the number of affected rows.

```php
public exec(string $query): int
```

**Return Value:**

`int` - The number of affected rows.

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
public bind(string $param, mixed $value, int|null $type = null): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$param` | **string** | The parameter identifier. |
| `$value` | **mixed** | The parameter value. |
| `$type` | **int&#124;null** | The parameter type. |

**Return Value:**

`self` - The current instance.

***

### param

Binds a variable to a parameter.

```php
public param(string $param, mixed &$value, int|null $type = null): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$param` | **string** | The parameter identifier. |
| `&$value` | **mixed** | The parameter value passed by reference. |
| `$type` | **int&#124;null** | The parameter type. |

**Return Value:**

`self` - The current instance.

***

### execute

Executes the prepared statement.

```php
public execute(array|null $params = null): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$params` | **array&#124;null** | An optional list array to bound parameters while executing statement. Each value is treated as a string. |

**Return Value:**

`bool` - True on success, false on failure.

**Throws:**

- [\Luminova\Exceptions\DatabaseException](/running/exceptions.md#databaseexception) - Query statment  encounter error.

***

### ok

Check if query execution is completed successfully.

```php 
public function ok(): bool;
```

**Return Value:**

`bool` - True on success, false on failure.

***

### rowCount

Returns the number of rows affected by the last statement execution.

```php
public rowCount(): int|bool
```

**Return Value:**

`int|bool` - The number of rows, otherwise return false on failure.

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
public getInt(): array|bool
```

**Return Value:**

`array|bool` -  2D array of integers else return false on failure.

***

### getCount

Fetches total count of selected rows as integer.

```php
public getCount(): int|bool
```

**Return Value:**

`int|bool` -  Return integers else return false on failure.

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

### getStatment

Get prepared statement depending on database connection specified in `.env` file.

```php
public getStatment(): PDOStatement|mysqli_stmt|mysqli_result|bool|null
```

**Return Value:**

`object|false` - Returns prepared statement else false or null if not statement.

***

### getLastInsertId

Returns the ID of the last inserted record or sequence value.

```php
public getLastInsertId(): string
```

**Return Value:**

`string` - The last insert ID.

***

### getItem

More fancy way to return record from statement (context: all, next, 2dint, total, lastId, count or column).

```php
public getItem(int $mode = RETURN_ALL string $return = 'object'): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$mode` | **int** | Return mode RETURN_* <br> See database [return modes ](/variables/helper.md). |
| `$return` | **string** | [The return data type (array or object)]. |

**Return Value:**

`mixed` -  Return selected rows or false on failure.

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