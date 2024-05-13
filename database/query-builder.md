## Query Builder

***

## Overview

Query Builder is a powerful ORM (Object-Relational Mapping) tool that organizes CRUD operations, enabling developers to work with higher-level objects and methods for simplified database interactions.

***

The Query Builder is a powerful ORM (Object-Relational Mapping) tool designed to simplify database interactions for developers. It enables you to work with higher-level objects and methods, abstracting the process of constructing SQL queries.

With its flexible CRUD (Create, Read, Update, Delete) functionality, you can effortlessly create complex SQL queries without dealing directly with SQL syntax. This allows you to focus more on building your applications and less on database queries.

To learn more about how to use the Query Builder,  [see examples](/database/examples.md).

***

* Class namespace: `\Luminova\Database\Builder`
* Parent class namespace: [\Luminova\Database\Connection](/database/connection.md)

***

## Methods

### db

Get database connection instance.

```php
public db(): \Luminova\Instance\DatabaseInterface 
```

**Return Value:**

`DatabaseInterface|null` - Database driver connection instance.

To get the raw database connection instance of `PDO` or `mysqli`.

```php
<?php 
$builder->db()->raw()
```

***

### stmt

Returns last query prepared statement.

```php
public stmt(): \Luminova\Interface\DriversInterface|bool|null 
```

> To return statement you need to indicate `stmt` in  `returns('stmt')`,
>  before calling method `total()`, `sum()`, `find()`, `select()`, `update()`, `delete()` or `execute()` to return query statement object.
>  > The above method will return true if statement was executed successfully otherwise it will return false.

**Return Value:**

`\Luminova\Interface\DriversInterface|bool|null` - Database driver connection instance.

*Example*

```php
$tbl = $query->table('users');
$tbl->where('pref_code', '=', 'PHP');
$tbl->returns('stmt');
if($tbl->select()){
	$stmt = $tbl->stmt();
	// Do what you want with statment object 
  var_export($stmt->getAll());
	$stmt->free();
}
```

***

### getInstance

Class shared singleton class instance.

```php
public static getInstance(): static
```

**Return Value:**

`static`  - Shared instance of database driver connection.

**Throws:**

- [\Luminova\Exceptions\DatabaseException](/exceptions/classes#DatabaseException) -  If the database connection fails.

***

### table

Sets database table name to execute your query.

```php
public table(string $table, string $alias = ''): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$table` | **string** | The table name |
| `$alias` | **string** | Optional table alias |

**Return Value:**

`self` - Class instance.

***

### join

Specifies a table join operation for `table()`, to join query.

```php
public join(string $table, string $type = 'INNER', string $alias = ''): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$table` | **string** | The name of the table to join. |
| `$type` | **string** | The type of join (default: "INNER"). |
| `$alias` | **string** | Optional alias for the joined table (optional). |

**Return Value:**

`self` - Returns the instance of the class.

***

### on

Specifies join conditions for to query tables.

*Method Chaining*

The `on` Method can be called multiple times with different arguments, each call to `on()` sets a condition for the query.

```php
public on(string $condition, string $operator = '=', mixed $value = null): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$condition` | **string** | Join condition or column name. |
| `$operator` | **string** | Join operator (default: '='). |
| `$value` | **mixed** | Value to bind to the condition or another table column. |

**Return Value:**

`self` - Returns the instance of the class.

***

### innerJoin

Shorthand for inner join table operation.

```php
public innerJoin(string $table, string $alias = ''): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$table` | **string** | The table name |
| `$alias` | **string** | join table alias |

**Return Value:**

`self` - Returns the instance of the class.

***

### leftJoin

Shorthand for left join table operation.

```php
public leftJoin(string $table, string $alias = ''): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$table` | **string** | The table name |
| `$alias` | **string** | join table alias |

**Return Value:**

`self` - Returns the instance of the class.

***

### rightJoin

Shorthand for right join table operation.

```php
public rightJoin(string $table, string $alias = ''): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$table` | **string** | The table name |
| `$alias` | **string** | join table alias |

**Return Value:**

`self` - Returns the instance of the class.

***

### crossJoin

Shorthand for cross join table operation.

```php
public crossJoin(string $table, string $alias = ''): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$table` | **string** | The table name |
| `$alias` | **string** | join table alias |

**Return Value:**

`self` - Returns the instance of the class.

***

### limit

Set query limit for `update`, `select` or `delete` statement.

```php
public limit(int $limit, int $offset): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$limit` | **int** | limit threshold |
| `$offset` | **int** | start offset query limit |

**Return Value:**

`self` - Returns the instance of the class.

***

### order

Set the order for the query results in a select statement (e.g., "ASC", "DESC").

*Method Chaining*

The `order` Method can be called multiple times with different arguments to allows chaining of multiple query orders.

```php
public order(string $column, string $order = 'ASC'): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$column` | **string** | The column name to set the order for. |
| `$order` | **string** | The order algorithm to use (either "ASC" or "DESC") |

**Return Value:**

`self` - Returns the instance of the class.

***

### group

Set query grouping for the SELECT statement.

*Method Chaining*

The `group` Method can be called multiple times with different arguments to allows chaining of multiple query grouping.

```php
public group(string $group): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$group` | **string** | The column name to group by. |

**Return Value:**

`self` - Returns the instance of the class.

***

### where

Set query where clause for `select`, `update`, `delete` statement.

```php
public where(string $column, string $operator, mixed $key): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$column` | **string** | column name |
| `$operator` | **string** | Comparison Operator |
| `$key` | **mixed** | column key value |

**Return Value:**

`self` - Returns the instance of the class.

***

### and

Set query where and conditions or `select`, `update`, `delete` statement.

*Method Chaining*

The `and` Method can be called multiple times with different arguments to allows chaining of multiple query conditions.

```php
public and(string $column, string $operator, mixed $value): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$column` | **string** | column name |
| `$operator` | **string** | Comparison operator |
| `$value` | **mixed** | column key value |

**Return Value:**

`self` - Returns the instance of the class.

***

### set

Set table update column name and value.

*Method Chaining*

The `set` Method can be called multiple times with different arguments to allows chaining of multiple query update key-values.

```php
public set(string $column, mixed $value): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$column` | **string** | column name |
| `$value` | **mixed** | column key value  to update|

**Return Value:**

`self` - Returns the instance of the class.

***

### or

Set query `OR` condition operator for a query like `WHERE column = 1 OR comum2 = 1 `.

*Method Chaining*

The `or` Method can be called multiple times with different arguments to allows chaining of multiple query `OR` conditions.

```php
public or(string $column, string $operator, mixed $value): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$column` | **string** | column name |
| `$operator` | **string** | Comparison operator |
| `$value` | **mixed** | column key value |

**Return Value:**

`self` - Returns the instance of the class.

***

### andor

Set query strict `OR` condition operators for queries like `WHERE column = 1 AND (comum2 = 1 OR column3 = 3) `.

*Method Chaining*

The `andor` Method can be called multiple times with different arguments to allows chaining of multiple query `AND (? OR ?)` conditions.

```php
public andor(string $column, mixed $operator, mixed $value, string $orColumn, mixed $orOperator, mixed $orValue): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$column` | **string** | column name |
| `$operator` | **string** | Comparison operator |
| `$value` | **mixed** | column key value |
| `$orColumn` | **string** | column name |
| `$orOperator` | **string** | Comparison operator |
| `$orValue` | **mixed** | column or key value |

**Return Value:**

`self` - Returns the instance of the class.

***

### in

Set query where `IN`  operator expression, allows you to specify array values to search.

```php
public in(string $column, array $lists = []): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$column` | **string** | column name |
| `$lists` | **array<int, mixed>** | Array values to search |

**Return Value:**

`self` - Returns the instance of the class.

***

### inset

Set query operator FIND_IN_SET() expression.

```php
public inset(string $search, string $operator = '=', array $list = []): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$search` | **string** | search value |
| `$operator` | **string** | allow specifying the operator for matching (e.g., > or =) |
| `$list` | **array<int, mixed>** | of values |

**Return Value:**

`self` - Returns the instance of the class.

***

### returns

Set a return type for query result `stmt`, `array` or `object`, the default return type is `object`.

When the return type is set to `stmt` if query executed successfully method `total()`, `sum()`, `find()`, `select()`, `update()`, `delete()` or `execute()` will return true else false.

```php
public returns(string $type): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$type` | **string** | Return type`stmt`, 'object' or 'array' |

**Return Value:**

`self` - Returns the instance of the class.

***

### datetime

Get date/time format for storing SQL.

```php
public static datetime(string $format = 'datetime', ?int $timestamp = null): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$format` | **string** | Format to return default is `datetime`.<br />Available time formats.<br />- `time`  - Return time format from timestamp<br />- `datetime` - Return SQL datetime format<br />- `date`  - Return SQL date format. |
| `$timestamp` | **int&#124;null** | Optional timestamp |

**Return Value:**

`string` - Returns formatted date/time/timestamp.

***

### cache

Cache the query result and return cached data when next user request the same content.
To cache a query result, you must call cache method before `select()`, `find()`, or `total()` method is called.

```php
public cache(string $key, string $storage = null, DateTimeInterface|int $expiry = 7 * 24 * 60 * 60): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string** | The cache storage key |
| `$storage` | **string** | Optional storage name, but it's recommended to void storing large data in one file. |
| `$expiry` | **DateTimeInterface&#124;int** | The cache expiry time in seconds or datetime interface (default: 7 days). |

**Return Value:**

`self` - Returns the instance of the class.

> Returning statement doesn't support caching.

***

### caching

Caching provides the flexibility to enable or disable all caches universally. This is particularly useful for debugging or temporarily disabling database caching throughout your application without the need to manually remove `cache()` method calls from your all code. 

To disable all database caching, simply call this method with a `false` parameter.

```php
public caching(bool $enable): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$enable` | **bool** | The caching status action |

**Return Value:**

`self` - Returns the instance of the class.

> By default caching is enabled once you call `cache()` method.

***

### insert

Insert records into a database table, either by passing an array of key-value pairs with values, or by using the `set()` method to specify the data to update.

```php
public insert(array<string,mixed> $values, bool $prepare = true): int|bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$values` | **array<string,mixed>** | array of values to insert into table |
| `$prepare` | **bool** | Use bind values and execute prepare statement instead of query |

**Return Value:**

- `int` - Returns number of affected rows or false on failure.
- `bool` - Returns true if method `returns()` was set to `stmt` and query execution succeed otherwise false.

***

### select

Select records from database table.

```php
public select(array<int,string> $columns = ['*']): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$columns` | **array<int,string>** | Array of column names to select. |

**Return Value:**

`mixed` - returns selected rows or false on failure.

***

### query

Allows you to execute more complex query by building your own `SQL` query.

```php
public query(string $query): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$query` | **string** | SQL query string |

**Return Value:**

`self` - Returns the instance of the class.

**Throws:**

- [\Luminova\Exceptions\DatabaseException](/exceptions/classes#DatabaseException) - when query is empty.

*Example*

The below example show how you can use custom query builder to create and execute your SQL queries.

```php
$query = $builder->query("
	UPDATE addresses 
	SET address_default = (address_id = :address_id) 
	WHERE address_customer_id = :customer_id
");
$query->execute([
	'address_id' => $id,
	'customer_id' => $customer_id
]);
```

***

### execute

Executes your SQL query after constructing it in `query()` method.

To cache query result you must initialize `cache()` method before calling execute method. 
It also allows you to pass your array placeholders, of key-value pairs with values to be executed with query.
The key will be usage as placeholder name while value is the value to bind to the placeholder.

```php
public execute(array $placeholder = null, int $mode = RETURN_ALL): mixed
```

**Modes**

- [Return Modes](/global/constants.md#database-fetch-modes) - Database query return modes.

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$binds` | **array<string, mixed>** | binds placeholder to query |
| `$mode` | **int** | The return type mode |

**Return Value:**

`mixed` - Return result or prepared statement depending on `$mode` otherwise false on failed.

**Throws:**

- [\Luminova\Exceptions\DatabaseException](/exceptions/classes#DatabaseException) - If placeholder key is not a valid string.

***

### find

Select next or a single  record from database table.

```php
public find(array<int,string> $columns = ['*']): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$columns` | **array<int,string>** | select columns to return |

**Return Value:**

`mixed` - returns selected row or false on failure.

***

### total

Select total counts of records from database table.

```php
public total(string $column = '*'): int|bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$column` | **string** | column to index counting (default: *) |

**Return Value:**

- `int` - Returns total counts of records or false on failure.
- `bool` - Returns true if `returns()` is set to `stmt` and query execution succeed else false.

***

### sum

Select total sum of records from database table.

```php
public sum(string $column): int|float|bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$column` | **string** | The column to index summing |

**Return Value:**

- `int|float` - Returns total sum of records or false on failure.
- `bool` - Returns true if `returns()` is set to `stmt` and query execution succeed else false.

***

### update

Update database table records with a new value.
You can pass an array of key-value pairs with values to update method or use the `set()` method.

```php
public update(array<string,mixed> $setValues = []): int|bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$setValues` | **array<string,mixed>** | Optionally pass an associative array of columns and values to update |

**Return Value:**

- `int` - Returns number of affected rows or false on failure.
- `bool` - Returns true if `returns()` is set to `stmt` and query execution succeed else false.

***

### max

Set max query limit for `update` and `delete` queries statement.

```php
public max(int $limit): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$limit` | **int** | maximum number of records to update or delete  |

**Return Value:**

`self` - The class instance.

***

### delete

Delete record from database table.

```php
public delete(): int|bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$limit` | **int** | row limit |

**Return Value:**

- `int` - Returns number of affected rows or false on failure.
- `bool` - Returns true if `returns()` is set to `stmt` and query execution succeed else false.

***

### errors

Get query execution errors.

```php
public errors(): array
```

**Return Value:**

`array` - Returns array of error information

***

### transaction

Begin a query transaction.

```php
public transaction(): self
```

**Return Value:**

`self` - The class instance.

***

### commit

Commit a transaction.

```php
public commit(): void
```

***

### rollback

Rollback a transaction to default on failure.

```php
public rollback(): void
```

***

### truncate

Delete all records in a table, and reset table auto increment to `1`.

```php
public truncate(bool $transaction = true): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$transaction` | **bool** | Use query transaction (default: true). |

**Return Value:**

`bool` - Returns true if completed else false.

**Throws:**

- [\Luminova\Exceptions\DatabaseException](/exceptions/classes#DatabaseException) - Throws if execution encountered error.

***

### exec

Execute an SQL statement and return the number of affected rows.

```php
public exec(string $query): int|bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$query` | **string** | SQL query statement to execute. |

**Return Value:**

`int|bool` - Returns number of affected rows or false on failure.

**Throws:**

- [\Luminova\Exceptions\DatabaseException](/exceptions/classes#DatabaseException) - Throws if execution encountered error.

***

### drop

Drop table from database

```php
public drop(): int|bool
```

**Return Value:**

`int|bool` - Returns number of affected rows or false on failure.

**Throws:**

- [\Luminova\Exceptions\DatabaseException](/exceptions/classes#DatabaseException) - Throws if execution encountered error.

***

### create

Create table from table Scheme model.

```php
public create(\Luminova\Database\Scheme $scheme): int|bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$scheme` | **\Luminova\Database\Scheme** | table column instance |

**Return Value:**

`int|bool` - Returns number of affected rows or false on failure.

**Throws:**

- [\Luminova\Exceptions\DatabaseException](/exceptions/classes#DatabaseException) - Throws if execution encountered error.

***

### scheme

Get table scheme mode class instance.

```php
public scheme(): \Luminova\Database\Scheme
```

**Return Value:**

`Scheme` - Returns scheme model class instance.

***

### manager

Retrieves the database manager class instance.
Returns a singleton instance of the Manager class initialized with the current database connection.

```php
public manager(): \Luminova\Database\Manager
```

**Return Value:**

`Manager` - Database manager class instance.

**See Also:**

*  - [Database Manager](/database/manager.md) - Database manager class.

***

### export

Exports a database table and downloads it to the browser as JSON or CSV format.

```php
public export(string $as = 'csv', string|null $filename = null, array $columns = ['*']): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$as` | **string** | Export as csv or json format. |
| `$filename` | **string&#124;null** | Filename to download. |
| `$columns` | **array** | Table columns to export (default: all). |

**Return Value:**

`bool` - Return true if export is successful, false otherwise.

**Throws:**

- [\Luminova\Exceptions\DatabaseException](/exceptions/classes#DatabaseException) - If an invalid format is provided or if unable to create the export.

***

### backup

Creates a backup for the database.

```php
public backup(string|null $filename = null): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$filename` | **string&#124;null** | Filename to store the backup. |

**Return Value:**

True if backup is successful, false otherwise.

**Throws:**

- [\Luminova\Exceptions\DatabaseException](/exceptions/classes#DatabaseException) - If unable to create the backup directory or if failed to create the backup.

***

### dump

Debug dump statement information for the last statement execution for debugging.

```php
public dump(): bool|null
```

**Return Value:**

`bool|null` - Returns trues else false or null on failure.

***

### reset

Reset query conditions, parameters and Free database resources ready to execute next query without interruption.

```php
public reset(): void
```

***

### free

Frees up the statement cursor and sets the statement object to null.

```php
public free(): void
```

***

### close

Close database connection and free statement cursor.

```php
public close(): void
```