# Database Connection Drivers and Interface Overview

***

## Overview

Database driver classes handle database connections, execute queries, and retrieve results. Designed to work with both PDO and MySQLi.

***

## Introduction

Luminova provides built-in database drivers that implement the core interface for executing queries, preparing statements, and retrieving results. These drivers abstract the low-level database operations behind a consistent interface.

To get started with real-world usage, refer to the [Database Examples](/examples/database-integration.md).

---
###  Usages

### Recommended

The preferred way to manage database connections in Luminova is via the [Database Connection Class](/database/connection.md). It supports advanced features such as sharding, connection pooling, retry attempts, and fallback connections. It also allows easy switching between drivers by simply updating the `.env` value for `database.connection`—no code changes needed.

```php
use Luminova\Database\Connection;

$enableConnectionPool = true; // Enable connection pooling
$maxConnectionPools = 5;      // Maximum number of pooled connections
$autoConnect = true;          // Automatically connect on initialization

$conn = new Connection($enableConnectionPool, $maxConnectionPools, $autoConnect);
```

---

### Manual Driver Initialization

You can also initialize a specific driver manually if you need more control.

#### MySQLi Driver

```php
use Luminova\Database\Driver\MysqliDatabase;
use App\Config\Database;

$mysqli = new MysqliDatabase(new Database([
    // connection configuration options...
]));

$mysqli->connect();
```

---

#### PDO Driver

```php
use Luminova\Database\Driver\PdoDatabase;
use App\Config\Database;

$pdo = new PdoDatabase(new Database([
    // connection configuration options...
]));

$pdo->connect();
```

> **Note:**
> Manual driver usage **does not** support connection pooling, fallback connections, sharding or retry attempts.
> And must explicitly call `connect` method to establish connection.

---

### Emulate Prepares

Emulate Prepares allows you to **reuse the same named placeholder** multiple times within a SQL query, a feature not natively supported by MySQLi.

While **PDO** supports this behavior through its own emulation, **MySQLi does not**. However, Luminova's database layer **supports emulate prepares for both PDO and MySQLi**, providing consistent behavior across drivers.

To enable or disable emulate prepares:

* Set the `.env` key: `database.emulate.prepares`
* Or define it in your configuration array using the `emulate_prepares` key.

**When Enabled:**

You can reuse placeholders like this:

```php
use Luminova\Database\Connection;

$db = (new Connection())->database();

$stmt = $db->prepare('
    SELECT * FROM users 
    WHERE id = :identifier OR email = :identifier
');

$result = $stmt->execute(['identifier' => 100]);
```

**Using the Database Builder**

You can also use the Builder class for the same query:

```php
$result = Builder::query('
    SELECT * FROM users 
    WHERE id = :identifier OR email = :identifier
')->execute(['identifier' => 100]);
```

**When Disabled:**

The same query will fail with an error like:

```
The number of variables must match the number of parameters in the prepared statement.
```

> Luminova will internally expand the query and parameters to ensure compatibility with the driver.
>> This feature is especially useful for queries involving multiple comparisons with the same value. 
>> Use it when building dynamic queries or conditions across columns.

---

### Class Definition

* **Namespaces:**

  * `Luminova\Database\Driver\MysqliDatabase`
  * `Luminova\Database\Driver\PdoDatabase`

* **Implements:**
  [Luminova\Interface\DatabaseInterface](/interface/classes.md#databaseinterface)

***

## Methods

### constructoror

Initialize database driver constructor for configurations.

This constructor allows you to initialize driver with database configuration object to later establish a connection with.

```php
public __constructor(Luminova\Foundation\Core\Database $config)
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$config` | **Luminova\Foundation\Core\Database** | The database connection configuration object. |

### connect

Establish a database connection.

This method allows you to open a database connection for selected driver with database configurations.

```php
public connect(): bool
```

**Return Value:**

`bool` - Return true if database connection was established, otherwise false.

**Throws:**

- [Luminova\Exceptions\DatabaseException](/error-handlers/exceptions.md#databaseexception) - If the database connection fails.

***

### isConnected

Check if the database is connection is already established.

```php
public isConnected(): bool
```

**Return Value:**

`bool` - Returns true if connected, false otherwise.

***

### isStatement

Determine Whether the executed query prepared statement is ready.

```php
public isStatement(): bool
```

**Return Value:**

`bool` - Return true if is a prepared statement, false otherwise.

***

### isResult

Determine Whether the executed query result is ready.

```php
public isResult(): bool
```

**Return Value:**

`bool` - Return true if query result, false otherwise.

***

### getDriver

Get the driver version name of the database connection driver.

This method will retrieve the database driver name in use (e.g `sqlite`, `mysql`, `cubrid` etc...).

```php
public getDriver(): ?string
```

**Return Value:**

`string|null` - Returns the driver version name if the connection is open, otherwise null.

***

### getConfig

Retrieve a specific database configuration property.
 
This method allows fetching configuration values related to the database connection.
If the requested property does not exist, `null` is returned.

**Available Properties:**

- **connection** `(string)`: The connection method, typically `'pdo'` or other (default: `'pdo'`).
- **pdo_version** `(string)`: The PDO driver version to use (e.g., `'mysql'`, `'sqlite'`, etc.) (default: `'mysql'`).
- **charset** `(string)`: The character encoding for the connection (default: `'utf8mb4'`).
- **sqlite_path** `(string|null)`: Path to the SQLite database file (default: `null`).
- **production** `(bool)`: Indicates if the connection is in a production environment (default: `false`).
- **database** `(string)`: The name of the selected database (default: `''`).
- **persistent** `(bool)`: Whether to use persistent connections (default: `true`).
- **socket** `(bool)`: Whether to use a Unix socket instead of TCP/IP (default: `false`).
- **socket_path** `(string)`: The Unix socket path if `socket` is enabled (default: `''`).
- **emulate_prepares** `(bool)`: Enables query emulation before execution (default: `false`).

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

Get the raw database connection instance (e.g., `PDO` or `MySQLi`).

```php
public raw(): ?Luminova\Interface\ConnInterface
```

**Return Value:**

`ConnInterface|null` - Returns the connection instance if connected, otherwise null.

***

### getStatement

Retrieve the last executed prepared statement.

```php
public getStatement(): PDOStatement|mysqli_stmt|null
```

**Return Value:**

`PDOStatement|mysqli_stmt|null` - Returns the statement object, or null if no statement exists.

***

### setDebug

Sets the debug mode.

```php
public setDebug(bool $debug): Luminova\Interface\DatabaseInterface
```

**Return Value:**

`DatabaseInterface` - Returns the instance of database driver interface.

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

Prepares an SQL statement for execution.

This method initializes a prepared statement for both PDO and MySQLi drivers. The query should contain placeholders (`:columnName`) to be bound later using `bind()` or `param()`.

```php
public prepare(string $query): Luminova\Interface\DatabaseInterface
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$query` | **string** | The SQL query string to execute. |

**Return Value:**

`DatabaseInterface` - Returns the instance of database driver interface.

**Throws:**

- [Luminova\Exceptions\DatabaseException](/error-handlers/exceptions.md#databaseexception) - If no database connection is established.

**Example:**

Preparing SQL Statement:

```php
use Luminova\Database\Connection;

$db = (new Connection())->database();

$stmt = $db->prepare("SELECT * FROM users WHERE id = :id");
$stmt->bind(':foo', 'bar');
$stmt->execute();

$result = $stmt->fetch();
```

***

### query

Executes an SQL query without binding or placeholders.

This method is used for executing raw SQL statements that do not require parameter binding, such as **DDL** operations (`CREATE`, `ALTER`, `DROP`) or direct `SELECT` queries.

```php
public query(string $query): Luminova\Interface\DatabaseInterface
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$query` | **string** | The SQL query string to execute. |

**Return Value:**

`DatabaseInterface` - Returns the instance of database driver interface.

**Throws:**

- [Luminova\Exceptions\DatabaseException](/error-handlers/exceptions.md#databaseexception) - If no database connection is established.

**Example:**

Query Examples:

```php
use Luminova\Database\Connection;

$db = (new Connection())->database();

// select query
$stmt = $db->query("SELECT * FROM users");
$result = $stmt->fetchAll();

// Create a new table
$result = $db->query("CREATE TABLE logs (id INT AUTO_INCREMENT PRIMARY KEY, message TEXT)")
    ->rowCount();
```

***

### exec

Executes an SQL statement without placeholders and returns the number of affected rows.

This method is useful for operations like `ALTER`, `CREATE`, `DELETE`, where you need to know how many rows were modified.

```php
public exec(string $query): int
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$query` | **string** | The SQL query string to execute. |

**Return Value:**

`int` - Returns the number of rows affected by the query.

**Throws:**

- [Luminova\Exceptions\DatabaseException](/error-handlers/exceptions.md#databaseexception) - If no database connection is established.

**Example:**

Using the **exec** method.

```php
use Luminova\Database\Connection;

$db = (new Connection())->database();

$affected = $db->exec("UPDATE users SET status = 'active' WHERE last_login > NOW() - INTERVAL 30 DAY");
echo "Updated {$affected} rows.";
```

***

### beginTransaction

Begins a transaction with and optional read-only isolation level and `SAVEPOINT` for `PDO`.

**Flags:**

For MySQLi:
    - MYSQLI_TRANS_START_READ_ONLY: Set transaction as read-only.
For PDO:
    - Specify `4` to create a read-only isolation level.

```php
public beginTransaction(int $flags = 0, ?string $name = null): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$flags` | **int** | Optional flags to set transaction properties (default: `0`). |
| `$name` | **string&#124;null** | Optional name for a savepoint.<br> If provided in `PDO`, `SAVEPOINT` will be created with name instead. |

**Return Value:**

`bool` - Returns true if the transaction and optional savepoint were successfully started.

**Throws:**

- [Luminova\Exceptions\DatabaseException](/error-handlers/exceptions.md#databaseexception) - Throws exception on `PDO` if failure to set transaction isolation level or create `savepoint`.

> **Note:** 
> - If `$flags` is set to `4` in `PDO`, which is equivalent to `MYSQLI_TRANS_START_READ_ONLY`, a read-only isolation level will be established. If the transaction starts successfully, it will return true.
> 
> - If `$name` is specified in `PDO`, a `SAVEPOINT` will be created. If the savepoint creation is successful, the transaction will return true.

***

### commit

Commits a transaction if any.

```php
public commit(int $flags = 0, ?string $name = null): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$flags` | **int** | Optional flags to set transaction properties (default: `0`).<br>Only supported in `MySQLi`. |
| `$name` | **string&#124;null** | Optional name for a savepoint.<br>Only supported in `MySQLi`. |

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
| `$flags` | **int** | Optional flags to set transaction properties (default: `0`).<br/>Only supported in `MySQLi`. |
| `$name` | **string&#124;null** | Optional name of the savepoint to roll back to.<br/>If provided in `PDO`, rolls back to the `SAVEPOINT` named |

**Return Value:**

`bool` - Returns true if the rollback was successful, otherwise false.

**Throws:**

- [Luminova\Exceptions\DatabaseException](/error-handlers/exceptions.md#databaseexception) - Throws exception on `PDO` if failure to create `SAVEPOINT`.

***

### inTransaction

Determine if there is any active transaction.

```php
public inTransaction(): bool
```

**Return Value:**

`bool` - Return true if any active transaction, otherwise false.

**Throws:**

- [Luminova\Exceptions\DatabaseException](/error-handlers/exceptions.md#databaseexception) - Throw if an called when no connection is established.

***

### getType

Determines the appropriate database parameter type for a given value.

This is used to bind parameters correctly when preparing SQL statements, returning either a PDO type constant (string) or a MySQLi type constant (int), depending on the driver context.

```php
public static getType(mixed $value): string|int
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$value` | **mixed** | The value to get the type.. |

**Return Value:**

`string|int` - Return the parameter type, a string for PDO or an integer for MySQLi.

***

### bind

Binds a value to a named parameter for use in a prepared statement.

This method works for both PDO and MySQLi drivers. For PDO, the binding is done via `bindValue()`, while for MySQLi, the value is stored in an internal array for later binding.

```php
public bind(string $param, mixed $value, ?int $type = null): Luminova\Interface\DatabaseInterface
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$param` | **string** | The placeholder named parameter (e.g., `:columnName`). |
| `$value` | **mixed** | The value to bind. |
| `$type` | **int&#124;null** | (Optional) The data type for the value (default: null).<br/>- `PARAM_*`: For MySQLi and PDO driver.<br/>- `PDO::PARAM_*`: For PDO driver only supports custom type.<br/>- `NULL`:  Resolve internally. |

**Return Value:**

`DatabaseInterface` - Returns the instance of database driver interface.

**Throws:**

- [Luminova\Exceptions\DatabaseException](/error-handlers/exceptions.md#databaseexception) - If called without a prepared statement.

**Example:**

Supported in both PDO and MySQLi:

```php
use Luminova\Database\Connection;

$db = (new Connection())->database();

$stmt = $db->prepare("SELECT * FROM users WHERE id = :id");
$stmt->bind(':id', 123)->execute();
```

***

### value

Binds a value to a named parameter for use in a prepared statement.

This method is an alias of `bind()`.

```php
public value(string $param, mixed $value, ?int $type = null): Luminova\Interface\DatabaseInterface
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$param` | **string** | The placeholder named parameter (e.g., `:columnName`). |
| `$value` | **mixed** | The value to bind. |
| `$type` | **int&#124;null** | (Optional) The data type for the value (default: null).<br/>- `PARAM_*`: For MySQLi and PDO driver.<br/>- `PDO::PARAM_*`: For PDO driver only supports custom type.<br/>- `NULL`:  Resolve internally. |

**Return Value:**

`DatabaseInterface` - Returns the instance of database driver interface.

**Throws:**

- [Luminova\Exceptions\DatabaseException](/error-handlers/exceptions.md#databaseexception) - If called without a prepared statement.

***

### param

Binds a variable to a named parameter by reference use in a prepared statement.

Unlike `bind()` and `value()`, this method binds the parameter by reference, which means changes to the variable will be reflected in the query execution. This is useful for scenarios where values might change before execution.

> For PDO, the method binds using `bindParam()`, while for MySQLi, it stores a reference in an internal array.

```php
public param(string $param, mixed &$value, ?int $type = null): Luminova\Interface\DatabaseInterface
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$param` | **string** | The placeholder named parameter (e.g., `:columnName`). |
| `&$value` | **mixed** | The variable to bind by reference. |
| `$type` | **int&#124;null** | (Optional) The data type for the value (default: null).<br/>- `PARAM_*`: For MySQLi and PDO driver.<br/>- `PDO::PARAM_*`: For PDO driver only supports custom type.<br/>- `NULL`:  Resolve internally. |

**Return Value:**

`DatabaseInterface` - Returns the instance of database driver interface.

**Throws:**

- [Luminova\Exceptions\DatabaseException](/error-handlers/exceptions.md#databaseexception) - If called without a prepared statement.

**Example:**

For both PDO and MySQLi:

```php
use Luminova\Database\Connection;

$db = (new Connection())->database();

$stmt = $db->prepare("UPDATE users SET status = :status WHERE id = :id");

$status = 'active';
$stmt->param(':status', $status)
    ->param(':id', $userId);

// Changing the variable before execution affects the query
$status = 'inactive';
$stmt->execute();
```

***

### execute

Executes the prepared statement.

If parameters are provided, they will be bound to the statement during execution. This method works for both **PDO** and **MySQLi** drivers.

```php
public execute(?array<string,mixed> $params = null): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$params` | **array&#124;null** | An optional associative or indexed array of values to bind to placeholders before execution.. |

**Return Value:**

`bool` - Returns `true` on success or `false` on failure.

**Throws:**

- [Luminova\Exceptions\DatabaseException](/error-handlers/exceptions.md#databaseexception) - If execution fails due to a database error.

**Example:**

Executing Using Prepared Statement:

```php
use Luminova\Database\Connection;

$db = (new Connection())->database();

$stmt = $db->prepare("INSERT INTO users (name, email) VALUES (:name, :email)");
$stmt->execute([
    ':name' => 'John Doe', 
    ':email' => 'john@example.com'
]);

var_dump($stmt->rowCount());
```

***

### ok

Check if the last query execution was successful.

```php 
public function ok(): bool;
```

**Return Value:**

`bool` - Returns true on success, false on failure.

**Example:**

Checking Execution status:

```php
use Luminova\Database\Connection;

$db = (new Connection())->database();

$stmt = $db->prepare("SELECT * users WHERE id = :id");
$stmt->bind('id', 100);
$stmt->execute();

$result = null;

if($stmt->ok()){
    $result = $stmt->fetchAll();
}
```

***

### rowCount

Get the number of rows affected by the last executed statement.

```php
public rowCount(): int
```

**Return Value:**

`int` - Returns the number of affected rows.

***

### fetchNext

Fetch the next row from the result set as an object or array.

Alias `getNext()`.

```php
public fetchNext(int $mode = FETCH_OBJ): array|object|false
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$mode` | **int** | The result fetch mode (e.g, `FETCH_*` default: `FETCH_OBJ`). |

**Return Value:**

`array|object|false` - Returns the next row as an object or array, or false if no more rows are available.

***

### fetchAll

Fetch all rows from the result set as an array of objects or arrays.

Alias `getAll()`.

```php
public fetchAll(int $mode = FETCH_OBJ): array|object|bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$mode` | **int** | The result fetch mode (e.g, `FETCH_*` default: `FETCH_OBJ`). |

**Return Value:**

`array|object|false` - Returns an array of result objects or arrays, an empty array if no rows are found or false.

***

### getInt

Fetch the result set as a 2D array of integers.

```php
public getInt(): array
```

**Return Value:**

`array` -  Returns a 2D array of integers, or an empty array if no results are found.

***

### getCount

Get the total count of selected rows as an integer.

```php
public getCount(): int
```

**Return Value:**

`int` - Returns the total row count, or `0` if no results are found.

***

### getColumns

Retrieve a specific column or multiple columns from the result set.

```php
public getColumns(int $mode = FETCH_COLUMN): array
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$mode` | **int** | The fetch mode (default: `FETCH_COLUMN`). |

**Return Value:**

`array` -  Return and associative array or indexed array of columns depending on mode.

***

### fetch

Fetch data from the result set using a specified fetch mode.

**Return Modes**

- `RETURN_ALL` to retrieve all rows at once,  
- `RETURN_NEXT` to fetch a single row,  
- `RETURN_STREAM` to fetch rows one at a time (use in while loops).

```php
public fetch(int $returnMode = RETURN_ALL, int $fetchMode = FETCH_OBJ): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$returnMode` | **int** | The mode of the result to return (e.g., `RETURN_*`). |
| `$fetchMode` | **int** | The fetch mode (e.g., `FETCH_ASSOC`, `FETCH_OBJ`, `FETCH_*`). |

**Return Value:**

`mixed` -  Return the fetched result(s) based on the specified type and mode.

**Throws:**

- [Luminova\Exceptions\DatabaseException](/error-handlers/exceptions.md#databaseexception) - Throws an exception if an error occurs.

***

### fetchObject

Fetch the result set as an object of the specified class or stdClass.

```php
public fetchObject(?\T<string> $class = null, mixed ...$arguments): ?object
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$class` | **string&#124;null** | The full qualify class name to instantiate (default: `stdClass::class`). |
| `$arguments` | **array** | Additional arguments to initialize the class constructor with. |

**Return Value:**

`\T<object>|null` - Returns the fetched object, or null if an error occurs.

**Throws:**

- [Luminova\Exceptions\DatabaseException](/error-handlers/exceptions.md#databaseexception) - If an error occurs.

**Example:**

Define your user model class.

> **Note:** This `fetchObject` doesn't strictly require class to extend `Luminova\Base\Model as BaseModel`. 
> It can be any class.

```php
// /app/Models/User.php

namespace App\Models;

use Luminova\Base\Model as BaseModel;

class User extends BaseModel
{
    public string $name = '';
    public string $email = '';
    private ?string $password = null;

    public function __construct(string $password)
    {
        $this->password = $password;
    }
}
```

Execute query and return instance of `User` class object that resolve to result.

```php
use Luminova\Database\Connection;

$db = (new Connection())->database();

$stmt = $db->prepare("SELECT name, email users WHERE id = :id LIMIT 1");
$stmt->bind('id', 100);
$stmt->execute();

$user = null;

if($stmt->ok()){
    $user = $stmt->fetchObject(User::class, 'user-password');
    echo $user->name;
}
```

***

### getLastInsertId

Get the ID of the last inserted row or sequence value.

```php
public getLastInsertId(?string $name = null): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$name` | **string&#124;null** | Optional name of the sequence object from which the ID should be returned (MySQL/PostgreSQL only). |

**Return Value:**

`mixed` - Returns the last inserted ID, or `null/false` on failure.

***

### getResult

Retrieves a result item based on the specified mode and return type.

This method allows flexible result retrieval using a single interface, depending on the mode and return type provided.

**Available Modes:**

- `RETURN_NEXT`: Fetch the next row (same as `$stmt->fetchNext(...)`).
- `RETURN_2D_NUM`: Fetch a 2D numeric array (same as `$stmt->getInt()`).
- `RETURN_INT`: Fetch a single integer value (same as `$stmt->getCount()`).
- `RETURN_ID`: Fetch the last insert ID (same as `$stmt->getLastInsertId(...)`).
- `RETURN_COUNT`: Fetch the count of affected rows (same as `$stmt->rowCount()`).
- `RETURN_COLUMN`: Fetch specific columns (same as `$stmt->getColumns()`).
- `RETURN_ALL`: Fetch all rows (same as `$stmt->fetchAll($return)`).
- `RETURN_STMT`: Return the statement object itself (same as `$stmt->getStatement()`).
- `RETURN_RESULT`: Return the query result in MySQLi or statement in PDO.

See documentation for database [return modes](/global/env-variables.md).

```php
public getResult(int $returnMode = RETURN_ALL, int $fetchMode = FETCH_OBJ): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$mode` | **int** | The mode of the result to return (e.g., `RETURN_*`). |
| `$return` | **string** | The result fetch mode (e.g, `FETCH_*`).<br/>Used only with `RETURN_NEXT` and `RETURN_ALL` modes. |

**Return Value:**

`mixed` -  Return the result based on the specified mode and return type.

**Throws:**

- [Luminova\Exceptions\DatabaseException](/error-handlers/exceptions.md#databaseexception) - If an error occurs.

***

### free

Frees up the statement cursor and sets the statement object to null.

```php
public free(): void
```

***

### close

Frees up the statement cursor and closes the database connection.

```php
public close(): void
```

***

### profiling

Start recording the database query execution time for queries.

This method records the duration of a query in shared memory under the key `__DB_QUERY_EXECUTION_TIME__`. The stored value can later be retrieved from anywhere in your application.

> **Note:**
> The profiling mechanism is built into Luminova’s database drivers.
> Avoid calling `profiling()` directly — doing so resets the internal timer.
> Instead, use `getQueryTime()` or `getLastQueryTime()` **after** a query runs.
> This method is intended for use **only** when building or extending a custom driver.

```php
public profiling(bool $start = true, bool $finishedTransaction = false): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$start` | **bool** | Set to `true` to start profiling, or `false` to stop (default: true). |
| `$finishedTransaction` | **bool** | Set to `true` when stopping profiling after a commit or rollback transaction (default: false). |

**Example:**

To retrieve the last recorded query execution time from anywhere in your app:

```php
$time = shared(
    '__DB_QUERY_EXECUTION_TIME__', /* Default key used for profiling */
    null, /* Do not override the existing value */
    0  /* Fallback if not set */
);
```

***

### getQueryTime

Returns the total accumulated execution time for all queries executed so far.

This method returns the accumulated time spent on executing queries in either float or integer format, depending on the internal state of the query time.

```php
public getQueryTime(): float|int
```

**Return Value:**

`float|int` -  Return the total query execution time in seconds.

***

### getLastQueryTime

Returns the execution time of the most recently executed query.

This method returns the time spent on the last query execution in either float or integer format, depending on the internal state of the query time.

```php
public getLastQueryTime(): float|int
```

**Return Value:**

`float|int` - Return the last query execution time in seconds.