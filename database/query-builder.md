# Query Builder

***

## Overview

Query Builder is a powerful ORM (Object-Relational Mapping) tool that organizes CRUD operations, enabling developers to work with higher-level objects and methods for simplified database interactions.

***

## Introduction

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
public db(): \Luminova\Instance\DatabaseInterface|null
```

**Return Value:**

`\Luminova\Interface\DatabaseInterface|null` - Return database driver instance.

**Return Raw Connection Object**

To get the raw database connection instance of `PDO` or `MYSQLI`.

```php
<?php 
$conn = $builder->db()->raw();
```

***

### getInstance

To get a shared singleton instance of builder class.

```php
public static getInstance(): static
```

**Return Value:**

`static` - Return new static instance of builder class.

**Throws:**

- [\Luminova\Exceptions\DatabaseException](/exceptions/classes.md#databaseexception) -  If the database connection fails.

***

### table

Sets the database table name to build query for.

```php
public table(string $table, string|null $alias = null): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$table` | **string** | The table name (non-empty string). |
| `$alias` | **string&#124;null** | Optional table alias (default: NULL). |

**Return Value:**

`self` - Returns the instance of builder class.

**Throws:**

- [\Luminova\Exceptions\InvalidArgumentException](/exceptions/classes.md#invalidargumentexception) - Throws if an invalid table name is provided.

***

### join

To join main table `table()` with another using `JOIN` operators (e.g `INNER JOIN`, `FULL JOIN`).

```php
public join(string $table, string $type = 'INNER', ?string $alias = null): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$table` | **string** | The name of the table to join. |
| `$type` | **string** | The type of join (default: &quot;INNER&quot;). |
| `$alias` | **string&#124;null** | Optional table join alias (default: NULL). |

**Return Value:**

`self` - Returns the instance of builder class.

**Throws:**

- [\Luminova\Exceptions\InvalidArgumentException](/exceptions/classes.md#invalidargumentexception) - Throws if invalid argument is provided.

**Join Types**

- `INNER` - Returns rows with matching values in both tables.
- `LEFT`  - Returns all rows from the left table and matching rows from the right table, or NULLs for non-matching rows from the right table.
- `RIGHT` - Returns all rows from the right table and matching rows from the left table, or NULLs for non-matching rows from the left table.
- `CROSS` - Returns the Cartesian product of the two tables.
- `FULL`  - Returns rows with matching values in either table, with NULLs for non-matching rows from either table.
- `FULL OUTER` - Returns all rows when there is a match in either the left or right table, or NULL from the side that does not have a match.

***

### on

Specifies join conditions to query table, the `on` method can be called multiple times with different arguments, each call to `on` sets a condition for the query.

```php
public on(string $condition, string $operator, mixed $value): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$condition` | **string** | Join condition or column name. |
| `$operator` | **string** | Join operator (default: '='). |
| `$value` | **mixed** | Value to bind to the condition or another table column. |

**Return Value:**

`self` - Returns the instance of builder class.

***

### innerJoin

Sets table join condition as `INNER JOIN`.

```php
public innerJoin(string $table, string|null $alias = null): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$table` | **string** | The table name. |
| `$alias` | **string&#124;null** | Optional table join alias (default: NULL). |

**Return Value:**

`self` - Returns the instance of the class.

**Throws:**

- [\Luminova\Exceptions\InvalidArgumentException](/exceptions/classes.md#invalidargumentexception) - Throws if invalid argument is provided.

***

### leftJoin

Sets table join condition as `LEFT JOIN`.

```php
public leftJoin(string $table, string|null $alias = null): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$table` | **string** | The table name |
| `$alias` | **string&#124;null** | Optional table join alias (default: NULL). |

**Return Value:**

`self` - Returns the instance of the class.

**Throws:**

- [\Luminova\Exceptions\InvalidArgumentException](/exceptions/classes.md#invalidargumentexception) - Throws if invalid argument is provided.

***

### rightJoin

Sets table join condition as `RIGHT JOIN`.

```php
public rightJoin(string $table, string|null $alias = null): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$table` | **string** | The table name |
| `$alias` | **string&#124;null** | Optional table join alias (default: NULL). |

**Return Value:**

`self` - Returns the instance of the class.

**Throws:**

- [\Luminova\Exceptions\InvalidArgumentException](/exceptions/classes.md#invalidargumentexception) - Throws if invalid argument is provided.

***

### crossJoin

Sets table join condition as `CROSS JOIN`.

```php
public crossJoin(string $table, string|null $alias = null): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$table` | **string** | The table name |
| `$alias` | **string&#124;null** | Optional table join alias (default: NULL). |

**Return Value:**

`self` - Returns the instance of the class.

**Throws:**

- [\Luminova\Exceptions\InvalidArgumentException](/exceptions/classes.md#invalidargumentexception) - Throws if invalid argument is provided.

***

### fullJoin

Sets table join condition as `FULL JOIN`.

```php
public fullJoin(string $table, string|null $alias = null): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$table` | **string** | The table name |
| `$alias` | **string&#124;null** | Optional table join alias (default: NULL). |

**Return Value:**

`self` - Returns the instance of the class.

**Throws:**

- [\Luminova\Exceptions\InvalidArgumentException](/exceptions/classes.md#invalidargumentexception) - Throws if invalid argument is provided.

***

### fullOuterJoin

Sets table join condition as `FULL OUTER JOIN`.

```php
public fullOuterJoin(string $table, string|null $alias = null): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$table` | **string** | The table name |
| `$alias` | **string&#124;null** | Optional table join alias (default: NULL). |

**Return Value:**

`self` - Returns the instance of the class.

**Throws:**

- [\Luminova\Exceptions\InvalidArgumentException](/exceptions/classes.md#invalidargumentexception) - Throws if invalid argument is provided.

***

### limit

Set the limit and offset for `SELECT` query execution.
This method can be called when working while calling methods like `total`, `select` `fetch`, `count`, `sum` and `average`.

```php
public limit(int $limit, int $offset): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$limit` | **int** | The maximum number of results to return. |
| `$offset` | **int** | The starting offset for the results (default is 0). |

**Return Value:**

`self` - Returns the instance of the builder class.

***

### max

Set the maximin record to execute when called `update` or `delete`method.

```php
public max(int $limit): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$limit` | **int** | number of records to update or delete. |

**Return Value:**

`self` - Return instance of builder class.

***

### order

Set result return order for query selection (e.g., `id ASC`, `date DESC`).
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

`self` - Return instance of builder class.

***

### orderByMatch

Set the result return order when match `against` method is called.

```php
public orderByMatch(array $columns, string|int|float $value, string $mode = 'NATURAL_LANGUAGE', string $order = 'ASC'): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$columns` | **array** | The column names to index match order. |
| `$value` | **string&#124;int&#124;float** | The value to match against in order. |
| `$mode` | **string** | The comparison match mode operator.<br />Optionally you can choose any of these modes or pass your own mode.<br />    - NATURAL_LANGUAGE<br />    - BOOLEAN<br />    - NATURAL_LANGUAGE_WITH_QUERY_EXPANSION<br />    - WITH_QUERY_EXPANSION |
| `$order` | **string** | The order algorithm to use (either &quot;ASC&quot; or &quot;DESC&quot;). |

**Return Value:**

`self` - Returns an instance of the class.

***

### group

Set query grouping for the SELECT statement.
The `group` Method can be called multiple times with different arguments to allows chaining of multiple query grouping.

```php
public group(string $group): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$group` | **string** | The column name to group by. |

**Return Value:**

`self` - Return instance of builder class.

***

### where

Set query condition for `WHERE` operator.
This can be called while working with methods like: `select`, `find`, `stmt`, `update`, `delete`, `sum`, `total` or `average` methods execution.

```php
public where(string $column, string $operator, mixed $key): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$column` | **string** | The column name. |
| `$operator` | **string** | The comparison operator (e.g. `=`, `&gt;=`, `&lt;&gt;`). |
| `$value` | **mixed** | The where condition column value. |

**Return Value:**

`self` - Return instance of builder class.
***

### and

Set query condition for `AND` operator.
The `and` method can be called multiple times with different arguments to allows chaining of multiple query conditions.

```php
public and(string $column, string $operator, mixed $value): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$column` | **string** | The column name. |
| `$operator` | **string** | The comparison operator (e.g. `=`, `&gt;=`, `&lt;&gt;`). |
| `$value` | **mixed** | The and condition column value. |

**Return Value:**

`self` - Return instance of builder class.

***

### against

The `against` method allows you to perform database querying with the `match` operator for efficient matching against specified columns. It sets the query match columns and mode. It also support chaining multiple against conditions.

```php
public against(array $columns, string $mode, mixed $value): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$columns` | **array** | The column names to match against. |
| `$mode` | **string** | The comparison match mode operator. |
| `$value` | **mixed** | The value to match against. |

**Modes:**

You can use any of the following matching modes or pass your own mode:

- `NATURAL_LANGUAGE`: This mode provides a natural language search experience.
- `BOOLEAN`: This mode enables Boolean search capabilities.
- `NATURAL_LANGUAGE_WITH_QUERY_EXPANSION`: This mode extends the `NATURAL_LANGUAGE` mode with query expansion.
- `WITH_QUERY_EXPANSION`: This mode extends the standard search with query expansion.

**Return Value:**

`self` - Return instance of builder class.

***

### set

To stage the table column name and value which will be use when called `update` method.
The `set` Method can be called multiple times with different arguments to allows chaining of multiple query update key-values.

```php
public set(string $column, mixed $value): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$column` | **string** | The column name to update. |
| `$value` | **mixed** | The column key value to update. |

**Return Value:**

`self` - Return instance of builder class.

***

### or

To set query `OR` condition operator for execution.
The `or` method can be called multiple times with different arguments to allows chaining of multiple query `OR` conditions.

```php
public or(string $column, string $operator, mixed $value): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$column` | **string** | The column name. |
| `$operator` | **string** | The comparison operator to use. |
| `$value` | **mixed** | The column key value. |

**Return Value:**

`self` - Return instance of builder class.

***

### orGroup

To set an `OR` group of conditions (e.g `(foo = 1 OR bar = 2)`).

```php
public orGroup(array $conditions): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$conditions` | **array** | Array of conditions to be grouped with `OR`. |

**Return Value:**

`self` - Return instance of builder class.

***

### andGroup

Adds a group of conditions combined with AND to the query.

```php
public andGroup(array $conditions): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$conditions` | **array** | Array of conditions to be grouped with AND. |

**Return Value:**

`self` - Return instance of builder class.

***

### Group Example

Here is an example of how you can implement `orGroup` and `andGroup`.

```php
<?php 
$builder->orGroup([
	['column1' => ['operator' => '=', 'value' => 1]],
	['column2' => ['operator' => '=', 'value' => 2]]
]);
```

```php
<?php 
$builder->andGroup([
	['column1' => ['operator' => '=', 'value' => 1]],
	['column2' => ['operator' => '=', 'value' => 2]]
]);
```

> *Note:*
>
> The binding columns must be the array key while the child elements must have only 2 index with keys `operator` and `value`.

***

### orBind

To bind multiple `OR` group conditions and combined to a single group (e.g: `((foo = 1 OR bar = 2) OR (baz = 3 OR bra = 4))`).

```php
public orBind(array $group1, array $group2, string $bind = 'OR'): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$group1` | **array** | First group of conditions. |
| `$group2` | **array** | Second group of conditions. |
| `$bind` | **string** | The type of logical operator to use in binding groups together (default: 'OR').<br />- `AND` or `OR`. |

**Return Value:**

`self` - Return instance of builder class.

***

### andBind

Adds two groups of conditions combined with AND condition.
The `andBind` is similar with `orBind`, except that it uses `AND` operator to bind group conditions before combining to a single group (e.g: `((foo = 1 AND bar = 2) OR (baz = 3 AND bra = 4))`).

```php
public andBind(array $group1, array $group2, string $bind = 'AND'): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$group1` | **array** | First group of conditions. |
| `$group2` | **array** | Second group of conditions. |
| `$bind` | **string** | The type of logical operator to use in binding groups together (default: 'AND').<br />- `AND` or `OR`. |

**Return Value:**

`self` - Return instance of builder class.

***

### Binds Group Example

Here is an example of how you can implement `orBind` and `andBind`.

```php
<?php 
$builder->orBind([
	['column1' => ['operator' => '=', 'value' => 1]],
	['column2' => ['operator' => '=', 'value' => 2]]
],
[
	['column3' => ['operator' => '=', 'value' => 3]],
	['column4' => ['operator' => '=', 'value' => 4]]
]);
```

```php
<?php 
$builder->andBind([
	['column1' => ['operator' => '=', 'value' => 1]],
	['column2' => ['operator' => '=', 'value' => 2]]
],
[
	['column3' => ['operator' => '=', 'value' => 3]],
	['column4' => ['operator' => '=', 'value' => 4]]
]);
```

> *Note:*
>
> The binding columns must be the parent array key while the child elements should only have 2 elements with key `operator` and `value`.

***

### in

Set query where `IN`  operator expression, allows you to specify array values to search.

```php
public in(string $column, array&lt;int,mixed&gt; $list): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$column` | **string** | The column name. |
| `$list` | **array<int,mixed>** | The expression values. |

**Return Value:**

`self` - Return instance of builder class.

**Throws:**

- [\Luminova\Exceptions\InvalidArgumentException](/exceptions/classes.md#invalidargumentexception) - If values is not provided.

***

### inset

Set query operator FIND_IN_SET() expression.

```php
public inset(string $search, string $operator, array&lt;int,mixed&gt; $list): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$search` | **string** | The search value. |
| `$operator` | **string** | allow specifying the operator for matching (e.g., &gt; or =). |
| `$list` | **array<int,mixed>** | The expression values. |

**Return Value:**

`self` - Return instance of builder class.

**Throws:**

- [\Luminova\Exceptions\InvalidArgumentException](/exceptions/classes.md#invalidargumentexception) - If values is not provided.

***

### returns

To change the result return type to either `object` or `array`, call this method before finally executing `fetch`, `find` or `select` method. The default result response is `object`.

```php
public returns(string $type): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$type` | **string** | The result return type `object` or `array`. |

**Return Value:**

`self` - Return instance of builder class.

**Throws:**

- [\Luminova\Exceptions\InvalidArgumentException](/exceptions/classes.md#invalidargumentexception) - Throws if an invalid type is provided.

***

### debug

To enable query string debugging, this allow you to preview the compiled query string ready to be executed.

```php
public debug(): self
```

**Return Value:**

`self` - Return instance of builder class.

> If this method is called in a production environment, the query string will be logged using the `debug` level.
> Additionally when debug is enabled, execution will always fail and caching will be disabled.

***

### printDebug

To get the query string debug information, this method will return an array containing the query string in `mysql` and `pdo` placeholder format. Additionally it will show the exactly the binding mapping for every column in the query string.

```php
public printDebug(): array<string,mixed>;
```

**Return Value:**

`array<string,mixed>` - Return array containing query information.

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

### caching

The caching method provides the flexibility to enable or disable all caches universally. This is particularly useful for debugging or temporarily disabling database caching throughout your application without the need to manually remove `cache()` method calls from your all code. 

To disable all database caching, simply call this method with a `false` parameter.

```php
public caching(bool $enable): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$enable` | **bool** | The caching status action. |

**Return Value:**

`self` - Return instance of builder class.

> By default caching is enabled once you call `cache` method.

***

### cache

To cache query result response, and return cached data when next user request the same content.
In other for caching to work, you must call `cache` method before `select`, `find`, `execute`, `fetch`, `sum`, `average` or `total` method is called.

```php
public cache(string $key, string $storage = null, DateTimeInterface|int $expiry = 7 * 24 * 60 * 60, string|null $folder = null): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string** | The cache storage key |
| `$storage` | **string** | Optional storage name, but it's recommended to void storing large data in one file. |
| `$expiry` | **DateTimeInterface&#124;int** | The cache expiry time in seconds or datetime interface (default: 7 days). |
| `$folder` | **string&#124;null** | Optionally set a sub folder name to store caches. |

**Return Value:**

`self` - Return instance of builder class.

> Using `stmt` method for returning preferred statement object doesn't support caching.
> To use cache, you will need to manually implement it.

***

### insert

To Insert records into a database table, either by passing an array of key-value pairs with values, or by using the `set()` method to specify the data to update.

Additionally the insert method supports inserting array value to database, but `The array will be JSON encoded`, if wish to insert a serialized string, you should do that before passing to insert

```php
public insert(array<string,mixed> $values, bool $prepare = true): int
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$values` | **array<int,array<string,mixed>>** | An associative arrays,<br />each containing column names and corresponding values to insert into the table. |
| `$prepare` | **bool** | Use bind values and execute prepare statement instead of query (default: true). |

**Return Value:**

`int` - Return number of affected rows or 0 if none was inserted.

***

### select

To select multiple records from table, call `select` method after you have prepared other conditional methods.

```php
public select(array<int,string> $columns = ['*']): mixed

```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$columns` | **array<int,string>** | Array of column names to select. |

**Return Value:**

`mixed` - Return selected rows, otherwise false on failure.

***

### fetch

The `fetch` method allows you to retrieve records from table, by passing desired fetch mode and result type.

```php
public fetch(string $result = 'all', int $mode = FETCH_OBJ, array<int,string> $columns = ['*']): object|null|array|int|float|bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$result` | **string** | The fetch result type (next or all). |
| `$mode` | **int** | The fetch result mode FETCH_*. |
| `$columns` | **array<int,string>** | The table columns to return (default: *). |

**Return Value:**

`object|null|array|int|float|bool` - Return selected records, otherwise false on failure.

**Throws:**

- [\Luminova\Exceptions\DatabaseException](/exceptions/classes.md#databaseexception) - If where method was not called.

***

### stmt

Returns query prepared statement based on build up method conditions.

```php
public stmt(array<int,string> $columns = ['*']): \Luminova\Interface\DatabaseInterface;
```

> *Note:*
>
> To return prepared statement object don't need to call any of these methods `total`, `sum`, `find`, `select`, `average`, `fetch` or `execute`, calling them will result in `stmt` returning `NULL`.

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$columns` | **array<int,string>** | The table columns to return (default: *). |

**Return Value:**

`\Luminova\Interface\DatabaseInterface` - Return prepared statement object if query is successful otherwise null.

**Example**

To learn more about methods included in statement object see [Database driver documentation](/database/drivers.md).

```php
<?php
$tbl = $builder->table('users');
$tbl->where('pref_code', '=', 'PHP');
$stmt = $tbl->stmt();

// Do what you want with statement object 
var_export($stmt->getAll());
$stmt->free();
```

***

### query

Build a custom SQL query string to execute when calling the `execute` method.
This allows you to execute more customized query string, it also support caching result response.

```php
public query(string $query): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$query` | **string** | SQL query string |

**Return Value:**

`self` - Return instance of builder class.

**Throws:**

- [\Luminova\Exceptions\DatabaseException](/exceptions/classes.md#databaseexception) - when query is empty.

*Example*

The below example show how you can use custom query builder to create and execute your SQL queries.

```php
<?php
$query = $builder->query("
	UPDATE addresses 
	SET address_default = (address_id = :address_id) 
	WHERE address_customer_id = :customer_id
");
$response = $query->execute([
	'address_id' => $id,
	'customer_id' => $customer_id
]);
```

***

### execute

To execute an SQL query string that was previously constructed in the `query` method, tenable caching of query result, you must call `cache` method before calling the `execute` method. 

Optionally, it allows you to pass an array of placeholders, of key-value pairs with values to be executed with query.
The key will be usage as placeholder name while value is the value to bind to the placeholder.

It uses prepared statements if placeholder array is passed, otherwise, it will fallback to `query` execution, so ensure that values passed directly to the query are escaped.

```php
public execute(?array $placeholder = null, int $mode = RETURN_ALL): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$binds` | **array<string,mixed>&#124;null** | The placeholder and value to binds with query string (default: `NULL`). |
| `$mode` | **int** | Result return mode RETURN_* (default: RETURN_ALL). |

**Return Value:**

`mixed` - Return result or prepared statement depending on `$mode` otherwise false on failed.

**Modes**

- [Constant database return modes](/global/constants.md) - To see reference on database return modes.

**Throws:**

- [\Luminova\Exceptions\DatabaseException](/exceptions/classes.md#databaseexception) - If placeholder key is not a valid string.

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

To calculate the total number of records table,

```php
public total(string $column = '*'): int|bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$column` | **string** | The column to index calculation (default: *). |

**Return Value:**

`int|bool` - Return total number of records in table, otherwise false if execution failed.

***

### sum

To calculate a total sum of a numeric column in the table.

```php
public sum(string $column): int|float|bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$column` | **string** | The column to index summing |

**Return Value:**

`int|float|bool` - Return total sum columns, otherwise false if execution failed.

**

### average

To calculate the average value of a numeric column in the table.

```php
public average(string $column): int|float|bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$column` | **string** | The column to calculate the average. |

**Return Value:**

`int|float|bool` - Return total average of columns, otherwise false if execution failed.

***

### update

To update database table records with a new data.
You can pass an array of column key pairs with values to update method or use the `set` method stage your columns and values to update.

```php
public update(array<string,mixed> $setValues = []): int|bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$setValues` | **array<string,mixed>** | Optionally pass an associative array of columns and values to update |

**Return Value:**

`int` - Return number of affected rows.

**Throws:**

- [\Luminova\Exceptions\DatabaseException](/exceptions/classes.md#databaseexception) - Throw if error occurred while updating.

> *Note:* 
> 
> Passing an array value to update field will be automatically convert to `JSON` string, so ensure that your table field is designed to accept json string before passing an array.

***

### delete

To delete record from database table.

```php
public delete(): int
```

**Return Value:**

`int` - Return number of affected rows.

**Throws:**

- [\Luminova\Exceptions\DatabaseException](/exceptions/classes.md#databaseexception) - Throw if error occurs.

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

Begins a transaction with optional read-only isolation level and `SAVEPOINT` for `PDO`.

```php
public transaction(int $flags = 0, ?string $name = null): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$flags` | **int** | Optional flags to set transaction properties. (Default: `0`).<br>No predefined flags for `PDO`, specify `4` to create read-only isolation. |
| `$name` | **string&#124;null** | Optional name.<br> If provided in `PDO`, `SAVEPOINT` will be created with name instead. |

**Return Value:**

`bool` - Return true if transaction started successfully, otherwise false.

**Throws:**

- [\Luminova\Exceptions\DatabaseException](/exceptions/classes.md#databaseexception) - Throws exception on `PDO` if failure to set transaction isolation level or create `savepoint`.

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

- [\Luminova\Exceptions\DatabaseException](/exceptions/classes.md#databaseexception) - Throws exception on `PDO` if failure to create `SAVEPOINT`.

***

### truncate

Delete all records in a table, and reset table auto increment to `1`.

```php
public truncate(bool $transaction = true): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$transaction` | **bool** | Weather to use transaction (default: true). |

**Return Value:**

`bool` - Return true truncation was completed, otherwise false.

**Throws:**

- [\Luminova\Exceptions\DatabaseException](/exceptions/classes.md#databaseexception) - Throws if execution encountered error.

***

### temp

Creates a temporary table and copies all records from the main table to the temporary table.

```php
public temp(bool $transaction = true): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$transaction` | **bool** | Whether to use a transaction (default is true). |

**Return Value:**

`bool` - Returns true if the operation was successful; false otherwise.

**Throws:**

- [\Luminova\Exceptions\DatabaseException](/exceptions/classes.md#databaseexception) - Throws an exception if a database error occurs during the operation.

**Example**

```php
<?php
if ($builder->table('users')->temp()) {
   $data = $builder->table('temp_users')->select();
}
```

> **Note:**
> Temporary tables are automatically deleted when the current session ends.
> To query the temporary table, use the `temp_` prefix before the main table name.

***

### exec

Execute an `SQL` query string and return the number of affected rows.

```php
public exec(string $query): int
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$query` | **string** | The `SQL` query string to execute. |

**Return Value:**

`int` - Return number affected rows.

**Throws:**

- [\Luminova\Exceptions\DatabaseException](/exceptions/classes.md#databaseexception) - Throws if execution encountered error.

***

### drop

Drop database table if table exists.

```php
public drop(bool $transaction = false): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$transaction` | **bool** | Whether to use a transaction (default: false). |

**Return Value:**

`bool` - Return true if table was successfully dropped, false otherwise.

**Throws:**

- [\Luminova\Exceptions\DatabaseException](/exceptions/classes.md#databaseexception) - Throws if error occurs.

***

### dropTemp

Drop a temporal database table if table exists.

```php
public dropTemp(bool $transaction = false): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$transaction` | **bool** | Whether to use a transaction (default: false). |

**Return Value:**

`bool` - Return true if table was successfully dropped, false otherwise.

**Throws:**

- [\Luminova\Exceptions\DatabaseException](/exceptions/classes.md#databaseexception) - Throws if error occurs.

***

### manager

Retrieves the database manager class shared instance, initialized with the current database connection.

```php
public manager(): \Luminova\Database\Manager
```

**Return Value:**

`\Luminova\Database\Manager` - Database manager class instance.

**See Also:**

[Database Manager](/database/manager.md) - Database manager documentation.

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

- [\Luminova\Exceptions\DatabaseException](/exceptions/classes.md#databaseexception) - If an invalid format is provided or if unable to create the export.

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

- [\Luminova\Exceptions\DatabaseException](/exceptions/classes.md#databaseexception) - If unable to create the backup directory or if failed to create the backup.

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