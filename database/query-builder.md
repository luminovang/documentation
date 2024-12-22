# ORM Database Query Builder Module

***

## Overview

Database Builder Class: An Object-Relational Mapping Tool for Simplified CRUD Operations and Object-Oriented Database Management.

***

## Introduction

The Luminova database query `Builder` class is a powerful ORM (Object-Relational Mapping) tool designed to simplify database interactions. It enables developers to work with intuitive, object-oriented methods, abstracting the complexities of constructing SQL queries.

With its versatile CRUD (Create, Read, Update, Delete) capabilities, you can effortlessly build both simple and complex SQL queries without writing raw SQL. This allows you to focus more on developing your application and less on the intricacies of database queries. Each method in the `Builder` class is carefully crafted with names and functionality that align closely with SQL syntax, making it easy to grasp if you're familiar with SQL.

For more detailed examples and usage instructions, refer to the [query Builder documentation](/database/examples.md).

***

## Class Definition

* Class namespace: `\Luminova\Database\Builder`
* This class implements: [\Luminova\Interface\LazyInterface](/interface/classes.md#lazyinterface),

***

## Methods

> **Note:** This class constructor is private preventing instantiation from outside.
> To initialize the class you can either use the singleton methods `Builder::getInstance()` or `Builder::table()`.

**Example:**

Create instance of Builder to access class methods without initializing table.

```php
<?php
$builder = Builder::getInstance();
```

Create instance of Builder to access with a table name.

```php
<?php
$table = Builder::table('table-name', 'optional-table-name-alias');
```

### database

Get database connection driver instance (e.g, `MySqlDriver` or `PdoDriver`).

```php
public database(): Luminova\Instance\DatabaseInterface
```

**Return Value:**

`\Luminova\Interface\DatabaseInterface|null` - Return database driver instance.

**Throws:**

- [\Luminova\Exceptions\DatabaseException](/running/exceptions.md#databaseexception) -  Throws if database connection failed.

**Returning the Original Database Connection Object**

This method retrieves the underlying database connection instance, which can be either `PDO` or `MySqli`, encapsulated within the `Luminova\Interface\ConnInterface`.

```php
<?php 
$conn = $builder->database()->raw();
```

> This is useful when you need to access the raw connection for operations not covered by the interface.

***

### getInstance

To get a shared singleton instance of builder class.

```php
public static getInstance(): \Luminova\Database\Builder
```

**Return Value:**

`\Luminova\Database\Builder` - Return new static instance of builder class.

**Throws:**

- [\Luminova\Exceptions\DatabaseException](/running/exceptions.md#databaseexception) -  If the database connection fails.

***

### table

 Create instance of builder class and specifies the database table to build the query for. This method is essential in the `Builder` class and is typically called first to define the table you want to work with.

```php
public static table(string $table, ?string $alias = null): \Luminova\Database\Builder
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$table` | **string** | The table name to query (non-empty string). |
| `$alias` | **string&#124;null** | Optional table alias (default: NULL). |

**Return Value:**

`\Luminova\Database\Builder` - Returns the instance of builder class.

**Throws:**

- [\Luminova\Exceptions\InvalidArgumentException](/running/exceptions.md#invalidargumentexception) - Throws if an invalid table name is provided.

***

### join

To join main table `table` with another using `JOIN` operators (e.g `INNER JOIN`, `FULL JOIN`).

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

- [\Luminova\Exceptions\InvalidArgumentException](/running/exceptions.md#invalidargumentexception) - Throws if invalid argument is provided.

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
| `$table` | **string** | The table name to join. |
| `$alias` | **string&#124;null** | Optional table join alias (default: NULL). |

**Return Value:**

`self` - Returns the instance of the class.

**Throws:**

- [\Luminova\Exceptions\InvalidArgumentException](/running/exceptions.md#invalidargumentexception) - Throws if invalid argument is provided.

***

### leftJoin

Sets table join condition as `LEFT JOIN`.

```php
public leftJoin(string $table, string|null $alias = null): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$table` | **string** | The table name to join. |
| `$alias` | **string&#124;null** | Optional table join alias (default: NULL). |

**Return Value:**

`self` - Returns the instance of the class.

**Throws:**

- [\Luminova\Exceptions\InvalidArgumentException](/running/exceptions.md#invalidargumentexception) - Throws if invalid argument is provided.

***

### rightJoin

Sets table join condition as `RIGHT JOIN`.

```php
public rightJoin(string $table, string|null $alias = null): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$table` | **string** | The table name to join. |
| `$alias` | **string&#124;null** | Optional table join alias (default: NULL). |

**Return Value:**

`self` - Returns the instance of the class.

**Throws:**

- [\Luminova\Exceptions\InvalidArgumentException](/running/exceptions.md#invalidargumentexception) - Throws if invalid argument is provided.

***

### crossJoin

Sets table join condition as `CROSS JOIN`.

```php
public crossJoin(string $table, string|null $alias = null): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$table` | **string** | The table name to join. |
| `$alias` | **string&#124;null** | Optional table join alias (default: NULL). |

**Return Value:**

`self` - Returns the instance of the class.

**Throws:**

- [\Luminova\Exceptions\InvalidArgumentException](/running/exceptions.md#invalidargumentexception) - Throws if invalid argument is provided.

***

### fullJoin

Sets table join condition as `FULL JOIN`.

```php
public fullJoin(string $table, string|null $alias = null): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$table` | **string** | The table name to join. |
| `$alias` | **string&#124;null** | Optional table join alias (default: NULL). |

**Return Value:**

`self` - Returns the instance of the class.

**Throws:**

- [\Luminova\Exceptions\InvalidArgumentException](/running/exceptions.md#invalidargumentexception) - Throws if invalid argument is provided.

***

### fullOuterJoin

Sets table join condition as `FULL OUTER JOIN`.

```php
public fullOuterJoin(string $table, string|null $alias = null): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$table` | **string** | The table name to join. |
| `$alias` | **string&#124;null** | Optional table join alias (default: NULL). |

**Return Value:**

`self` - Returns the instance of the class.

**Throws:**

- [\Luminova\Exceptions\InvalidArgumentException](/running/exceptions.md#invalidargumentexception) - Throws if invalid argument is provided.

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
public in(string $column, array<int,mixed> $list): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$column` | **string** | The column name. |
| `$list` | **array<int,mixed>** | The expression values. |

**Return Value:**

`self` - Return instance of builder class.

**Throws:**

- [\Luminova\Exceptions\InvalidArgumentException](/running/exceptions.md#invalidargumentexception) - If values is not provided.
- [\Luminova\Exceptions\JsonException](/running/exceptions.md#jsonexception) - If an error occurs while encoding values.

***

### inset

Add a `FIND_IN_SET` condition to the query.

```php
public inset(string $search, string $operator, array<int,mixed>|string $list, bool $isSearchColumn = false): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$search` | **string** | The search value or column name depending on `$isSearchColumn`. |
| `$operator` | **string** | The operator for matching (e.g., `exists`, `first`, `&gt;= foo`, `&lt;= bar`). |
| `$list` | **array<int,mixed>&#124;string** | The comma-separated values or a column name containing the list. |
| `$isSearchColumn` | **bool** | Whether the `$search` argument is a column name (default: false). |

**Return Value:**

`self` - Return instance of builder class.

**Throws:**

- [\Luminova\Exceptions\InvalidArgumentException](/running/exceptions.md#invalidargumentexception) - Throws if list value is not empty.

**Default Operators:**

- `exists|>` - Check if exists or match any in the list.
- `first|=` - Check if it's the first in the list.
- `last` - Check if it's the first in the list.
- `position` - Position in the list (as inset_position).
- `contains` - Check if it contains the search term (uses the `$search` as the search value).
- `none` - No match in the list.

**Usage Examples:**

**Using the `custom` Operator:**

```php
Builder::table('fruits')->inset('banana', '= 2', ['apple','banana','orange']);
```

**Using the `exists` Operator with a column:**

```php
Builder::table('employees')->inset('PHP', 'exists', 'column_language_skills');
```

**Using the `exists` Operator with a search column:**

```php
Builder::table('employees')->inset('department', 'exists', 'HR,Finance,Marketing', true);
```

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

- [\Luminova\Exceptions\InvalidArgumentException](/running/exceptions.md#invalidargumentexception) - Throws if an invalid type is provided.

***

### datetime

Get date/time format for storing `SQL`.

```php
public static datetime(string $format = 'datetime', ?int $timestamp = null): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$format` | **string** | Format to return (default: `datetime`). |
| `$timestamp` | **int&#124;null** | An optional timestamp. |

**Return Value:**

`string` - Returns formatted date-time.

**Available time formats:**

- `time`  - Return time format from timestamp
- `datetime` - Return SQL datetime format
- `date`  - Return SQL date format.

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
| `$enable` | **bool** | The caching status action (e.g, `true` or `false`). |

**Return Value:**

`self` - Return instance of builder class.

> **Note:** By default caching is enabled once you call the `cache` method.

***

### cacheDelete

Deletes the cached data associated with current table or a specific database table.

This method clears cached data for a specified table and subfolder within the cache system.
It supports both `file-based` and `memory-based` caching (e.g., `Memcached`).
If no table is specified, it defaults to the table name associated with the current instance.

```php
public cacheDelete(?string $table = null, ?string $subfolder = null): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$table` | **string&#124;null** | The name of the table for which to delete the cache (default: null). |
| `$subfolder` | **string&#124;null** | Optional file-based caching feature, the subfolder name used while storing the cache if any (default: null). |

**Return Value:**

`bool` - Returns true if the cache was successfully cleared; otherwise, false.

***

### cacheDeleteAll

Deletes all cached items for the specified subfolder or the default database cache.

```php
public cacheDeleteAll(?string $subfolder = null): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$subfolder` | **string&#124;null** | Optional `file-based` caching feature, the subfolder name used while storing caches if any (default: null). |

**Return Value:**

`boo` - Returns true if the cache was successfully flushed, false otherwise.

***

### cache

Configures and manages caching for current query operation, to cache query result response, and return cached data when next user request the same content.

This method sets up the caching mechanism based on the provided parameters.
It supports both memory-based caching (e.g., `Memcached`) and file-based caching.
The method determines if the cache for the given key exists and whether it has expired.

```php
public cache(
	string $key, 
	?string $storage = null, 
	\DateTimeInterface|int $expiry = 7 * 24 * 60 * 60, 
	?string $subfolder = null, 
	?string $persistent_id = null
): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string** | The unique key identifying the cache item. |
| `$storage` | **string&#124;null** | Optional storage name for the cache (it's recommended to void storing large data in one file when using file-based cache).<br/>Defaults to the current table name or 'capture' if not specified. |
| `$expiry` | **\DateTimeInterface&#124;int** | The cache expiration time (default: to 7 days). |
| `$subfolder` | **string&#124;null** | Optional `file-based` caching feature, to set subfolder within the cache root directory (default: `database`). |
| `$persistent_id` | **string&#124;null** | Optional `memory-based` caching feature, to set a unique persistent connection ID (default: `__database_builder__`). |

**Return Value:**

`self` - Return instance of builder class.

> **Note:** In other for caching to work, you must call `cache` method before `select`, `find`, `execute`, `fetch`, `sum`, `average` or `total` method is called.
> Additionally, using `stmt` method for returning statement object doesn't support caching, you will need to manually implement it if need.

**Throws:**

- [\Luminova\Exceptions\CacheException](/running/exceptions.md#cacheexception) - Throws if an error occurs while creating cache or reading expired cache.

***

### insert

To insert records into a specified database table. 

This method allows for inserting multiple records at once by accepting an array of associative arrays or by using the `set` method to specify. For arrays, each associative array should contain the column names as keys and their corresponding values as values.

Additionally the insert method supports inserting array value to database, the array will be converted to `JSON` encoded string, if wish to insert a serialized string, you should do that before passing to insert

```php
public insert(array<string,mixed> $values, bool $prepare = true): int
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$values` | **array<int,array<string,mixed>>** | An array of associative arrays, where each associative array represents a record to be inserted into the table. |
| `$prepare` | **bool** | If set to true, uses prepared statements with bound values for the insert operation.<br />If false, executes a raw query instead (default: true). |

**Return Value:**

`int` - Returns the number of affected rows, 0 if no rows were inserted.

**Throws:**

- [\Luminova\Exceptions\DatabaseException](/running/exceptions.md#databaseexception) - Throws if an error occurs during the insert operation or if the provided values are not associative arrays.
- [\Luminova\Exceptions\JsonException](/running/exceptions.md#jsonexception) - Throws if an error occurs while encoding array values to JSON format.

***

### query

Build a custom SQL query string for execution when calling the `execute` method. This method allows you to run more custom queries as needed. 

The method supports caching, and when you pass an array of values directly to the `execute` method, it utilizes prepared statements for enhanced security. If no array values are provided, the method defaults to executing the query directly, so ensure that any values included in the query string are properly escaped to prevent SQL injection.

```php
public query(string $query): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$query` | **string** | The SQL query string |

**Return Value:**

`self` - Return instance of builder class.

**Throws:**

- [\Luminova\Exceptions\DatabaseException](/running/exceptions.md#databaseexception) - Throws if the query is empty string or an error occur.

**Example**

The below example show how you can use custom query builder to create and execute your SQL queries.

```php
<?php
$id = 1;
$customer_id = 'peter';

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

Executes an SQL query string that was previously prepared using the `query` method.

To enable caching of the query result, call the `cache` method before invoking `execute`.

You can optionally pass an associative array of placeholders (key-value pairs) to bind values to the query. The key serves as the placeholder name, while the value is the corresponding value to bind.

When placeholders are provided, prepared statements are used. If no placeholders are given, the method falls back to executing the query directly, so ensure any values included in the query are properly escaped.

```php
public execute(?array<string,mixed> $placeholder = null, int $mode = RETURN_ALL): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$placeholder` | **array<string,mixed>&#124;null** | The placeholder and value to binds with query string (default: `null`). |
| `$mode` | **int** | The query return result mode (e.g, `RETURN_*`) (default: `RETURN_ALL`). |

**Return Value:**

`mixed` - Return result or prepared statement depending on `$mode` otherwise false on failed.

**Return Modes**

- Refer to the [Luminova constants documentation](/global/constants.md) for details on database result return modes.

**Throws:**

- [\Luminova\Exceptions\DatabaseException](/running/exceptions.md#databaseexception) - If placeholder key is not a valid string or an error occur.

***

### total

Calculate the total number of records table,

```php
public total(string $column = '*'): int|false
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$column` | **string** | The column to index calculation (default: `*`). |

**Return Value:**

`int|false` - Return total number of records in table, otherwise false if execution failed.

**Throws:**

- [\Luminova\Exceptions\DatabaseException](/running/exceptions.md#databaseexception) - Throw if an error occurs.

***

### sum

Calculate the total sum of a numeric column in the table.

```php
public sum(string $column): int|float|false
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$column` | **string** | The column to calculate the sum. |

**Return Value:**

`int|float|false` - Return total sum columns, otherwise false if execution failed.

**Throws:**

- [\Luminova\Exceptions\DatabaseException](/running/exceptions.md#databaseexception) - Throw if an error occurs.

***

### average

Calculate the average value of a numeric column in the table.

```php
public average(string $column): int|float|false
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$column` | **string** | The column to calculate the average. |

**Return Value:**

`int|float|false` - Return total average of columns, otherwise false if execution failed.

**Throws:**

- [\Luminova\Exceptions\DatabaseException](/running/exceptions.md#databaseexception) - Throw if an error occurs.

***

### find

Select next or a single  record from database table.

```php
public find(array<int,string> $columns = ['*']): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$columns` | **array<int,string>** | An array of table columns to return (default: `[*]`). |

**Return Value:**

`mixed` - Return selected single row, otherwise false if execution failed.

**Throws:**

- [\Luminova\Exceptions\DatabaseException](/running/exceptions.md#databaseexception) - Throw if where method was not called or an error occurs.

***

### select

To select multiple records from table, call `select` method after you have prepared other conditional methods.

```php
public select(array<int,string> $columns = ['*']): mixed

```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$columns` | **array<int,string>** | An array of table columns to return (default: `[*]`). |

**Return Value:**

`mixed` - Return selected rows, otherwise false if execution failed.

**Throws:**

- [\Luminova\Exceptions\DatabaseException](/running/exceptions.md#databaseexception) - Throw if an error occurs.

***

### fetch

Fetch records from database table.
The `fetch` method allows you to retrieve records from table, by passing desired fetch `mode` and result type.

```php
public fetch(string $result = 'all', int $mode = FETCH_OBJ, array<int,string> $columns = ['*']): object|array|int|float|false|null
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$result` | **string** | The fetch result type to use, default `all` (e.g, `next` or `all`). |
| `$mode` | **int** | The fetch result mode (e.g, `FETCH_*`). |
| `$columns` | **array<int,string>** | An array of table columns to return (default: `[*]`). |

**Return Value:**

`object|array|int|float|false|null` - Return selected records, otherwise false if execution failed.

**Throws:**

- [\Luminova\Exceptions\DatabaseException](/running/exceptions.md#databaseexception) - If where method was not called.

***

### stmt

Returns the prepared query statement based on the conditions built using previous method calls.

> **Note:** To obtain a prepared statement object, do not call any of the following methods: `total`, `sum`, `find`, `select`, `average`, `fetch`, or `execute`. Calling any of these methods will result in `stmt` returning `NULL`.

```php
public stmt(array<int,string> $columns = ['*']): \Luminova\Interface\DatabaseInterface;
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$columns` | **array<int,string>** | An array of table columns to return (default: `[*]`). |

**Return Value:**

`\Luminova\Interface\DatabaseInterface` - Returns the prepared statement object if the query is successful; otherwise, it returns `NULL`.

**Throws:**

- [\Luminova\Exceptions\DatabaseException](/running/exceptions.md#databaseexception) - Throws if an error occurs.

**Example**

For more information about the methods available in the statement object, see the [Database Driver Documentation](/database/drivers.md).

```php
<?php
$tbl = Builder::table('users');
$tbl->where('programming_language', '=', 'PHP');
$stmt = $tbl->stmt();

// Use the statement object as needed 
var_export($stmt->getAll());
$stmt->free();
```

***

### update

Updates records in the database table with new data. You can pass an associative array of column-value pairs directly to the `update` method or use the `set` method to stage your columns and values for updating.

```php
public update(array<string,mixed> $setValues = []): int|bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$setValues` | **array<string,mixed>** | An optional associative array of columns and values to update. |

**Return Value:**

`int` - Returns the number of affected rows.

**Throws:**

- [\Luminova\Exceptions\DatabaseException](/running/exceptions.md#databaseexception) - Thrown if an error occurs while updating.
- [\Luminova\Exceptions\JsonException](/running/exceptions.md#jsonexception) - Thrown if an error occurs while encoding values.

> **Note:** If you pass an array value to an update field, it will be automatically converted to a `JSON` string. Ensure that your table field is designed to accept `JSON` strings before passing an array.

***

### delete

To delete record from database table.

```php
public delete(): int
```

**Return Value:**

`int` - Return number of affected rows.

**Throws:**

- [\Luminova\Exceptions\DatabaseException](/running/exceptions.md#databaseexception) - Throw if error occurs.

***

### transaction

Begins a transaction with optional read-only isolation level and `SAVEPOINT` for `PDO`.

```php
public transaction(int $flags = 0, ?string $name = null): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$flags` | **int** | Optional flags to set transaction properties (default: `0`).<br />For MySQLi: use `MYSQLI_TRANS_START_READ_ONLY` to set transaction as read-only.<br />For PDO: No predefined flags, specify `4` to create read-only isolation. |
| `$name` | **string&#124;null** | Optional transaction name to use.<br> If provided in `PDO`, `SAVEPOINT` will be created with name instead. |

**Return Value:**

`bool` - Returns true if the transaction was successfully started, otherwise false.

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

- [\Luminova\Exceptions\DatabaseException](/running/exceptions.md#databaseexception) - Throws if execution encountered error.

***

### temp

Creates a temporary table, it automatically copies all records from the main table into it.

```php
public temp(bool $transaction = true): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$transaction` | **bool** | Whether to use a transaction (default: true). |

**Return Value:**

`bool` - Returns true if the operation was successful; false otherwise.

**Throws:**

- [\Luminova\Exceptions\DatabaseException](/running/exceptions.md#databaseexception) - Thrown if a database error occurs during the operation.

**Example**

```php
<?php
if (Builder::table('users')->temp()) {
   $data = Builder::table('temp_users')->select();
}
```

> **Note:** 
> Temporary tables are automatically deleted when the current session ends. 
> You won't find these tables in `phpMyAdmin` or any other database manager as they are session-specific. 
> To query a temporary table, use the `temp_` prefix before the main table name.

***

### exec

Execute an SQL query string directly without bind nor prepared statement and return the number of affected rows.

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

- [\Luminova\Exceptions\DatabaseException](/running/exceptions.md#databaseexception) - Throws an exception if a database error occurs during the operation.
- [\Luminova\Exceptions\InvalidArgumentException](/running/exceptions.md#invalidargumentexception) - Throws if query string is empty.

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

- [\Luminova\Exceptions\DatabaseException](/running/exceptions.md#databaseexception) - Throws if error occurs.

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

- [\Luminova\Exceptions\DatabaseException](/running/exceptions.md#databaseexception) - Throws if error occurs.

***

### manager

Returns the shared instance of the database manager class, initialized with the current database connection. The manager object can be used to create database table backups or export entire tables in `CSV` or `JSON` format.

```php
public manager(): \Luminova\Database\Manager
```

**Return Value:**

`\Luminova\Database\Manager` - An instance of the database manager class.

**Throws:**

- [\Luminova\Exceptions\DatabaseException](/running/exceptions.md#databaseexception) - Throws if database connection failed.

**See Also:**

[Database Manager](/database/manager.md) - Detailed documentation on the database manager.

**

### export

Exports the current database table and prompts the browser to download it as a `JSON` or `CSV` file. This method utilizes the `manager` class for the export operation.

```php
public export(string $as = 'csv', ?string $filename = null, array $columns = ['*']): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$as` | **string** | The format to export the table in, either `csv` or `json`. |
| `$filename` | **string&#124;null** | The name of the file to download (optional). |
| `$columns` | **array** | The table columns to export (default: all columns). |

**Return Value:**

`bool` - Returns `true` if the export is successful, `false` otherwise.

**Throws:**

- [\Luminova\Exceptions\DatabaseException](/running/exceptions.md#databaseexception) - If an invalid format is specified or if the export operation fails.

***

### backup

Creates a backup of the current database table and saves the backup file in the `writeable/backups` directory. This method utilizes the `manager` class for the backup operation.

```php
public backup(?string $filename = null): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$filename` | **string&#124;null** | Optional name of the backup file (default: null). If not provided, table name and timestamp will be used. |

**Return Value:**

`bool` - Return true if backup is successful, false otherwise.

**Throws:**

- [\Luminova\Exceptions\DatabaseException](/running/exceptions.md#databaseexception) - If unable to create the backup directory or if failed to create the backup.

***

### errors

Retrieves the last query execution errors.

```php
public errors(): array
```

**Return Value:**

`array` - Return an array containing error information from the last executed query.

***

### debug

Enables query string debugging, allowing you to preview the compiled query string before execution.

```php
public debug(): self
```

**Return Value:**

`self` - Returns the current instance of the builder class.

> **Note:** In production environments, the query string will be logged at the `debug` level. When debugging is enabled, query execution will intentionally fail, and caching will be disabled.

***

### printDebug

Returns detailed debug information about the query string, including formats for `MySQL` and `PDO` placeholders, as well as the exact binding mappings for each column.

```php
public printDebug(): array<string,mixed>
```

**Return Value:**

`array<string,mixed>` - Return an array containing the debug information for the query string.

***

### dump

Outputs the debug information for the last executed query.

```php
public dump(): ?bool
```

**Return Value:**

`bool|null` - Returns `true` on success, `false` on failure, or `null` if no debug information is available.

***

### reset

Resets query conditions, parameters, and frees database resources, preparing the builder for the next query execution.

```php
public reset(): void
```

***

### free

Releases the statement cursor and sets the statement object to `null`.

```php
public free(): void
```

***

### close

Closes the database connection and releases the statement cursor.

```php
public close(): void
```