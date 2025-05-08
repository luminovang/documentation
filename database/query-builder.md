# Object-Relational Mapping (ORM) and Query Builder

***

## Overview

Database Builder Class: An Object-Relational Mapping Tool for Simplified CRUD Operations and Object-Oriented Database Management.

***

## Introduction

The Luminova database query `Builder` class is a powerful ORM (Object-Relational Mapping) tool designed to simplify database interactions. It enables developers to work with intuitive, object-oriented methods, abstracting the complexities of constructing SQL queries.

With its versatile CRUD (Create, Read, Update, Delete) capabilities, you can effortlessly build both simple and complex SQL queries without writing raw SQL. This allows you to focus more on developing your application and less on the intricacies of database queries. Each method in the `Builder` class is carefully crafted with names and functionality that align closely with SQL syntax, making it easy to grasp if you're familiar with SQL.

For more detailed examples and usage instructions, refer to the [Query Builder Usage Examples](/database/examples.md).

***

## Usage Examples

> **Note:** This class constructor is private preventing instantiation from outside e,g, `new Builder()` is not allowed. 

To initialize builder class you can either use the singleton methods `Builder::getInstance(...)`, `Builder::table(...)`, `Builder::query(...)` or `Builder::exec(...)` for custom SQL execution.

**Initialization:**

Create instance of Builder to inherit configuration across table method without specifying table name.

```php
$builder = Builder::getInstance()
	->strict(true) /* Force strict where clause check for `delete` and `update` */
	->caching(true) /* Enable caching */
	->closeAfter(true) /* Enable auto close connection after execution */
	->returns(Builder::RETURN_ARRAY); /* Set return format to array */
```

Now use the instance with inherited settings:

```php
$result = $builder->table('users')
	->where('id', '=', 100)
	->select(['name']);
```

***

Create instance of Builder with a table name.

```php
$table = Builder::table('table-name', 'optional-table-name-alias');
```

***

**Raw Expressions:**

To parse a raw SQL query to select column, insert or update value. Use the raw expression builder.

Custom raw expression method: 

```php
Builder::raw('COUNT(*) as total');
```

Raw expression class: 

```php
use Luminova\Database\RawExpression;

// Create a new instance of expression
new RawExpression('expression');

// Predefined raw expressions
RawExpression::increment('columnName'); // columnName = columnName + 1
RawExpression::decrement('columnName', 2);  // columnName = columnName - 2
RawExpression::now(); // NOW()
```

---

**Query Execution Order**  

Luminova Builder processes queries in a structured sequence:  

1. **Define the table**  
2. **Apply conditions and filters** (`where`, `join`, `cache`, `limit` etc.)  
3. **Specify the result executor method** (e.g, `select`, `find`, `sum`, `update`, etc.)  

For better readability and efficiency, always define conditions before executing result.  

**Incorrect:**  

```php
$result = Builder::table('users')
    ->select(['name', 'email'])
    ->where('id', '=', 1);
```

**Correct:**  
```php
$result = Builder::table('users')
    ->where('id', '=', 1)
    ->select(['name', 'email']);
```

**Why This Matters?**  

- **Improves Readability** - Conditions are applied before executing.  
- **Optimizes Execution** - The database filters rows first, reducing unnecessary processing.  
- **Follows SQL Logic** - Queries naturally apply conditions before fetching results.  
 

***

## Class Definition

* Class namespace: `Luminova\Database\Builder`
* This class implements: [\Luminova\Interface\LazyInterface](/interface/classes.md#lazyinterface),

***

## Methods

### database

Get database connection driver instance (e.g, `MySqlDriver` or `PdoDriver`).

```php
public static database(): Luminova\Instance\DatabaseInterface
```

**Return Value:**

`Luminova\Interface\DatabaseInterface|null` - Return database driver instance.

**Throws:**

- [\Luminova\Exceptions\DatabaseException](/running/exceptions.md#databaseexception) -  Throws if database connection failed.

**Returning the Original Database Connection Object**

This method retrieves the underlying database connection instance, which can be either `PDO` or `MySqli`, encapsulated within the `Luminova\Interface\ConnInterface`.

```php
$conn = Builder::database()->raw();
```

> This is useful when you need to access the raw connection for operations not covered by the interface.

***

### getInstance

Get or initialize the shared singleton instance of the Builder class.

This method also allows setting global configurations for query execution, ensuring consistency across multiple queries.

```php
public static getInstance(?string $table = null, ?string $alias = null): Luminova\Database\Builder
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$table` | **string** | The table name to query (non-empty string). |
| `$alias` | **string&#124;null** | Optional table alias (default: NULL). |

**Return Value:**

`Luminova\Database\Builder` - Returns the singleton instance of the Builder class.

**Throws:**

- [\Luminova\Exceptions\DatabaseException](/running/exceptions.md#databaseexception) -  If the database connection fails.

***

### getCache

Retrieves the current cache instance.

This method returns the static cache instance used by builder table method. The cache instance is typically set up earlier in the builders class's lifecycle.

```php
public static getCache(): ?Luminova\Base\BaseCache
```

**Return Value:**

`Luminova\Base\BaseCache|null` - Returns the current cache instance if set, or null if no cache has been initialized.

***

### getDebug

Get an array of debug query information.

Returns detailed debug information about the query, including formats for `MySQL` and `PDO` placeholders, as well as the exact binding mappings for each column.

```php
public getDebug(): array<string,mixed>
```

**Return Value:**

`array<string,mixed>` - Return array containing query information.

***

### table

Creates an instance of the builder class and sets the target database table.

This method is essential in the `Builder` class and is typically called first to define the table you want to work with.

```php
public static table(string $table, ?string $alias = null): Luminova\Database\Builder
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$table` | **string** | The name of the database table (must be a non-empty string). |
| `$alias` | **string&#124;null** | Optional alias for the table (default: `null`). |

**Return Value:**

`Luminova\Database\Builder` - Returns the instance of builder class.

**Throws:**

- [\Luminova\Exceptions\InvalidArgumentException](/running/exceptions.md#invalidargumentexception) - Throws if an invalid table name is provided.

**Example:** 

Performing a table join and executing queries:

```php
$tbl = Builder::table('users', 'u')
	->innerJoin('roles', 'r')
	->on('u.user_id', '=', 'r.role_user_id')
	->where('u.user_id', '=', 1);

// Updating records
$result = $tbl->update(['r.role_id' => 1]);
 
// Selecting records
$result = $tbl->select(['r.role_id', 'u.name']);
```

***

### query

Executes an SQL query with an optional placeholders.

This method allows direct execution of raw SQL queries with supports for caching result. If an array of values is passed to the `execute` method, prepared statements are used for security. Otherwise, ensure that manually embedded values in the query are properly escaped.

```php
public static query(string $query): Luminova\Database\Builder
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$query` | **string** | The SQL query string (must be non-empty). |

**Return Value:**

`Luminova\Database\Builder` - Return instance of builder class.

**Throws:**

- [\Luminova\Exceptions\DatabaseException](/running/exceptions.md#databaseexception) - Throws if the query is empty string or an error occur.

**Examples:**

Executing a raw query:

```php
$updated = Builder::query("
	UPDATE addresses 
	SET default_address = (address_id = :address_id) 
	WHERE address_user_id = :user_id
")->execute([
	'address_id' => 1,
	'user_id' => 101
], RETURN_COUNT);
```

Selecting records and cache results:

```php
$result = Builder::query("SELECT * FROM users WHERE user_id = :user_id")
	->cache()
	->execute(['user_id' => 101]);
```

***

### exec

Execute an SQL query string directly without bind nor prepared statement and return the number of affected rows.

For DDL statements like `CREATE`, `ALTER`, `DROP` etc..., the return value is typically of executed `1` rather than `0`, even though these statements do not affect rows directly.

```php
public static exec(string $query): int
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$query` | **string** | The `SQL` query string to execute. |

**Return Value:**

`int` - Return number affected rows or `0` if failed.

**Throws:**

- [\Luminova\Exceptions\DatabaseException](/running/exceptions.md#databaseexception) - Throws an exception if a database error occurs during the operation.
- [\Luminova\Exceptions\InvalidArgumentException](/running/exceptions.md#invalidargumentexception) - Throws if query string is empty.

***

### join

Specifies a table join operation in the query.

This method defines how another table should be joined to the current query.

**Join Types:**

- `INNER` - Returns only rows with matching values in both tables.
- `LEFT`  - Returns all rows from the left table and matching rows from the right table, filling in `NULL` for non-matching rows.
- `RIGHT` - Returns all rows from the right table and matching rows from the left table, filling in `NULL` for non-matching rows.
- `CROSS` - Returns the Cartesian product of the two tables.
- `FULL`  - Returns rows with matches in either table, filling in `NULL` for non-matching rows.
- `FULL OUTER` - Returns all rows from both tables, with `NULL` in places where there is no match.

```php
public join(string $table, string $type = 'INNER', ?string $alias = null): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$table` | **string** | The name of the table to join. |
| `$type` | **string** | The type of join to perform (default: `INNER`). |
| `$alias` | **string&#124;null** | Optional alias for the joined table (default: `null`). |

**Return Value:**

`self` - Returns the instance of builder class.

**Throws:**

- [\Luminova\Exceptions\InvalidArgumentException](/running/exceptions.md#invalidargumentexception) - Throws if either `$table` or `$type` is an empty string.

**Example:** 

Join table with an optional table name an alias:

```php
Builder::table('product', 'p')->join('users', 'LEFT', 'u');
```

**See Also:**

- `leftJoin()`
- `rightJoin()`
- `innerJoin()`
- `crossJoin()`
- `fullJoin()`

***

### on

Adds a join conditions to the table query.

This method defines a condition for joining tables, allowing comparisons between columns or values.

```php
public on(string $condition, string $comparison, mixed $value): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$condition` | **string** | The column name or condition to join on. |
| `$comparison` | **string** | The comparison operator (default: `=`). |
| `$value` | **mixed** | The value to compare or another table column.<br>- String literals must be wrapped in quotes.<br/>- Unquoted values are treated as column names. |

**Return Value:**

`self` - Returns the instance of builder class.

**Example:**

Using `on()` for table joins:

```php
Builder::table('users')
	->leftJoin('roles', 'r')
	->on('u.user_id', '=', 'r.role_user_id') /* Column comparison */
	->on('u.user_group', '=', 1)             /* Value comparison */
	->on('u.user_name', '=', '"peter"');     /* String literal (quoted) */
```

> **Note:** When using multiple joins in one query, always call `on()` immediately after each `join()`.

***

### innerJoin

Sets table join condition as `INNER JOIN`.

```php
public innerJoin(string $table, ?string $alias = null): self
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
public leftJoin(string $table, ?string $alias = null): self
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
public rightJoin(string $table, ?string $alias = null): self
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
public crossJoin(string $table, ?string $alias = null): self
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
public fullJoin(string $table, ?string $alias = null): self
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
public fullOuterJoin(string $table, ?string $alias = null): self
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

Sets the query limit for `SELECT` statements.

This method adds a `LIMIT` clause to the query, restricting the number of rows returned and optionally specifying an offset. Useful when working while calling methods like `total`, `select` `fetch`, `count`, `sum` and `average`.

```php
public limit(int $limit, int $offset = 0): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$limit` | **int** | The maximum number of results to return. Must be greater than 0. |
| `$offset` | **int** | The starting offset for the results (default: `0`). |

**Return Value:**

`self` - Returns the instance of the builder class.

**Example:**

Limiting number of results:

```php
Builder::table('users')
	->where('country', '=', 'NG')
	->limit(10, 5)
	->select();
```

This will generate: `LIMIT 5,10`

***

### max

Sets a maximum limit for `UPDATE` or `DELETE` operations.

This method applies a `LIMIT` clause to restrict the number of rows affected by `UPDATE` or `DELETE` queries.

```php
public max(int $limit): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$limit` | **int** | The maximum number of rows to update or delete. |

**Return Value:**

`self` - Return instance of builder class.

**Example:** 

Limiting number of rows to affect, ensuring the query affects at most 50 rows.

```php
$result = Builder::table('users')
	->where('country', '=','NG')
	->max(50)
	->update(['is_local' => 1]);
```

***

### strict

Enable or disable strict conditions for query execution.

When strict mode is enabled, certain operations (e.g., `delete`, `update`) may require a `WHERE` clause or logic operator to prevent accidental modifications of all records. This helps enforce safer query execution.

```php
public strict(bool $strict = true): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$strict` | **bool** | Whether to enable strict mode (default: `true`). |

**Return Value:**

`self` - Returns the instance of the builder class.

**Example:** 

Enabling strict mode:

If no `WHERE` condition is set, an exception will be thrown.

```php
$result = Builder::table('users')
    ->strict()
    ->delete(); 
 ```

Disabling strict mode:

The query will execute even if no `WHERE` condition is present.

```php
$result = Builder::table('users')
    ->strict(false)
    ->delete();
```

***

### order

Applies ascending or descending sorting order specified column for query results.

This method applies an `ORDER BY` clause to the query, allowing results to be sorted in ascending (`ASC`) or descending (`DESC`) order.

```php
public order(string $column, string $order = 'ASC'): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$column` | **string** | The name of the column to sort by. |
| `$order` | **string** | The sorting direction (default: `ASC`).<br/>(e.g, `ASC` for ascending, `DESC` for descending). |

**Return Value:**

`self` - Return instance of builder class.

**Example:**

Ordering results:

```php
$result = Builder::table('users')
	->order('created_at', 'DESC')
	->select();
```
This will generate: `ORDER BY created_at DESC`

### descending

Applies a descending order to the specified column in the result set.

```php
public descending(string $column): self
```

**Parameters:**

| Name | Type | Description |
|------|------|-------------|
| `$column` | `string` | The name of the column to sort by in descending order. |

**Return Value:**

`self` - Returns the instance of the class.

**Example:**

```php
$result = Builder::table('users')
    ->descending('created_at')
    ->select();
```

---

### ascending

Applies an ascending order to the specified column in the result set.

```php
public ascending(string $column): self
```

**Parameters:**

| Name | Type | Description |
|------|------|-------------|
| `$column` | `string` | The name of the column to sort by in ascending order. |

**Return Value:**

`self` - Returns the instance of the class.

**Example:**

```php
$result = Builder::table('users')
    ->ascending('username')
    ->select();
```

***

### orderAgainst

Set the result return order when match `against()` method is called.

**Predefined Modes**

- `NATURAL_LANGUAGE`
- `BOOLEAN`
- `NATURAL_LANGUAGE_WITH_QUERY_EXPANSION`
- `WITH_QUERY_EXPANSION `

```php
public orderAgainst(
	array $columns, 
	string|int|float $value, 
	string $mode = 'NATURAL_LANGUAGE', 
	string $order = 'ASC'
): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$columns` | **array** | The column names to index match order. |
| `$value` | **string&#124;int&#124;float** | The value to match against in order. |
| `$mode` | **string** | The comparison match mode operator (default: `NATURAL_LANGUAGE`).<br/> Optionally pass your own mode. |
| `$order` | **string** | The order algorithm to use (either `ASC` or `DESC`). |

**Return Value:**

`self` - Returns an instance of the class.

***

### group

Sets a `GROUP BY` clause for the query.

This method adds a column to the `GROUP BY` clause, allowing aggregation of results based on the specified column.

```php
public group(string $group): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$group` | **string** | The name of the column to group by. |

**Return Value:**

`self` - Return instance of builder class.

**Example:**

```php
Builder::table('users')
	->select()
	->group('country');
```
This will generate: `GROUP BY country`

***

### having

Add a filter `HAVING` expression to the query.

This method allows filtering grouped results using aggregate functions.
It appends a HAVING condition to the query, enabling advanced filtering after the `GROUP BY` clause.

```php
public having(
	Luminova\Database\RawExpression|string $expression, 
	string $comparison, 
	mixed $value, 
	string $operator = 'AND'
): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$expression` | **RawExpression&#124;string** | The column or expression to evaluate<br />(e.g, `Builder::raw('COUNT(columnName)'),`, `RawExpression::count('columnName')`). |
| `$comparison` | **string** | The comparison operator (e.g., `=`, `>`, `<=`, etc.). |
| `$value` | **mixed** | The value to compare against. |
| `$operator` | **string** | Logical operator to combine with other HAVING clauses (default: 'AND'). |

**Return Value:**

`self` - Return instance of builder class.

**Example** 

Filtering Using Having clause:

```php
Builder::table('orders')
    ->group('category')
    ->having('totalSales', '>', 1000)
    ->select(['category', 'SUM(sales) as totalSales'])
```
Generates: 

```sql
SELECT category, SUM(sales) as totalSales 
FROM orders
GROUP BY category
HAVING totalSales > 1000
```

Parsing Raw Expression:

```php
Builder::table('orders')
    ->group('category')
    ->having(RawExpression::sum('amount'), '>=', 1000)
    ->having(RawExpression::count('order_id'), '>', 10, 'OR')
	->select(['category']);
```

Generates: 

```sql
SELECT category
FROM orders
GROUP BY category
HAVING SUM(amount) >= 1000 OR OR COUNT(order_id) > 10
```

***

### where

Adds a `WHERE` condition to the query.

This method sets a conditional clause where the specified column must satisfy the given comparison operator and value.

> The `where` method can be called when working with methods like: `select`, `find`, `stmt`, `update`, `delete`, `sum`, `total` or `average`.

```php
public where(string $column, string $comparison, mixed $key): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$column` | **string** | The name of the column to filter by. |
| `$comparison` | **string** | The comparison operator (e.g., `=`, `>=`, `<>`, `LIKE`). |
| `$value` | **mixed** | The value to compare against. |

**Return Value:**

`self` - Return instance of builder class.

**Example:** 

Using the `WHERE` conditioning:

```php
Builder::table('users')
	->where('status', '=', 'active');
```

This will generate: `WHERE status = 'active'`

```php
Builder::table('users')
    ->where('status', 'IN', ['active', 'disabled']);
```

Generates: `WHERE status IN ('active', 'disabled')`

```php
Builder::table('users')
    ->where('status', 'NOT EXISTS', Builder::raw('(SELECT 1 FROM views WHERE id = 1)'));
```
Generates: `WHERE status NOT EXISTS (SELECT 1 FROM views WHERE id = 1)`

***

### clause

Add an advanced conditional clause to the query builder.

Supports a variety of query functions for building complex `WHERE` clauses.

**Supported Functions:**

- **REGULAR**: Default comparison as provided (e.g., `WHERE column = value`).
- **CONJOIN**: Combine multiple conditions (e.g., `WHERE (a = 1 OR b = 1)`).
- **NESTED**: Grouped conditions for deeply nested logic (e.g., `WHERE ((a = 1 OR b = 1) AND (c = 2))`).
- **AGAINST**: Full-text search using `MATCH ... AGAINST`.
- **FINDIN**: Use `IN (...)` for filtering against a list of values.

```php
public clause(
    string $clause, 
    string $column, 
    string $comparison, 
    mixed $value, 
    string $function = 'REGULAR'
): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$clause` | **string** | The logical connector (`AND`, `OR`, only). |
| `$column` | **string** | The column name to compare. |
| `$comparison` | **mixed** | The comparison operator (`=`, `<>`, `>=`, `LIKE`, etc.). |
| `$value` | **mixed** | The value to compare against. |
| `$function` | **mixed** | The clause function type (default: `REGULAR`). |

**Return Value:**

`self` - Return instance of builder class.

**Throws:**

- [\Luminova\Exceptions\InvalidArgumentException](/running/exceptions.md#invalidargumentexception) - If `FINDIN` is used with an empty array.

**Example Usage:**

```php
Builder::table('users')
    ->where('id', '=', 100)
    ->clause('OR', 'id', '=', 101)
    ->clause('AND', 'name', '=', 'Peter', 'REGULAR')
    ->clause('AND', 'roles', '=', ['admin', 'editor'], 'FINDIN');
```

This will generate: `WHERE id = 100 OR id = 101 AND name = 'Peter' AND roles IN ('admin', 'editor')`

***

### and

Adds an `AND` condition to the query.

This method appends an additional condition using the `AND` operator, requiring multiple conditions to be met.

```php
public and(string $column, string $comparison, mixed $value): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$column` | **string** | The name of the column to filter by. |
| `$comparison` | **string** | The comparison operator (e.g., `=`, `>=`, `<>`, `LIKE`). |
| `$value` | **mixed** | The value to compare against. |

**Return Value:**

`self` - Return instance of builder class.

**Example:** 
Using the `AND` conditioning:

```php
Builder::table('users')
	->where('status', '=', 'active')
    ->and('role', '=', 'admin');
```

This will generate: `WHERE status = 'active' AND role = 'admin'`

***

### or

Add a condition to the query using the `OR` operator.

This method appends a conditional clause where the specified column must satisfy the given comparison operator and value.

```php
public or(string $column, string $comparison, mixed $value): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$column` | **string** | The name of the column to apply the condition. |
| `$comparison` | **string** | The comparison operator to use (e.g., `=`, `>=`, `<>`, `LIKE`). |
| `$value` | **mixed** | The value to compare the column against. |

**Return Value:**

`self` - Return instance of builder class.

**Example:**

```php
Builder::table('users')
	->where('status', '=', 'active')
    ->or('role', '<>', 'admin');
```

This will generate: `WHERE status = 'active' OR role <> 'admin'`

***

### like

Add a `LIKE` clause to the query for pattern matching.

```php
public like(string $column, string $expression, string $clause = 'AND'): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$column` | **string** | The name of the column to apply the condition. |
| `$comparison` | **string** | The pattern to match using SQL `LIKE` (e.g. `%value%`). |
| `$clause` | **string** | The column clause condition (e.g, `OR` or `AND`). |

**Return Value:**

`self` - Return instance of builder class.

**Example:**

Using the `LIKE` conditioning:

```php
Builder::table('users')
    ->like('name', '%pet%')
    ->like('username', '%pet%', 'OR');
```
Generates: `WHERE name LIKE '%pet%' OR username LIKE '%pet%'`

***

### notLike

Add a `NOT LIKE` clause to the query to exclude pattern matches.

```php
public notLike(string $column, string $expression, string $clause = 'AND'): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$column` | **string** | The name of the column to apply the condition. |
| `$comparison` | **string** | The pattern to match using SQL `NOT LIKE` (e.g. `%value%`). |
| `$clause` | **string** | The column clause condition (e.g, `OR` or `AND`). |

**Return Value:**

`self` - Return instance of builder class.

**Example:**

Using the `NOT LIKE` conditioning:

```php
Builder::table('users')
    ->notLike('name', '%pet%');
```
Generates: `WHERE name NOT LIKE '%pet%'`

***

### against

Set query match columns and mode.

The `against` method allows you to perform database querying with the `match` operator for efficient matching against specified columns. It sets the query match columns and mode. It also support chaining multiple against conditions.

**Modes:**

You can use any of the following matching modes or pass your own mode:

- `NATURAL_LANGUAGE`: This mode provides a natural language search experience.
- `BOOLEAN`: This mode enables Boolean search capabilities.
- `NATURAL_LANGUAGE_WITH_QUERY_EXPANSION`: This mode extends the `NATURAL_LANGUAGE` mode with query expansion.
- `WITH_QUERY_EXPANSION`: This mode extends the standard search with query expansion.

```php
public against(array $columns, string $mode, mixed $value): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$columns` | **array** | The column names to match against. |
| `$mode` | **string** | The comparison match mode operator. |
| `$value` | **mixed** | The value to match against. |

**Return Value:**

`self` - Return instance of builder class.

**See Also:**

- `orderAgainst()`

***

### nullable

Adds a condition to filter results where the given column is `NULL` or `NOT NULL`.

This method appends a null match based on "$clause" condition to the query. It ensures that only records with a null or non-null value in the specified column are retrieved.

```php
public nullable(string $column, bool $isNull = true, string $clause = 'AND'): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$column` | **string** | The column name to check for non-null values. |
| `$isNull` | **bool** | Weather the the calumn should be null or not (default: true). |
| `$clause` | **string** | The column clause condition (e.g, `OR` or `AND`). |

**Return Value:**

`self` - Return instance of builder class.

**Example:**

```php
Builder::table('users')
	->where('country', '=', 'NG')
	->nullable('address')
	->select();
```

***

### isNotNull

Adds a condition to filter results where the given column is `NOT NULL`.

This method appends an `AND column IS NOT NULL` condition to the query.
It ensures that only records with a non-null value in the specified column are retrieved.

```php
public isNotNull(string $column, string $clause = 'AND'): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$column` | **string** | The column name to check for non-null values. |
| `$clause` | **string** | The column clause condition (e.g, `OR` or `AND`). |

**Return Value:**

`self` - Return instance of builder class.

**Example usage:**

```php
Builder::table('users')
	->where('country', '=', 'NG')
	->isNotNull('address')
	->select();
```

***

### isNull

Adds a condition to filter results where the given column is `NULL`.

This method appends an `AND column IS NULL` condition to the query.
It ensures that only records with a null value in the specified column are retrieved.

```php
public isNull(string $column, string $clause = 'AND'): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$column` | **string** | The column name to check for null values. |
| `$clause` | **string** | The column clause condition (e.g, `OR` or `AND`). |

**Return Value:**

`self` - Return instance of builder class.

**Example usage:**

```php
Builder::table('users')
	->where('country', '=', 'NG')
	->isNull('address')
	->select();
```

***

### set

Set the columns and values to be updated in the query.

This method should be invoked before the `update()` method to specify which columns to update.

```php
public set(string $column, mixed $value): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$column` | **string** | The name of the column to update. |
| `$value` | **mixed** | The value to set for the column. |

**Return Value:**

`self` - Return instance of builder class.

**Example:** 

Setting update columns and values:

```php
Builder::table('users')
	->set('status', 'active')
    ->set('updated_at', time())
	->update();
```

This will generate: `UPDATE table SET status = 'active', updated_at = 1699999999`

***

### column

Define a column condition for use in nested and conjoin queries.

This method simplifies the process of specifying a column condition with a comparison operator and a value.
It is particularly useful when used within methods like `conjoin()`, `nested()`, 'andConjoin()', `orConjoin()`
`orNested()` or `andNested()`.

```php
public static column(string $name, string $comparison, mixed $value): array<string,array>
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$name` | **string** | The name of the column. |
| `$comparison` | **string** | The comparison operator (e.g., `=`, `!=`, `<`, `>`, `LIKE`). |
| `$value` | **mixed** | The value to compare against. |

**Return Value:**

`array<string,array>` - Returns builder array column structure.

***

### bind

Binds a join named placeholder parameter to a value.

Use this method to manually assign a value to a named placeholder—typically used within a join condition where dynamic values are required.

```php
public bind(string $placeholder, mixed $value): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$placeholder` | **string** | The named placeholder. Must start with a colon `:` (e.g. `:id`). |
| `$value` | **mixed** | The value to bind to the placeholder. Arrays are JSON encoded. |

**Return Value:**

`self` - Return instance of builder class.

**Throws:**

- [\Luminova\Exceptions\InvalidArgumentException](/running/exceptions.md#invalidargumentexception) - If the placeholder does not start with a colon `:`.

**Example:**

```php
$result = Builder::table('users', 'u')
    ->innerJoin('orders', 'o')
        ->on('o.order_user_id', '=', 'u.user_id')
        ->on('o.order_id', '=', ':order_number')  /* Placeholder to bind */
        ->bind(':order_number', 13445)           /* Bind value */
    ->where('u.user_id', '=', 100)
    ->select();
```

> **Note:** This method is intended for join conditions only. Using it elsewhere may cause an exception.

***

### conjoin

Conjoin multiple conditions using either `AND` or `OR`.

This method creates a logical condition group where conditions are combined using the specified operator.

```php
public conjoin(
    array<int,array<string,array<string,mixed>>> $conditions, 
    string $operator = 'AND',
    string $clause = 'AND'
): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$conditions` | **array** | The conditions to group.<br />Or `Luminova\Database\column(...)` method for simplified builder. |
| `$operator` | **string** | The join logical operator (`AND` or `OR`) within each group (default: `AND`). |
| `$clause` | **string** | The column clause condition (e.g, `OR` or `AND`). |

**Return Value:**

`self` - Return instance of builder class.

**Throws:**

- [\Luminova\Exceptions\InvalidArgumentException](/running/exceptions.md#invalidargumentexception) - Throws if invalid group operator is specified.

**Examples:**

Group conditions:

```php
Builder::table('fooTable')->conjoin([
	['column1' => ['comparison' => '=', 'value' => 1]],
	['column2' => ['comparison' => '=', 'value' => 2]]
], 'OR');
```
**Using Column method:**

```php
Builder::table('fooTable')->conjoin([
    Builder::column('column1', '=', 1),
    Builder::column('column2', '=', 2)
], 'OR');
```
Generates: `... WHERE (column1 = 1 OR column2 = 2)`

***

### orConjoin

Groups multiple conditions using the `OR` operator.

This method uses `conjoin` method to creates a logical condition group where at least one condition must be met.

```php
public orConjoin(array<int,array<string,array<string,mixed>>> $conditions, string $clause = 'AND'): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$conditions` | **array** | The conditions to be grouped with `OR`. |
| `$clause` | **string** | The column clause condition (e.g, `OR` or `AND`). |

**Return Value:**

`self` - Return instance of builder class.

**Example:**

```php
Builder::table('fooTable')->orConjoin([
    Builder::column('column1', '=', 1),
	Builder::column('column2', '=', 2),
	/* ... */
]);
```
Generates: `... WHERE (column1 = 1 OR column2 = 2)`

***

### andConjoin

Groups multiple conditions using the `AND` operator.

This method uses `conjoin` method creates a logical condition group where all conditions must be met.

```php
public andConjoin(array<int,array<string,array<string,mixed>>> $conditions, string $clause = 'AND'): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$conditions` | **array** | The conditions to be grouped with `AND`. |
| `$clause` | **string** | The column clause condition (e.g, `OR` or `AND`). |

**Return Value:**

`self` - Return instance of builder class.

**Example:**

```php
Builder::table('fooTable')->andConjoin([
    Builder::column('column1', '=', 1),
    Builder::column('column2', '=', 2),
	/* ... */
]);
```
Generates: `WHERE (column1 = 1 AND column2 = 2)`

***

### nested

Creates a nested condition group by combining two condition sets.

This method groups two sets of conditions and binds them with the specified logical operator.

```php
public nested(
    array<int,array<string,array<string,mixed>>> $conditions1, 
    array<int,array<string,array<string,mixed>>> $conditions2, 
    string $operator = 'AND', 
    string $nestedOperator = 'AND',
    string $clause = 'AND'
): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$conditions1` | **array** | The first condition group.<br />Or `Luminova\Database\column(...)` method for simplified builder. |
| `$conditions2` | **array** | The second condition group. |
| `$operator` | **string** | The join logical operator (`AND` or `OR`) within each group (default: `AND`). |
| `$nestedOperator` | **string** | The nested logical operator (`AND` or `OR`) to bind the groups (default: `AND`). |
| `$clause` | **string** | The column clause condition (e.g, `OR` or `AND`). |

**Return Value:**

`self` - Return instance of builder class.

**Throws:**

- [\Luminova\Exceptions\InvalidArgumentException](/running/exceptions.md#invalidargumentexception) - Throws if invalid group operator is specified.

**Examples:**

Generating a nested  conditions:

```php
Builder::table('fooTable')
	->where('fooUser', '=', 100)
	->nested([
    	['foo' => ['comparison' => '=', 'value' => 1]],
    	['bar' => ['comparison' => '=', 'value' => 2]]
	],
	[
    	['baz' => ['comparison' => '=', 'value' => 3]],
		['bra' => ['comparison' => '=', 'value' => 4]]
	], 
	'OR', /* Group condition */
	'AND' /* Nested condition */
	);
```

**Using Column:**

```php
Builder::table('fooTable')
	->where('fooUser', '=', 100)
	->nested([
		Builder::column('column1', '=', 1),
		Builder::column('column2', '=', 2)
	],
	[
		Builder::column('column1', '=', 1),
		Builder::column('column2', '=', 2)
	], 
	'OR', /* Group condition */
	'AND' /* Nested condition */
	);
```

Generates: `... WHERE fooUser = 100 AND ((foo = 1 OR bar = 2) AND (baz = 3 OR bra = 4))`

***

### orNested

Binds two condition groups using the `OR` operator.

This method creates two logical condition groups and combines them using `OR`.

- `AND` - Groups are combined with AND (e.g., `WHERE ((a OR b) AND (c OR d))`).
- `OR`  - Groups are combined with OR (e.g., `WHERE ((a OR b) OR (c OR d))`).

```php
public orNested(
    array<int,array<string,array<string,mixed>>> $group1, 
    string $joinOperator, 
    array<int,array<string,array<string,mixed>>> $group2,
    string $clause = 'AND'
): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$group1` | **array** | The first condition group. |
| `$joinOperator` | **string** | The logical operator to bind both group (e.g, `AND`, `OR`). |
| `$group2` | **array** | The second condition group. |
| `$clause` | **string** | The column clause condition (e.g, `OR` or `AND`). |

**Return Value:**

`self` - Return instance of builder class.

**Example:**

Generating a query with nested `OR` conditions:

```php
Builder::table('fooTable')
	->orNested([
		Builder::column('foo', '=', 1),
		Builder::column('bar', '=', 2),
		/* ... */
	],
	'AND',
	[
		Builder::column('baz', '=', 3),
		Builder::column('bra', '=', 4),
		/* ... */
	]);
```
Generates: `... WHERE ((foo = 1 OR bar = 2) AND (baz = 3 OR bra = 4))`

***

### andNested

Binds two condition groups using the `AND` operator.

This method creates two logical condition groups and combines them using `AND`.

- `AND` - Groups are combined with AND (e.g., `WHERE ((a AND b) AND (c AND d))`).
- `OR`  - Groups are combined with OR (e.g., `WHERE ((a AND b) OR (c AND d))`).

```php
public andNested(
    array<int,array<string,array<string,mixed>>> $group1, 
    string $joinOperator, 
    array<int,array<string,array<string,mixed>>> $group2,
    string $clause = 'AND'
): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$group1` | **array** | The first condition group. |
| `$joinOperator` | **string** | The logical operator to bind both group (e.g, `AND`, `OR`). |
| `$group2` | **array** | The second condition group. |
| `$clause` | **string** | The column clause condition (e.g, `OR` or `AND`). |

**Return Value:**

`self` - Return instance of builder class.

**Example:**

Generating a query with nested `AND` conditions:

```php
Builder::table('fooTable')
	->andNested([
    	Builder::column('foo', '=', 1),
		Builder::column('bar', '=', 2),
		/* ... */
	],
	'OR',
	[
		Builder::column('baz', '=', 3),
		Builder::column('bra', '=', 4),
		/* ... */
	]);
```
Generates: `... WHERE ((foo = 1 AND bar = 2) OR (baz = 3 AND bra = 4))`

***

### in

Set query to search using `IN ()` expression.

This method allows you to set an array-value expressions to search for a given column name.

```php
public in(string $column, array<int,mixed> $list, string $clause = 'AND'): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$column` | **string** | The column name to search. |
| `$list` | **array<int,mixed>** | The array expression values. |
| `$clause` | **string** | The column clause condition (e.g, `OR` or `AND`). |

**Return Value:**

`self` - Return instance of builder class.

**Throws:**

- [\Luminova\Exceptions\InvalidArgumentException](/running/exceptions.md#invalidargumentexception) - If values is not provided.
- [\Luminova\Exceptions\JsonException](/running/exceptions.md#jsonexception) - If an error occurs while encoding values.

***

### notIn

Adds a NOT IN condition to the query.

This method creates a condition where the specified column's value is not in the provided list.

```php
public notIn(string $column, array $list, string $clause = 'AND'): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$column` | **string** | The name of the column to check against the list. |
| `$list` | **array** | An array of values to compare against the column. |
| `$clause` | **string** | The column clause condition (e.g, `OR` or `AND`). |

**Return Value:**

`self` - Return instance of builder class.

**Throws:**

- [\Luminova\Exceptions\InvalidArgumentException](/running/exceptions.md#invalidargumentexception) - If the provided list is empty.

***

### inset

Add a condition for `FIND_IN_SET` expression for the given column name.

**Default Operators:**

- `exists`, `>` - Check if exists or match any in the list.
- `first`, `=` - Check if it's the first in the list.
- `last` - Check if it's the first in the list.
- `position` - Position in the list (as inset_position).
- `contains` - Check if it contains the search term (uses the `$search` as the search value).
- `none` - No match in the list.

```php
public inset(
    string $search, 
    string $comparison, 
    array<int,mixed>|string $list, 
    bool $isSearchColumn = false,
    string $clause = 'AND'
): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$search` | **string** | The search value or column name depending on `$isSearchColumn`. |
| `$comparison` | **string** | The comparison operator for matching (e.g., `exists`, `first`, `>= foo`, `<= bar`). |
| `$list` | **array<int,mixed>&#124;string** | The comma-separated values or a column name containing the list. |
| `$isSearchColumn` | **bool** | Whether the `$search` argument is a column name (default: false). |
| `$clause` | **string** | The column clause condition (e.g, `OR` or `AND`). |

**Return Value:**

`self` - Returns the instance of the builder class.

**Throws:**

- [\Luminova\Exceptions\InvalidArgumentException](/running/exceptions.md#invalidargumentexception) - Throws if list value is not empty.

**Usage Examples:**

**Using the `custom` Operator:**

```php
Builder::table('fruits')
    ->inset('banana', '= 2', ['apple','banana','orange']);
```

**Using the `exists` Operator with a column:**

```php
Builder::table('employees')
	->inset('PHP', 'exists', 'columnName_language_skills');
```

**Using the `exists` Operator with a search column:**

```php
Builder::table('employees')
	->inset('department', 'exists', 'HR,Finance,Marketing', true);
```

***

### returns

Set result return type to an `object`, `array` or prepared `statement` object.

This method changes the default result return type from and `object` to either  `array` or `statement` object.

```php
public returns(string $type): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$type` | **string** | The result return type (e.g, `Builder::RETURN_OBJECT`, `Builder::RETURN_ARRAY` or `Builder::RETURN_STATEMENT`). |

**Return Value:**

`self` - Return instance of builder class.

**Throws:**

- [\Luminova\Exceptions\InvalidArgumentException](/running/exceptions.md#invalidargumentexception) - Throws if an invalid type is provided.

> **Note:** Call method before `fetch`, `find` `select` etc...

***

### raw

Creates a new raw SQL expression.

This method is used to pass raw SQL expressions that should not be escaped or quoted by the query builder. It is useful for performing operations like `COUNT(*)`, `NOW()`, or `scores + 1` directly in queries.

```php
public static raw(string $expression): Luminova\Database\RawExpression
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$expression` | **string** | The raw SQL expression. |

**Return Value:**

`Luminova\Database\RawExpression` - Return instance of RawExpression, representing the raw SQL string.

**Throws:**

- [\Luminova\Exceptions\InvalidArgumentException](/running/exceptions.md#invalidargumentexception) - If an empty string is passed.

**Examples:**

Using RawExpression in a `SELECT` Query

```php
use Luminova\Database\Builder;

$users = Builder::table('users')
	->group('id')
	->limit(5)
    ->select(['id', 'name', Builder::raw('COUNT(*) OVER() AS totalRecords')]);
```

Using RawExpression in an `UPDATE` Query

```php
use Luminova\Database\Builder;

Builder::table('posts')
    ->where('id', '=', 5)
    ->update([
        'views' => Builder::raw('views + 1'),
        'updated_at' => Builder::raw('NOW()')
    ]);
```

Using RawExpression in an `INSERT` Query

```php
use Luminova\Database\Builder;

Builder::table('logs')->insert([
    'message' => 'User login',
    'created_at' => Builder::raw('NOW()')
]);
```

***

### datetime

Helper method to get a formatted date/time string for `SQL` storage.

**Available formats:**

- `time`     → HH:MM:SS (e.g., `14:30:45`)
- `datetime` → YYYY-MM-DD HH:MM:SS (e.g., `2025-04-03 14:30:45`)
- `date`     → YYYY-MM-DD (e.g., `2025-04-03`)
- `unix`     → UNIX timestamp (e.g., `1712256645`)

```php
public static datetime(
	string $format = 'datetime', 
	\DateTimeZone|string|null $timezone = null, 
	?int $timestamp = null
): string
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$format` | **string** | The format to return (default: `datetime`). |
| `$timezone` | **DateTimeZone&#124;string&#124;null** |Optional timezone string or object (default: null). |
| `$timestamp` | **int&#124;null** | Optional UNIX timestamp to format; uses current time if null. |

**Return Value:**

`string` - Return formatted date/time or UNIX timestamp.

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

### closeAfter

Sets the auto-close connection status for the current query.

This method allows you to control whether the database connection should be automatically closed after executing the query.
By default, the connection remains open after query execution.

```php
public closeAfter(bool $close = true): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$close` | **bool** |  Whether to automatically close the connection after executing the query (default: `true`). |

**Return Value:**

`self` - Return instance of builder class.

***

### cacheDelete

Deletes the cached data associated with current table or a specific database table.

This method clears cached data for a specified table and subfolder within the cache system. It supports both `file-based` and `memory-based` caching (e.g., `Memcached`). If no table is specified, it defaults to the table name associated with the current instance.

```php
public cacheDelete(?string $storage = null, ?string $subfolder = null, ?string $persistentId = null): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$storage` | **string&#124;null** | Optional storage name for the cache. Defaults to the current table name or 'capture' if not specified. |
| `$subfolder` | **string&#124;null** | Optional file-based caching feature, the subfolder name used while storing the cache if any (default: null). |
| `$persistentId` | **string&#124;null** | Optional memory-based caching feature, to set a unique persistent connection ID (default: `__database_builder__`). |

**Return Value:**

`bool` - Returns true if the cache was successfully cleared; otherwise, false.

***

### cacheDeleteAll

Deletes all cached items for the specified subfolder or the default database cache.

```php
public cacheDeleteAll(?string $subfolder = null, ?string $persistentId = null): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$subfolder` | **string&#124;null** | Optional `file-based` caching feature, the subfolder name used while storing caches if any (default: null). |
| `$persistentId` | **string&#124;null** | Optional memory-based caching feature, to set a unique persistent connection ID (default: `__database_builder__`). |

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
	?string $persistentId = null
): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$key` | **string** | The unique key identifying the cache item. |
| `$storage` | **string&#124;null** | Optional storage name for the cache (it's recommended to void storing large data in one file when using file-based cache).<br/>Defaults to the current table name or 'capture' if not specified. |
| `$expiry` | **\DateTimeInterface&#124;int** | The cache expiration time (default: to 7 days). |
| `$subfolder` | **string&#124;null** | Optional `file-based` caching feature, to set subfolder within the cache root directory (default: `database`). |
| `$persistentId` | **string&#124;null** | Optional `memory-based` caching feature, to set a unique persistent connection ID (default: `__database_builder__`). |

**Return Value:**

`self` - Return instance of builder class.

> **Note:** In other for caching to work, you must call `cache` method before `select`, `find`, `execute`, `fetch`, `sum`, `average` or `total` method is called.
> Additionally, using `stmt` method for returning statement object doesn't support caching, you will need to manually implement it if need.

**Throws:**

- [\Luminova\Exceptions\CacheException](/running/exceptions.md#cacheexception) - Throws if an error occurs while creating cache or reading expired cache.

***

### insert

Insert records into a specified database table.

This method allows for inserting multiple records at once by accepting an array of associative arrays. Each associative array should contain the column names as keys and their corresponding values as values.

Optionally use the `set` method to to prepare inset values. 

> **Note:** If the insert value is an array, it will be converted to `JSON` encoded string, if wish to insert a serialized string, you should do that before passing the value.

```php
public insert(array<int,array<string,mixed>> $values, bool $prepare = true): int
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$values` | **array** | An array of associative arrays, where each associative array represents a record to be inserted into the table.. |
| `$prepare` | **bool** | If set to true, uses prepared statements with bound values for the insert operation.<br />If false, executes a raw query instead (default: true). |

**Return Value:**

`int` - Returns the number of affected rows, 0 if no rows were inserted.

**Throws:**

- [\Luminova\Exceptions\DatabaseException](/running/exceptions.md#databaseexception) - Throws if an error occurs during the insert operation or if the provided values are not associative arrays.
- [\Luminova\Exceptions\JsonException](/running/exceptions.md#jsonexception) - Throws if an error occurs while encoding array values to JSON format.

**Example:**

Using RawExpression in an INSERT Query.

```php
use Luminova\Database\RawExpression;

Builder::table('logs')->insert([
	[
		'message' => 'User login',
		'created_at' => Builder::raw('NOW()'),
		'updated_at' => RawExpression::now()
	],
	[
		'message' => 'User update',
		'created_at' => Builder::raw('NOW()'),
		'updated_at' => RawExpression::now()
	]
]);
```

***

### onDuplicate

Specifies an action to take when a duplicate key conflict occurs during an `INSERT` operation.

This method allows defining custom update operations when a record with a duplicate key is encountered.

**Supported operations:**

- `=` → Replace the value (e.g., `email = 'new@example.com'`).
- `+=` → Increment the existing value (e.g., `points = points + 10`).
- `-=` → Decrement the existing value (e.g., `points = points - 5`).

```php
public onDuplicate(string $column, string $operation, mixed $value): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$column` | **string** | The column to update on duplicate key. |
| `$operation` | **string** | The operation to apply (`=`, `+=`, `-=`). |
| `$value` | **mixed** | The new value or increment amount. |

**Return Value:**

`self` - Return instance of builder class.

**Example:**

```php
Builder::table('users')
	->onDuplicate('points', '=', 'VALUES(points)')
	->onDuplicate('points', '+=', 10) /* Increment points by 10 on duplicate key */
	->onDuplicate('email', '=', 'new@example.com') /* Update email on duplicate key */
	->insert([
		[
			'id' => 1, 
			'name' => 'Alice', 
			'points' => 50, 
			'email' => 'alice@example.com'
 		]
	]);
```

***

### ignoreDuplicate

Sets whether duplicates should be ignored during insertion.

This method allows you to control the behavior of handling duplicate keys.
When set to `true`, duplicate keys will be ignored. If set to `false`, the default behavior is to handle duplicates as normal (which may result in an error or update based on the context).

```php
public ignoreDuplicate(bool $ignore = true): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$ignore` | **bool** | Whether to ignore duplicates (default: `true`). |

**Return Value:**

`self` - Return instance of builder class.

**Example:**

Ignore duplicates during insertion:

```php
Builder::table('users')
	->ignoreDuplicate()
	->insert([
		[
			'id' => 1, 
			'name' => 'Alice', 
			'points' => 50, 
			'email' => 'alice@example.com'
		]
	]);
```

***

### replace 

Replaces records in the specified database table.

This method replaces existing records, it will first **deletes** existing rows with the same primary key or unique key before inserting new ones. 

> **Note:** This may lead to unintended data loss, especially if foreign key constraints exist.

```php
public replace(array<string,mixed> $values, bool $prepare = true): int
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$values` | **array<int,array<string,mixed>>** | An array of associative arrays, where each associative array represents a record to be replace in the table. |
| `$prepare` | **bool** | If `true`, executes the operation using prepared statements with bound values.<br/>If `false`, executes a raw SQL query instead (default: `true`). |

**Return Value:**

`int` - Returns the number of affected rows (each `REPLACE` may count as **two**: one delete + one insert).

**Throws:**

- [\Luminova\Exceptions\DatabaseException](/running/exceptions.md#databaseexception) - Throws if an error occurs during the operation or if the provided values are invalid.
- [\Luminova\Exceptions\JsonException](/running/exceptions.md#jsonexception) - Throws if an error occurs while encoding array values to JSON format.

> **Warning:** Since `replace` removes and re-inserts data, it can reset auto-increment values and trigger delete/insert events instead of update events.

***

### copy 

Prepares a copy selection query for copying data from the current table.

This method selects the specified columns from the current table context, allowing the data to be copied into another table using the `to()` method.

```php
public copy(array $columns): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$columns` | **array<int,string>** |The list of column names to be selected for copying. |

**Return Value:**

`self` - Return instance of builder class.

**Throws:**

- [\Luminova\Exceptions\DatabaseException](/running/exceptions.md#databaseexception) - Throws if an error occurs.

**Example** 

Prepare a copy of specific columns

```php
Builder::table('users')
	->where('id', '=', 1)
	->copy(['id', 'email', 'created_at'])
	->to('backup_users', ['id', 'email', 'created_at']); /* Destination */
```

***

### to 

Executes the selected columns from `copy` method and insert into a target table.

This method must be used after calling `copy()`, as it executes the previously prepared selection query and inserts the results into the specified target table.

```php
public to(string $targetTable, array $columns): int
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$targetTable` | **string** | The name of the target table where the copied data will be inserted. |
| `$columns` | **array<int,string>** | The list of target table column names that should receive the copied values. |

**Return Value:**

`int` - Return the number of affected rows or `0` if the operation fails.

**Throws:**

- [\Luminova\Exceptions\DatabaseException](/running/exceptions.md#databaseexception) - Throws if an error occurs.
- [\Luminova\Exceptions\InvalidArgumentException](/running/exceptions.md#invalidargumentexception) - Throws if empty table is specified.

***

### execute

Executes an SQL query that was previously set using the `query()` method.

**Caching**  

To cache the query result, call the `cache()` method before invoking `execute()`. If caching is enabled and a cached result exists, the method returns the cached value immediately.  

**Parameter Binding**

This method supports placeholder binding via an associative array. The key represents the placeholder name, and the value is the corresponding value to bind.  
- If placeholders are provided, the query is executed using prepared statements.  
- If no placeholders are given, the query is executed directly. Ensure that all values in the query are properly escaped to prevent SQL injection.  

**Available Return Modes:**

- `RETURN_ALL` → Returns all rows (default).
- `RETURN_NEXT` → Returns a single row or the next row from the result set.
- `RETURN_2D_NUM` → Returns a 2D array with numerical indices.
- `RETURN_ID` → Returns the last inserted ID.
- `RETURN_COUNT` → Returns the number of affected rows.
- `RETURN_COLUMN` → Returns columns from the result.
- `RETURN_INT` → Returns an integer count of records.
- `RETURN_STMT` → Returns a PDO or MySQLi prepared statement object.
- `RETURN_RESULT` → Returns a MySQLi result object or a PDO statement object.

```php
public execute(?array<string,mixed> $placeholder = null, int $mode = RETURN_ALL): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$placeholder` | **array<string,mixed>&#124;null** |  An associative array of placeholder values to bind to the query (default: `null`). |
| `$mode` | **int** | The result return mode e.g, `RETURN_*` (default: `RETURN_ALL`). |

**Return Value:**

`mixed|\PDOStatement|\mysqli_result|\mysqli_stmt` - Returns the query result, a prepared statement object, or `false` on failure.

- `PDOStatement` - If `env(database.connection)` is `PDO` and return mode is set to `RETURN_STMT` or `RETURN_RESULT`.
- `mysqli_stmt` - If `env(database.connection)` is `MYSQLI` and return mode is set to `RETURN_STMT`.
- `mysqli_result` - If `env(database.connection)` is `MYSQLI` and return mode is set to `RETURN_RESULT`.

**Return Modes**

- Refer to the [Luminova constants documentation](/global/constants.md) for details on database result return modes.

**Throws:**

- [\Luminova\Exceptions\DatabaseException](/running/exceptions.md#databaseexception) - If placeholder key is not a valid string or an error occur.

**Example:** 

Executing a prepared query:

```php
$stmt = Builder::query("SELECT * FROM users WHERE user_id = :user_id");

$result = $stmt->execute(['user_id' => 1]);

// Fetching a single row:
$user = $stmt->execute(['user_id' => 1], RETURN_NEXT);
```

***

### total

Calculate the total number of records table.

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

### exists

Determine if a database table exists.

```php
public exists(): bool
```

**Return Value:**

`bool` - Return true if table exists in database, otherwise false.

**Throws:**

- [\Luminova\Exceptions\DatabaseException](/running/exceptions.md#databaseexception) - Throw if an error occurs.

**Example:**

Check if user table exists in database:

```php
if(Builder::table('users')->exists()){
    echo 'Table exists in database';
}
```

***

### has

Determine of a records exists in table.

```php
public has(): bool
```

**Return Value:**

`bool` - Return true if records exists in table, otherwise false.

**Throws:**

- [\Luminova\Exceptions\DatabaseException](/running/exceptions.md#databaseexception) - Throw if an error occurs.

**Example:**

Check if users in country Nigeria exists in table:

```php
if(Builder::table('users')->where('country', '=', 'NG')->has()){
    echo 'Users in Nigeria exists';
}
```

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

Select next or a single record from database table.

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

Select single or multiple records from table.

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
public fetch(
	string $result = 'all', 
	int $mode = FETCH_OBJ, 
	array<int,string> $columns = ['*']
): mixed
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

Returns query prepared statement based on build up method conditions.

For more information about the methods available in the `DatabaseInterface` statement object, see the [Database Driver Documentation](/database/drivers.md).

```php
public stmt(array<int,string> $columns = ['*']): ?Luminova\Interface\DatabaseInterface;
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$columns` | **array<int,string>** | An optional array of columns to return (default: `[*]`).<br/>Column maybe required when called `stmt` with `select`, `find` or `fetch`. |

**Return Value:**

`Luminova\Interface\DatabaseInterface|null` - Returns the prepared statement object if the query is successful; otherwise, it returns `NULL`.

**Throws:**

- [\Luminova\Exceptions\DatabaseException](/running/exceptions.md#databaseexception) - Throws if an error occurs.

**Example**

Execute query in statement method:

```php
$stmt = Builder::table('users')
	->where('language', '=', 'PHP')
	->stmt(['name', 'email']);
```

Execute query before returning statement object:

```php
$tbl = Builder::table('users')
	->where('language', '=', 'PHP')
	->returns(Builder::RETURN_STATEMENT);

// Execute query
$tbl->find(['name', 'email']);

// Return statement object
$stmt = $tbl->stmt();
```

Use the statement object as needed 

```php
var_export($stmt->getAll());
$stmt->free();
```

***

### update

Update table with columns and values.

This method constructs and executes an `UPDATE` statement based on the current query conditions. 
It ensures that strict mode prevents execution without a `WHERE` clause, reducing the risk of accidental override.

```php
public update(?array<string,mixed> $setValues = null): int
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$setValues` | **array<string,mixed>\|null** | An optional associative array of columns and values to update if not already set defined using `set` method. |

**Return Value:**

`int` - Returns the number of affected rows.

**Throws:**

- [\Luminova\Exceptions\DatabaseException](/running/exceptions.md#databaseexception) - Thrown if an error occurs while updating.
- [\Luminova\Exceptions\JsonException](/running/exceptions.md#jsonexception) - Thrown if an error occurs while encoding values.

> **Note:** If you pass an array value to an update field, it will be automatically converted to a `JSON` string. Ensure that your table field is designed to accept `JSON` strings before passing an array.

**Example:**

Update table with columns and values:

```php
Builder::table('users')
	->where('id', '=', 1)
	->strict(true) /* Enable or disable strict where clause check */
	->update([
		'updated_at' => Builder::datetime(),
		'scores' => Builder::raw('scores + 1')
	]);
```

Update table preparing columns and values using `set` method:

```php
Builder::table('users')
	->where('id', '=', 1)
	->set('updated_at', Builder::datetime())
	->set('scores', Builder::raw('scores + 1'))
	->update();
```

Update table with join clause:

```php
Builder::table('users', 'u')
	->innerJoin('post', 'p')
	->on('u.user_id', '=', 'p.post_user_id')
	->where('u.user_id', '=', 1)
	->update([
		'u.last_update' => Builder::datetime(),
		'p.views' => Builder::raw('p.views + 1')
	]);
```

***

### delete

Delete records from the table.

This method constructs and executes a `DELETE` statement based on the current query conditions. 
It ensures that strict mode prevents execution without a `WHERE` clause, reducing the risk of accidental deletions.

```php
public delete(): int
```

**Return Value:**

`int` - Return number of affected rows.

**Throws:**

- [\Luminova\Exceptions\DatabaseException](/running/exceptions.md#databaseexception) - Throw if error occurs.

**Example:**

Delete table column:

 ```php
Builder::table('users')
	->where('id', '=', 1)
	->strict(true) /* Enable or disable strict where clause check */
	->delete();
```

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

**Example:**

```php
$tbl = Builder::table('users')
	->transaction()
	->where('country', '=', 'NG');

if($tbl->update(['suburb', 'Enugu'])){
	$tbl->commit();
}else{
	$tbl->rollback();
}

$tbl->free();
 ```

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
if (Builder::table('users')->temp()) {
   $data = Builder::table('temp_users')->select();
}
```

> **Note:** 
> Temporary tables are automatically deleted when the current session ends. 
> You won't find these tables in `phpMyAdmin` or any other database manager as they are session-specific. 
> To query a temporary table, use the `temp_` prefix before the main table name.

***

### drop

Drops a database table or a temporary table if it exists.

When `$isTemporalTable` is set to `true`, the temporal created table of the current table will be dropped instead.

```php
public drop(bool $transaction = false, bool $isTemporalTable = false): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$transaction` | **bool** | Whether to use a transaction (default: false). |
| `$isTemporalTable` | **bool** | Whether the table is a temporary table (default false). |

**Return Value:**

`bool` - Return true if table was successfully dropped, false otherwise.

**Throws:**

- [\Luminova\Exceptions\DatabaseException](/running/exceptions.md#databaseexception) - Throws if error occurs.

**Examples:**

This will drop the `users` table from the database.

```php
Builder::table('users')
    ->drop(); // Table 'users' was dropped
```

This will drop the `users` temporal table from the database.

```php
Builder::table('users')
    ->drop(false, true); // Table 'temp_users' was dropped
```

### lock

Handles database-level locking using advisory locks for PostgreSQL and MySQL.

- **PostgreSQL**: Uses `pg_advisory_lock` and `pg_advisory_unlock`, requiring an **integer** lock name.
- **MySQL**: Uses `GET_LOCK`, `RELEASE_LOCK`, and `IS_FREE_LOCK`, allowing **string** lock names.

```php
public static lock(string|int $identifier, int $timeout = 300): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$identifier` | **string&#124;int** | Lock identifier (must be an integer for PostgreSQL). |
| `$timeout` | **int** | Lock timeout in seconds (only applicable for MySQL). |

**Return Value:**

`bool` - Return true if the operation was successful, false otherwise.

**Throws:**

- [\Luminova\Exceptions\DatabaseException](/running/exceptions.md#databaseexception) - If an invalid action is provided or an invalid PostgreSQL lock name is used.

***

### unlock

Releases the lock for the given name.

```php
public static unlock(string|int $identifier): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$identifier` | **string&#124;int** | Lock identifier (must be an integer for PostgreSQL). |

**Return Value:**

`bool` - Return true if the lock was successfully released, false otherwise.

**Throws:**

- [\Luminova\Exceptions\DatabaseException](/running/exceptions.md#databaseexception) - If an invalid action is provided or an invalid PostgreSQL lock name is used.

***

### isLocked

Checks if the given lock is free.

```php
public static isLocked(string|int $identifier): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$identifier` | **string&#124;int** | Lock identifier (must be an integer for PostgreSQL). |

**Return Value:**

`bool` - Return true if the lock is free, false if it is currently held.

**Throws:**

- [\Luminova\Exceptions\DatabaseException](/running/exceptions.md#databaseexception) - If an invalid action is provided or an invalid PostgreSQL lock name is used.

***

### isConnected

Check if database connected.

```php
public static isConnected(): bool
```

**Return Value:**

`bool` - Return true if database connected, false otherwise.

***

### manager

Returns the shared instance of the database manager class, initialized with the current database connection. The manager object can be used to create database table backups or export entire tables in `CSV` or `JSON` format.

```php
public manager(): Luminova\Database\Manager
```

**Return Value:**

`Luminova\Database\Manager` - An instance of the database manager class.

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

Print the debug query information in the specified format.

This method prints a detailed debug information about the query, including formats for `MySQL` and `PDO` placeholders, as well as the binding mappings for each column.

If no format is provided or running is CLI/command mode, defaults to `print_r` without any formatting.

**Supported formats:**

- `null` → Print a readable array (default), 
- `html` → Format output in html `pre`. 
- `json` → Output as json-pretty print.

```php
public printDebug(?string $format = null): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$format` | **string&#124;null** | Output format (e.g, `html`, `json` or `NULL`). |

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

Reset query settings to default and Free database statement cursor if not in transaction and `returns` is not a statement object.

```php
public reset(): void
```

***

### free

Frees up the statement cursor and sets the statement object to `null`.

If in transaction, it will return false, you can free again when transaction is done (e.g, `committed` or `rollback`).

```php
public free(): bool
```

**Return Value:**

`bool` - Return true if successful, otherwise false.

***

### close

Close the current database connection.

```php
public close(): bool
```

**Return Value:**

`bool` - Return true if database connection is close, otherwise false. 
