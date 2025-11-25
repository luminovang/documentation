# Object-Relational Mapping (ORM) and Query Builder

***

## Overview

Database Builder Class: An Object-Relational Mapping Tool for Simplified CRUD Operations and Object-Oriented Database Management.

***

## Introduction

The Luminova database query `Builder` class is an ORM (Object-Relational Mapping) tool that simplifies working with databases. It provides object-oriented methods that construct SQL queries and binding. Features include support for table **Join**, **UNION** queries, **promise-style** resolution, and more.

With flexible CRUD (Create, Read, Update, Delete) capabilities, you can build both simple and advanced SQL queries with ease. This reduces the need to write raw SQL, letting you focus on your application logic instead of query syntax. Each method in the `Builder` class closely mirrors SQL conventions, so if you're familiar with SQL, you'll feel right at home.

For detailed examples and usage patterns, see the [Query Builder Usage Examples](/examples/database-integration.md).

---

## Usage Examples

> **Note:** This class uses a private constructor to prevent direct instantiation. You cannot call `new Builder()` directly.

To create a `Builder` instance, use one of the following static methods:

* `Builder::getInstance(...)`
* `Builder::table(...)`
* `Builder::query(...)`
* `Builder::exec(...)` (for custom SQL)

### Shared Initialization

Use `getInstance()` to create a base `Builder` instance that shares configuration across multiple table calls:

```php
$builder = Builder::getInstance()
    ->strict(true)          // Enforce strict WHERE checks on DELETE and UPDATE
    ->cacheable(true)       // Enable query caching for all table
    ->closeAfter(true)      // Automatically close DB connection after execution
    ->returns(Builder::RETURN_ARRAY); // Set return format to array
```

Now you can chain operations with inherited settings:

```php
$result = $builder->table('users')
    ->select(['name'])
    ->where('id', '=', 100);
```

---

### Single Initializers

Initialize a `Builder` instance with a specific table name:

```php
$table = Builder::table('table-name', 'optional-alias');
```

Run a raw SQL query with optional bindings:

```php
$result = Builder::query('SELECT * FROM users')->execute([...]);
```

Execute a raw SQL command and get the number of affected rows:

```php
$result = Builder::exec('ALTER TABLE users AUTO_INCREMENT = 2');
```

---

### Raw Expressions

The `RawExpression` class provide access to pass raw sql query to builder either to column, values or SQL query body. Use raw expressions when you need to insert or evaluate direct SQL values in methods like `select`, `insert`, or `update`.

#### Inline Expression

```php
$expr = Builder::raw('COUNT(*) AS total');
```

#### Expression Class

```php
use Luminova\Database\RawExpression;

// Create a new expression instance
$raw = new RawExpression('NOW()');

// Predefined helpers
RawExpression::increment('column');        // column = column + 1
RawExpression::decrement('column', 2);     // column = column - 2
RawExpression::now();                      // NOW()
// And more
```

---

### Closure Value 

Some builder methods (like `on()`, `where()` and more...) support closures as dynamic value sources.
A closure lets you build subqueries or compute values at runtime within the query builder context.

When you pass a closure, it receives **one argument** — a **static instance of the Builder** class.
This instance inherits your default global builder configuration (e.g., caching, auto-close connection, and return type), so it behaves exactly like your main builder.

**Signature:**

The closure must return either:

* A **scalar value** (string, int, float, etc.), or
* A **`Luminova\Database\RawExpression`** instance wrapping a raw SQL expression.

```php
(Closure(Builder $instance): mixed)
```

**Examples:**

Using closure inside `on()`.

```php
$builder->on('u.user_id', '=', function (Builder $b): string {
    $result = $b->table('foo')
        ->find(['id'])
        ->where('baz', '=', 'bra')
        ->get();

    // Optionally terminate if no result
    if (empty($result)) {
        throw new Exception('Record not found', ErrorCode::TERMINATED);
    }

    return $result->id;
});
```

Using closure inside `where()`.

```php
$builder->where('u.balance', '>', function (Builder $b): mixed {
    return $b->table('payments')
        ->find(['SUM(amount) as totalBalance'])
        ->where('status', '=', '"completed"')
        ->get()->totalBalance; // return numeric value
});
```

---

### Builder Execution Order

The `Builder` class processes methods in a defined order. This ensures clarity and consistency when building queries:

1. **Table Definition** – `table(...)`
2. **Selector Method** – `select`, `find`, `sum`, `average`, `total`, `copy`, etc.
3. **Conditions and Filters** – `where`, `join`, `in`, `like`, `limit`, etc.
4. **Execution Method** – `get`, `fetch`, `promise`, `stmt`, `to`, `has`, etc.

#### Example Structure:

```php
$result = Builder::table('users')          // Step 1: Define table
    ->select(['name', 'email'])           // Step 2: Select fields or use aggregate
    ->where('id', '=', 1)                 // Step 3: Add conditions
    ->limit(1)                            // Additional filters (limit, order, etc.)
    ->cache(true)                         // Optional settings
    ->get();                              // Step 4: Execute and fetch
```

> **Note:** The order of selector, condition, filter, or option methods doesn't affect the query. The only requirement is that a query **executor** method such as `get`, `fetch`, `update`, `insert`, `delete`, `promise`, `stmt`, `to`, or `has` must be called last to trigger execution.

---

### Union Tables

To build a union of multiple queries, **do not call** execution methods like `get()`, `fetch()`, `promise()`, or `stmt()` on any part of the chain **until** the entire union structure is defined. Calling them prematurely will execute the current query instead of merging it with others.

Use `union()` or `unionAll()` to combine multiple queries. Each union source can be either:

* a `Builder` instance
* a `Closure` returning a `Builder` instance or `null` (to skip that union source)

After combining all queries, you can apply filters, limits, or sorting to the union result before calling the final executor.

---

#### Basic Union (using closure)

```php
$result = Builder::table('users')
    ->select(['name', 'email'])
    ->where('id', '=', 1)
    ->union(function () {
        return Builder::table('contacts')
            ->select(['name', 'email'])
            ->where('id', '=', 1);
    })
    ->get();
```

---

#### Union with Predefined Instances

```php
$privates  = Builder::table('users')->where('visibility', '=', 'private');
$publics   = Builder::table('users')->where('visibility', '=', 'public');
$invisible = Builder::table('users')->where('visibility', '=', 'invisible');

$results = $privates
    ->union($publics)
    ->union($invisible)
    ->columns(['id', 'email'])
    ->descending('id')
    ->get();
```

---

#### Advanced Union with Chained Closures

```php
$result = Builder::table('users')
    ->select(['user_id AS id', 'user_email AS email'])
    ->where('user_visibility', '=', 'private')
    ->limit(3)
    ->union(function () {
        return Builder::table('users')
            ->select(['user_id AS id', 'user_email AS email'])
            ->where('user_visibility', '=', 'public')
            ->limit(3);
    })
    ->union(function () {
        return Builder::table('users')
            ->select(['user_id AS id', 'user_username AS email'])
            ->where('user_visibility', '=', 'invisible')
            ->limit(3);
    })
    ->columns(['id', 'email'])     // Optional: override final column structure
    ->descending('id')
    ->limit(9)
    ->returns(Builder::RETURN_ARRAY)
    ->get();
```

> **Note:** You can still apply additional `where`, `limit`, `order`, `cache`, and `conjoin()` filters to the final union result before execution.

***

## Class Definition

* Class namespace: `Luminova\Database\Builder`
* This class implements: [Luminova\Exceptions\LazyObjectInterface](/error-handlers/exceptions.md#lazyobjectinterface),

***

## Constants 

### Full-Text Match Modes

The constant are predefine modes for fulltext search when using `match`, `against` or `orderAgainst`.

| Constant                 | SQL Equivalent              | Description         |
| ------------------------ | --------------------------- | ------------------- |
| `MATCH_NATURAL`          | `IN NATURAL LANGUAGE MODE`  | Default mode; interprets the search string as natural human language. |
| `MATCH_BOOLEAN`          | `IN BOOLEAN MODE`           | Supports boolean operators like `+`, `-`, `>`, `*`, etc., for fine control. |
| `MATCH_NATURAL_EXPANDED` | `IN NATURAL LANGUAGE MODE WITH QUERY EXPANSION` | Uses natural language search with automatic query expansion. |
| `MATCH_EXPANSION`        | `WITH QUERY EXPANSION` | Performs search with query expansion only, no base mode specified. |

---

## Methods

### database

Get database connection driver instance (e.g, `MySqlDriver` or `Luminova\`).

```php
public static database(): Luminova\Instance\DatabaseInterface
```

**Return Value:**

`Luminova\Interface\DatabaseInterface|null` - Return database driver instance.

**Throws:**

- [Luminova\Exceptions\DatabaseException](/error-handlers/exceptions.md#databaseexception) -  Throws if database connection failed.

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

- [Luminova\Exceptions\DatabaseException](/error-handlers/exceptions.md#databaseexception) -  If the database connection fails.

***

### getCache

Retrieves the current cache instance.

This method returns the static cache instance used by builder table method. The cache instance is typically set up earlier in the builders class's lifecycle.

```php
public static getCache(): ?Luminova\Base\Cache
```

**Return Value:**

`Luminova\Base\Cache|null` - Returns the current cache instance if set, or null if no cache has been initialized.

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

- [Luminova\Exceptions\InvalidArgumentException](/error-handlers/exceptions.md#invalidargumentexception) - Throws if an invalid table name is provided.

**Example:** 

Performing a table join and executing queries:

```php
$tbl = Builder::table('users', 'u')
	->innerJoin('roles', 'r')
		->on('u.id', '=', 'r.role_uid')
	->where('u.id', '=', 1);

// Updating records
$result = $tbl->update(['r.role_id' => 1]);
 
// Selecting records
$result = $tbl->select(['r.role_id', 'u.name', 'u.user_foo AS foo'])->get();
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

`Luminova\Database\Builder` - Returns the current query builder instance.

**Throws:**

- [Luminova\Exceptions\DatabaseException](/error-handlers/exceptions.md#databaseexception) - Throws if the query is empty string or an error occur.

**Examples:**

Executing a raw query:

```php
$updated = Builder::query("
	UPDATE addresses 
	SET default_address = (address_id = :address_id) 
	WHERE address_uid = :user_id
")->execute([
	'address_id' => 1,
	'user_id' => 101
], RETURN_COUNT);
```

Selecting records and cache results:

```php
$result = Builder::query("SELECT * FROM users WHERE id = :user_id")
	->cache('cache-key')
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

- [Luminova\Exceptions\DatabaseException](/error-handlers/exceptions.md#databaseexception) - Throws an exception if a database error occurs during the operation.
- [Luminova\Exceptions\InvalidArgumentException](/error-handlers/exceptions.md#invalidargumentexception) - Throws if query string is empty.

***

### join

Adds a table join to the current query.
     
Use this method to combine data from another table or subquery into your main query.
You can specify the type of join (`INNER`, `LEFT`, etc.) and optionally assign an alias for the joined table.

```php
public join(string $table, ?string $alias = null, ?string $type = null, bool $forSubquery = false): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$table` | **string** | The name of the table to join. |
| `$alias` | **string&#124;null** | Optional alias for the joined table (default: `null`). |
| `$type` | **string&#124;null** | The type of join to use (`INNER`, `LEFT`, `RIGHT`, `FULL`, or `CROSS`). |
| `$forSubquery` | **bool** | Set to `true` if the joined source is a subquery instead of a normal table. |

**Return Value:**

`self` - Returns the instance of builder class.

**Throws:**

- [Luminova\Exceptions\InvalidArgumentException](/error-handlers/exceptions.md#invalidargumentexception) - If `$table` or `$type` is an empty string.

**Examples:** 

Basic join:
```php
Builder::table('products', 'p')
    ->join('users', 'u', 'LEFT');
```

Join without alias:

```php
Builder::table('users')
    ->join('orders', type: 'INNER');
```

Join using subquery:

```php
Builder::table('users', 'u')
    ->join('orders', 'o', 'INNER', true);
        ->onSubquery('(SELECT user_id, COUNT(*) AS total FROM orders GROUP BY user_id)');
```

**See Also:**

Supported Join Methods:

- `Builder::innerJoin()` - Use `INNER` when you only want matching rows from both tables.  
- `Builder::leftJoin()`  - Use `LEFT` when you want all rows from the left table, even if no match exists.  
- `Builder::rightJoin()` - Use `RIGHT` when you want all rows from the right table, even if no match exists.  
- `Builder::fullJoin()`  - Use `FULL` (or `FULL OUTER`) when you want all rows from both sides.  
- `Builder::crossJoin()` - Use `CROSS` when you want every combination of rows (Cartesian product).  
- `Builder::fullOuterJoin()` - Use `FULL OUTER JOIN` when you need **all rows from both tables**, including unmatched records from either side. *(Note: Not supported by MySQL or SQLite; use a `UNION` of `LEFT JOIN` and `RIGHT JOIN` instead.)*

***

### on

Adds a join condition to the current sub-table.

This method defines a complete `ON` clause expression for join operations.  
It's ideal for straightforward comparisons that don't require more advanced methods like `onCompound()`, `onCondition()` or `onSubquery()`.

```php
public on(string $condition, string $comparison, mixed $value, string $connector = 'AND'): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$condition` | **string** | The column name or condition to join on. |
| `$comparison` | **string** | The comparison operator (e.g. `=`, `<>`, `>`, `<`). |
| `$value` | **mixed** | The value or column to compare against.<br/>- Quoted strings are treated as literals.<br/>- Unquoted strings are treated as column names.<br/>- Named placeholders (e.g. `:role_name`) must be bound with `bind()`. |
| `$connector` | **string** | Logical operator to join with previous conditions (`AND` or `OR`). |

**Return Value:**

`self` - Returns the instance of builder class.

**Throws:**

- [Luminova\Exceptions\InvalidArgumentException](/error-handlers/exceptions.md#invalidargumentexception) - If invalid logical clause was provided.

**Examples:**

Simple join condition:

```php
Builder::table('users', 'u')
    ->leftJoin('roles', 'r')
        ->on('u.user_id', '=', 'r.role_user_id') // Column comparison
        ->on('u.user_group', '=', 1)             // Numeric value comparison
        ->on('u.user_name', '=', '"peter"')      // String literal
        ->on('r.role_name', '=', ':role_name')   // Placeholder binding
            ->bind(':role_name', 'foo')
    ->where('u.user_id', '=', 1);
```

Multiple joins:
```php
Builder::table('users', 'u')
    ->innerJoin('roles', 'r')
        ->on('u.user_id', '=', 'r.role_user_id')
    ->leftJoin('orders', 'o')
        ->on('u.user_id', '=', 'o.order_user_id')
    ->where('u.user_id', '=', 1);
```

Using a closure for a subquery condition:
```php
Builder::table('users', 'u')
    ->innerJoin('orders', 'o')
        ->on('u.user_id', '=', static function (Builder $b): string {
            $result = $b->table('payments')->find(['user_id'])
                ->where('status', '=', '"completed"')
                ->get();

             if(empty($result))
                throw new \Exception('User not found');

            return $result->user_id;
        })
    ->where('u.active', '=', 1);
```

> **Note:** 
> When chaining multiple joins, always call `on()` immediately after each `join()`.

***

### onCompound

Adds a compound join condition combining two column conditions with a connector.

This defines an ON condition that joins two column conditions (or value comparisons) linked by an operator like `AND`, `OR`, or any custom SQL operator.

Both columns should use the structure from `Builder::column()`.

```php
public onCompound(
    array<int,array<string,array>> $columns, 
    string $operator = 'OR', 
    string $connector = 'AND'
): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$columns` | **array** | The columns conditions (from `[Builder::column(...), ...]`). |
| `$operator` | **string** | The operator that connects both column conditions (e.g., `AND`, `OR`). |
| `$connector` | **string** | Logical operator to join with previous conditions (`AND` or `OR`). |

**Return Value:**

`self` - Returns the instance of builder class.

**Throws:**

- [Luminova\Exceptions\InvalidArgumentException](/error-handlers/exceptions.md#invalidargumentexception) - If empty array columns or invalid was provided.

**Example:**

Using `onCompound()` for table joins:

```php
Builder::table('users', 'u')
	->leftJoin('contacts', 'c')
	->onCompound([
		Builder::column('u.id' '=', 'c.contact_uid'), 
		Builder::column('u.user_group', '=', 2)
	], 'OR');
```

> **Note:** 
> When using multiple joins in one query, always call `onCompound()` immediately after each `join()`.

***

### onCondition

Adds a raw SQL condition to the current JOIN clause.
 
Use this method when you need full control over the SQL `ON` expression, for example, when building complex or non-standard join logic that can't be represented with the basic `on()` method.
     
**Subquery Replace Filters:**
- `{{tableName}}` — Replaced with the join table name.
- `{{tableAlias}}` — Replaced with the join table alias.

```php
public onCondition(Luminova\Database\RawExpression|string $sql, string $connector = 'AND'): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$sql` | **RawExpression\|string** | A raw SQL string or `RawExpression` instance to use in the join condition. |
| `$connector` | **string** | Logical operator to join with previous conditions (`AND` or `OR`). |

**Return Value:**

`self` - Returns the instance of builder class.

**Throws:**

- [Luminova\Exceptions\InvalidArgumentException](/error-handlers/exceptions.md#invalidargumentexception) - If empty array columns or invalid was provided.

**Examples:**

Complex join conditions:

```php
Builder::table('users', 'u')
    ->leftJoin('logs', 'l')
        ->onCondition('(u.id = 100 OR u.id = 200)')
        ->onCondition(Builder::raw('DATE(l.created_at) = CURDATE()'));
```

Using subquery:

```php
Builder::table('users', 'u')
    ->leftJoin('orders', forSubquery: true)
        ->onCondition('(
            SELECT order_id
            FROM {{tableName}}
            WHERE status = "active"
            AND amount > 500
        ) AS o');
```

> **Note:** 
> When using multiple joins in one query, always call `onCondition()` immediately after each `join()`.
>
> **When to Use `onCondition`:**
> - When you want to include advanced conditions (`OR`, functions, nested logic).
> - When joining on computed columns or database functions.
> - When your join needs to mix multiple logical clauses (e.g., `AND`, `OR`).

---

### onSubquery

Defines a subquery as the source for the current JOIN operation.
     
This method attaches a complete SQL subquery directly to the join, when join is marked `$forSubquery` as subquery join.
     
**Subquery Replace Filters:**

- `{{tableName}}` — Replaced with the join table name.
- `{{tableAlias}}` — Replaced with the join table alias.

```php
public onSubquery(Luminova\Database\RawExpression|string $sql): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$sql` | **RawExpression\|string** | A raw SQL string or `RawExpression` representing the subquery. |

**Return Value:**

`self` - Returns the instance of builder class.

**Examples:**

Join with subquery and outer conditions:

```php
Builder::table('users', 'u')
    ->leftJoin('logs', 'l', true)
        ->onSubquery('(
            SELECT name
            FROM {{tableName}}
            WHERE logger_user_id = 100
        )')
        ->on('l.foo', '=', 'bar') // Outer condition
        ->onCondition('(u.id = 100 OR u.id = 200)');
```

Manual alias assignment for subquery join:

```php
Builder::table('users', 'u')
    ->leftJoin('logs', forSubquery: true)
        ->onSubquery('(
            SELECT name
            FROM {{tableName}}
            WHERE logger_user_id = 100
        ) AS l');
```

> **Note:** 
> When using multiple joins in one query, always call `onSubquery()` immediately after each `join()`.
>
> **When to Use `onSubquery`:**
> - When the joined table is a subquery instead of a regular table.

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
$result = Builder::table('users')
	->select()
	->where('country', '=', 'NG')
	->limit(10, 5)
	->get();
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

`self` - Returns the current query builder instance.

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

`self` - Returns the current query builder instance.

**Example:**

```php
$result = Builder::table('users')
	->select()
	->group('country')
    ->get();
```
This will generate: `GROUP BY country`

***

### order

Applies ascending or descending sorting order or a raw SQL expression to query results.
     
This method adds an `ORDER BY` clause to the query. You can specify either a column name
or a raw SQL expression for advanced ordering logic (e.g., custom relevance scores).

```php
public order(string $column, string $order = 'ASC'): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$column` | **string** | The column name or raw SQL expression to order by. |
| `$order` | **string** | The sorting direction, `ASC` or `DESC` (default: `ASC`). |

**Return Value:**

`self` - Returns the current query builder instance.

**Example:**

Simple column ordering:
```php
Builder::table('users')
     ->order('created_at', 'DESC');
// Generates: ORDER BY created_at DESC
```

Complex SQL ordering:
```php
Builder::table('blog')
     ->order("CASE 
         WHEN LOWER(title) LIKE '%php framework%' THEN 3
         WHEN LOWER(description) LIKE '%php framework%' THEN 2
         WHEN LOWER(body) LIKE '%php framework%' THEN 1
         ELSE 0 END", 'DESC');
// Generates: ORDER BY CASE ... END DESC
```

---

### descending

Use when you want results from largest to smallest / newest to oldest (e.g., `10, 9, 8` …, `Z, Y, X` …, most recent dates first).

```php
$result = Builder::table('users')
    ->descending('created_at')
    ->select();
```

---

### ascending

Use when you want results from smallest to largest / oldest to newest (e.g., `1, 2, 3` …, `A, B, C` …, earliest dates first).

```php
$result = Builder::table('users')
    ->select()
    ->ascending('username')
    ->get();
```

---

### random

Apply random order to the result set.

Uses MySQL's `RAND()` function to return rows in a random order. Optionally accepts a seed for repeatable randomness.

```php
public random(?int $seed = null): self
```

**Parameters:**

| Name | Type | Description |
|------|------|-------------|
| `$seed` | `int\|null` | Optional seed for deterministic shuffling (default: null). |

**Return Value:**

`self` - Returns the current query builder instance.

**Example:**

```php
$result = Builder::table('users')
    ->select()
    ->random();
    ->get();
```

***

### orderAgainst

Set the ordering of full-text search results using `MATCH ... AGAINST`.

This method allows ranking and sorting results based on a full-text search score. Useful when prioritizing rows that better match the search term.

```php
public orderAgainst(
	string|int|float $value, 
	string|int $mode = self::MATCH_NATURAL, 
	string $order = 'ASC'
): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$value` | **string&#124;int&#124;float** | The value to order search against. |
| `$mode` | **string** | The match mode, can be a raw string or predefined constant (e.g, `Builder::MATCH_*`). |
| `$order` | **string** | The sort direction, either either `ASC` or `DESC` (default: `ASC`). |

**Return Value:**

`self` - Returns an instance of the class.

**Throws:**

- [Luminova\Exceptions\InvalidArgumentException](/error-handlers/exceptions.md#invalidargumentexception) - If the sort order is invalid.
- [Luminova\Exceptions\DatabaseException](/error-handlers/exceptions.md#databaseexception) - If no match columns have been defined via `match()`.

**See Also:**

- `match()` to define match columns.
- `against()` to return result using full search.

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

`self` - Returns the current query builder instance.

**Example** 

Filtering Using Having clause:

```php
$result = Builder::table('orders')
    ->select(['category', 'SUM(sales) AS totalSales'])
    ->group('category')
    ->having('totalSales', '>', 1000)
    ->get()
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
$result = Builder::table('orders')
	->select(['category'])
    ->group('category')
    ->having(RawExpression::sum('amount'), '>=', 1000)
    ->having(RawExpression::count('order_id'), '>', 10, 'OR')
	->get();
```

Generates: 

```sql
SELECT category
FROM orders
GROUP BY category
HAVING SUM(amount) >= 1000 OR COUNT(order_id) > 10
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
| `$comparison` | **string** | The comparison operator (e.g., `=`, `>=`, `<>`, `LIKE`, `IN`, `NOT`). |
| `$value` | **mixed** | The value to compare against. |

**Return Value:**

`self` - Returns the current query builder instance.

**Example:** 

Using the `WHERE` conditioning:

```php
Builder::table('users')
	->where('status', '=', 'active');
```

This will generate: `WHERE status = 'active'`

**Where In Array:**

Using where to check if column in array or not `IN` or `NOT`.

```php
Builder::table('users')
    ->where(
		'status', /* Column name*/
		'IN', /* Expression (e.g, IN or NOT)*/
		['active', 'disabled'] /* Array List */ 
	);
```

Generates: `WHERE status IN ('active', 'disabled')`

```php
Builder::table('users')
    ->where('status', 'NOT EXISTS', Builder::raw('(SELECT 1 FROM views WHERE id = 1)'));
```
Generates: `WHERE status NOT EXISTS (SELECT 1 FROM views WHERE id = 1)`

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

`self` - Returns the current query builder instance.

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

`self` - Returns the current query builder instance.

**Example:**

```php
Builder::table('users')
	->where('status', '=', 'active')
    ->or('role', '<>', 'admin');
```

This will generate: `WHERE status = 'active' OR role <> 'admin'`

***

### between

Add a `BETWEEN` condition to the query.

This method adds a SQL `BETWEEN` clause for comparing a column's value
within one or more numeric or date ranges. You can pass multiple pairs of
values to create multiple ranges automatically joined by `OR`.

The `BETWEEN` operator includes both boundary values.

```php
public between(string $column, array $values, string $connector = 'AND', bool $not = false): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$column` | **string** | The column name to apply the condition on. |
| `$values` | **array** | An array of range boundaries. Must contain an even number of values.<br/>Each pair (e.g., `[0, 100]`) represents a range. |
| `$connector` | **string** | Logical operator to join with previous conditions (`AND` or `OR`). |
| `$not` | **bool** | Set to `true` to use `NOT BETWEEN` instead of `BETWEEN`. |

**Return Value:**

`self` - Returns the current query builder instance.

**Throws:**

- [Luminova\Exceptions\DatabaseException](/error-handlers/exceptions.md#databaseexception) - If less than two values are provided, or an odd number of values is passed.

> **Note:**
> This method uses named placeholder parameter binding. 
> Passing SQL functions (like `NOW()` or `COUNT()`) as values will fail.  
> Use `whereClause()` instead if you need raw SQL conditions.

```php
// Single range (balance between 0 and 100)
Builder::table('transactions')
     ->select()
     ->where('status', 'active')
     ->between('balance', [0, 100])
     ->get();

// Produces:
// (balance BETWEEN :balance_btw_0_a AND :balance_btw_0_b)

// Multiple ranges (balance between 0–100 or 300–500)
$query->between('balance', [0, 100, 300, 500]);

// Produces:
// (balance BETWEEN :balance_btw_0_a AND :balance_btw_0_b
//  OR balance BETWEEN :balance_btw_2_a AND :balance_btw_2_b)

// Using NOT BETWEEN
$query->between('balance', [0, 100], 'AND', true);
```

---

### notBetween

A shortcut for calling `Builder::between()` with the `$not` flag set to `true`.
It returns rows where the column value is **outside** the specified range(s).

```php
// Single range (balance not between 0 and 100)
Builder::table('transactions')
     ->select()
     ->where('status', 'active')
     ->notBetween('balance', [0, 100])
     ->get();

// Produces:
// (balance NOT BETWEEN :balance_btw_0_a AND :balance_btw_0_b)

// Multiple NOT BETWEEN ranges
$query->notBetween('balance', [0, 100, 300, 500]);

// Produces:
// (balance NOT BETWEEN :balance_btw_0_a AND :balance_btw_0_b
//  OR balance NOT BETWEEN :balance_btw_2_a AND :balance_btw_2_b)
```

---

### like

Add a `LIKE` clause to the query for pattern matching.

```php
public like(string $column, string $expression, string $connector = 'AND'): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$column` | **string** | The name of the column to apply the condition. |
| `$comparison` | **string** | The pattern to match using SQL `LIKE` (e.g. `%value%`). |
| `$connector` | **string** | Logical operator to join with previous conditions (`AND` or `OR`). |

**Return Value:**

`self` - Returns the current query builder instance.

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

Using adds a `NOT LIKE` clause to the query to exclude pattern matching condition.

```php
Builder::table('users')
    ->notLike('name', '%pet%');
```

Generates: `WHERE name NOT LIKE '%pet%'`

***

### against

Adds a full-text search clause using `MATCH (...) AGAINST (...)`.

This method appends a full-text `AGAINST` clause to the query using the match columns defined via `match()`. It's typically used for searching textual content.

```php
public against(mixed $value, string|int $mode = self::MATCH_NATURAL): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$value` | **mixed** | The value to match against.<br/>Can be a string, number, or a Closure to defer value evaluation. |
| `$mode` | **string** | The match mode, can be a raw string or predefined constant (e.g, `Builder::MATCH_*`). |

**Return Value:**

`self` - Returns the current query builder instance.

**Throws:**

- [Luminova\Exceptions\DatabaseException](/error-handlers/exceptions.md#databaseexception) - If match columns are missing or invalid.

**See Also:**

- `match()` to define match columns.
- `orderAgainst()` to order using full search.

***

### nullable

Adds a condition to filter results where the given column is `NULL` or `NOT NULL`.

This method appends a null match based on "$connector" condition to the query. It ensures that only records with a null or non-null value in the specified column are retrieved.

```php
public nullable(string $column, bool $isNull = true, string $connector = 'AND'): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$column` | **string** | The column name to check for non-null values. |
| `$isNull` | **bool** | Whether the the column should be null or not (default: true). |
| `$connector` | **string** | Logical operator to join with previous conditions (`AND` or `OR`). |

**Return Value:**

`self` - Returns the current query builder instance.

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
Builder::table('users')
	->where('country', '=', 'NG')
	->isNotNull('address', 'AND')
	->select();
```

***

### isNull

Adds a condition to filter results where the given column is `NULL`.

This method appends an `AND column IS NULL` condition to the query.
It ensures that only records with a null value in the specified column are retrieved.

**Example usage:**

```php
Builder::table('users')
	->where('country', '=', 'NG')
	->isNull('address')
	->select();
```

***

### whereClause

Add a raw SQL fragment to the WHERE clause.

Accepts a string or a RawExpression object. This is useful when you need to insert custom SQL that can't be built using structured conditions.

```php
public whereClause(Luminova\Database\RawExpression|string $sql): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$sql` | **RawExpression\|string** | Raw SQL fragment to append to WHERE clause. |

**Return Value:**

`self` - Returns the current query builder instance.

> **Notes:**
> - Use this method only when you're sure the input is safe.
> - You must include the proper logical operator (e.g. AND, OR) in the raw SQL yourself.

**Example:**

```php
$result = Builder::table('users')
    ->select()
    ->whereClause('AND status <> "archived"')
    ->whereClause(new RawExpression('OR deleted_at IS NULL'))
    ->get();
```

***

### clause

Adds a complex conditional clause directly to the current table query builder.

This method allows flexible `WHERE` logic by supporting multiple condition types. It can be used to directly append conditions while manually constructing queries, bypassing higher-level helpers like `where()` or `match()`.

**Supported Modes:**

- `Builder::REGULAR`  — Standard comparison (e.g., `WHERE column = value`)
- `Builder::CONJOIN`  — Combined expressions (e.g., `WHERE (a = 1 OR b = 2)`)
- `Builder::NESTED`   — Deeply grouped conditions (e.g., `WHERE ((a = 1 AND b = 2) OR (c = 3))`)
- `Builder::AGAINST`  — Full-text match using `MATCH (...) AGAINST (...)`
- `Builder::INARRAY`  — Filters using `IN (...)` or `NOT IN (...)` with array values

```php
public clause(
	string $connector,
	string $column,
	string $comparison,
	mixed $value,
	?string $mode = null
): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$connector` | **string** | Logical operator to join with previous conditions (`AND` or `OR`). |
| `$column` | **string** | The column to apply the condition on. |
| `$comparison` | **mixed** | The comparison operator (`=`, `<>`, `>=`, `LIKE`, etc.). |
| `$value` | **mixed** | The condition value to compare. Can be scalar or array (for `Builder::INARRAY`). |
| `$mode` | **string\|null** |  The clause mode. One of the supported modes (default: `Builder::REGULAR`). |

**Return Value:**

`self` - Returns the current query builder instance.

**Throws:**

- [Luminova\Exceptions\InvalidArgumentException](/error-handlers/exceptions.md#invalidargumentexception) - If an unsupported mode is given or if `Builder::INARRAY` is used with an empty array.

**Example Usage:**

```php
$builder = Builder::table('users')
    ->select()
    ->clause('AND', 'id', '=', 100)
    ->clause('OR', 'id', '=', 101)
    ->clause('AND', 'name', '=', 'Peter')
    ->clause('AND', 'roles', 'IN', ['admin', 'editor'], Builder::INARRAY)
	->get();
```

This will generate: `SELECT * FROM users WHERE id = 100 OR id = 101 AND name = 'Peter' AND roles IN ('admin', 'editor')`

> **Internal:**  
> Used internally by the builder to compose query conditions.
> Can also be called directly to manually define clauses without relying on higher-level methods like `where()`, `or()`, or `against()`.
> Useful when you want full control and to skip additional processing.

***

### condition

Adds a conditional clause to the query builder using scalar or array values.

Supports both regular WHERE conditions and array-based `IN`/`NOT IN` clauses.

> **Note:**
> - When `$value` is an array, it is transformed into an `IN` or `NOT IN` clause depending on `$comparison`.
>   - `'IN'` → `WHERE column IN (...)`
>   - `'NOT'` → `WHERE column NOT IN (...)`

```php
public condition(string $connector, string $column, string $comparison, mixed $value): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$connector` | **string** | Logical operator to join with previous conditions (`AND` or `OR`). |
| `$column` | **string** | The column to apply the condition on. |
| `$comparison` | **mixed** | The comparison operator (`=`, `<>`, `>`, `LIKE`, `IN`, `NOT`, etc.). |
| `$value` | **mixed** | The value to compare against.<br/>Array for `IN`/`NOT IN` queries. |

**Return Value:**

`self` - Returns the current query builder instance.

**Throws:**

- [Luminova\Exceptions\InvalidArgumentException](/error-handlers/exceptions.md#invalidargumentexception) - IIf an error occurs.

**Example Usage:**

```php
$builder = Builder::table('users')
    ->select()
    ->condition('AND', 'id', '=', 100)
    ->condition('OR', 'id', '=', 101)
    ->condition('AND', 'name', '=', 'Peter')
    ->condition('AND', 'roles', 'IN', ['admin', 'editor'])
    ->get();
```

***

### set

Add a column and its value to use in an `UPDATE` or `INSERT` query.

You can call this method multiple times to set several columns before
running `update()` or `insert()`.  

- When used before `update()`, it decides which columns and values will be updated.  
- When used before `insert()`, it builds the values that will be inserted.

```php
public set(string $column, mixed $value, ?int $index = null): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$column` | **string** | The name of the column to set. |
| `$value` | **mixed** | The value to assign (can be a simple value, a closure, or an expression). |
| `$index` | **int\|null** | Optional. Use this when inserting multiple rows at once. |

**Return Value:**

`self` - Returns the current query builder instance.

**Throws:**

- [Luminova\Exceptions\InvalidArgumentException](/error-handlers/exceptions.md#invalidargumentexception) - If you try to mix indexed and non-indexed set calls.

> **Note:**
> If you use an index (e.g., `$index = 0`), it assumes you're inserting multiple rows.
> You can’t mix indexed and non-indexed calls in the same query.

**Examples:** 

Update example:
```php
Builder::table('users')
    ->where('id', 1)
    ->set('status', 'active')
    ->set('updated_at', Builder::datetime())
    ->update();

// Result:
// UPDATE users SET status = 'active', updated_at = '2024-04-03 14:30:45' WHERE id = 1
```

Insert example:
```php
Builder::table('users')
    ->set('name', 'Peter')
    ->set('age', 30)
    ->insert();

// Result:
// INSERT INTO users (name, age) VALUES ('Peter', 30)
```

***

### column

Define a column condition for use in nested and conjoin queries.

This method simplifies the process of specifying a column condition with a comparison operator and a value.
It is particularly useful when used within methods like `conjoin()`, `nested()`, `andConjoin()`, `orConjoin()`
`orNested()` or `andNested()`.

```php
public static column(string $name, string $comparison, mixed $value): array<string,array>
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$name` | **string** | The name of the column. |
| `$comparison` | **string** | The comparison operator (e.g., `=`, `!=`, `<`, `>`, `LIKE`). |
| `$value` | **mixed\|Closure** | The value to compare against. |

**Return Value:**

`array<string,array>` - Returns an array representing a column structure.

***

### bind

Bind a named placeholder parameter to a value.

This method allows you manually assign values to SQL placeholders (`:param`) used anywhere in the query — including joins, clauses, or even raw column expressions.

```php
public bind(string $placeholder, mixed $value): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$placeholder` | **string** | The named placeholder. Must start with a colon `:` (e.g. `:id`). |
| `$value` | **mixed** | The value to bind to the placeholder. Arrays are JSON encoded. |

**Return Value:**

`self` - Returns the current query builder instance.

**Throws:**

- [Luminova\Exceptions\InvalidArgumentException](/error-handlers/exceptions.md#invalidargumentexception) - If the placeholder does not start with a colon `:`.

**Example:**

Binding inside a JOIN condition:

```php
 $result = Builder::table('users', 'u')
    ->select()
    ->innerJoin('orders', 'o')
        ->on('o.order_user_id', '=', 'u.user_id')
        ->on('o.order_id', '=', ':oid') /* Placeholder to bind */
            ->bind(':oid', 13445)       /* Bind value */
    ->where('u.user_id', '=', 100)
    ->get();
```

Binding inside a SELECT column expression:

```php
$result = Builder::table('users', 'u')
    ->select([
        'u.*', 
        'ST_Distance_Sphere(
            u.location,
            ST_SRID(POINT(:lng, :lat), 4326)
        ) / 1000 AS distance'
    ])
    ->where('u.status', '=', 'active')
    ->having('distance', '<=', 10)
    ->bind(':lat', 1.3521)
    ->bind(':lng', 103.8198)
    ->get();
```

***

### conjoin

Conjoin multiple conditions using either `AND` or `OR`.

This method creates a logical condition group where conditions are combined using the specified operator.

```php
public conjoin(
    array<int,array<string,array<string,mixed>>> $conditions, 
    string $groupConnector = 'AND',
    string $connector = 'AND'
): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$conditions` | **array** | The conditions to group.<br />Or `Builder::column(...)` method for simplified builder. |
| `$groupConnector` | **string** | The `AND` or `OR` logical connector within group (default: `AND`). |
| `$connector` | **string** | Logical operator to join with previous conditions (`AND` or `OR`). |

**Example:**

Using conjoin and in array search.

```php
$followings = [1001, 1002, 1003];

$result = Builder::table('posts')
	->where('country', '=', 'NG')
	->conjoin([
		Builder::column('is_trending', '=', 1),
		Builder::column('author_id', 'IN', $followings)
	], 'OR', 'AND')
	->get();
```

**Return Value:**

`self` - Returns the current query builder instance.

**Throws:**

- [Luminova\Exceptions\InvalidArgumentException](/error-handlers/exceptions.md#invalidargumentexception) - Throws if invalid group operator is specified.

**Examples:**

Group conditions:

```php
Builder::table('fooTable')
	->conjoin([
		['column1' => ['comparison' => '=', 'value' => 1]],
		['column2' => ['comparison' => '=', 'value' => 2]]
	], 'OR');
```
**Using Column method:**

```php
Builder::table('fooTable')
	->conjoin([
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
Builder::table('fooTable')
	->orConjoin([
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
Builder::table('fooTable')
	->andConjoin([
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
Use `Builder::column()` for simplified column builder.

```php
public nested(
    array<int,array<string,array<string,mixed>>> $firstGroup, 
    array<int,array<string,array<string,mixed>>> $secondGroup, 
    string $groupConnector = 'AND', 
    string $nestedConnector = 'AND',
    string $connector = 'AND'
): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$firstGroup` | **array** | An array of first group conditions. |
| `$secondGroup` | **array** |An array of second group conditions. |
| `$groupConnector` | **string** | The `AND` or `OR` logical connector within each group (default: `AND`). |
| `$nestedConnector` | **string** | The `AND` or `OR` logical connector to bind groups (default: `AND`). |
| `$connector` | **string** | Logical operator to join with previous conditions (`AND` or `OR`). |

**Return Value:**

`self` - Returns the current query builder instance.

**Throws:**

- [Luminova\Exceptions\InvalidArgumentException](/error-handlers/exceptions.md#invalidargumentexception) - Throws if invalid group operator is specified.

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

Adds an `IN` condition to the query using the `IN (...)` SQL expression.

Use this method to find rows where the given column's value matches any value in a provided list.

```php
public in(string $column, \Closure|array<int,string|int|float> $values, string $connector = 'AND'): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$column` | **string** | The column name to match against. |
| `$values` | **Closure\|array** | A list of values or a Closure returning an array of values. |
| `$connector` | **string** | Logical operator to join with previous conditions (`AND` or `OR`). |

**Return Value:**

`self` - Returns the current query builder instance.

**Throws:**

- [Luminova\Exceptions\InvalidArgumentException](/error-handlers/exceptions.md#invalidargumentexception) - If the provided values are empty or invalid.
- [Luminova\Exceptions\JsonException](/error-handlers/exceptions.md#jsonexception) - If an error occurs while encoding the values.

**Example:**

```php
Builder::table('languages')
    ->select()
    ->in('tag', ['php', 'sql'])
    ->get();

// Generates: `IN ('php', 'sql')`
```

---

### notIn

Adds a `NOT IN` condition to the query using the `NOT IN (...)` SQL expression.
     
Use this method to find rows where the given column's value does **not** match any value in a provided list.
     

```php
Builder::table('users')
    ->select()
    ->where('country', '=', 'NG')
    ->notIn('state', ['Enugu', 'Lagos', 'Abuja'])
    ->get();

// Generates: `NOT IN ('Enugu', 'Lagos', 'Abuja')`
```

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
    string $connector = 'AND'
): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$search` | **string** | The search value or column name depending on `$isSearchColumn`. |
| `$comparison` | **string** | The comparison operator for matching (e.g., `exists`, `first`, `>= foo`, `<= bar`). |
| `$list` | **array<int,mixed>&#124;string** | The comma-separated values or a column name containing the list. |
| `$isSearchColumn` | **bool** | Whether the `$search` argument is a column name (default: false). |
| `$connector` | **string** | Logical operator to join with previous conditions (`AND` or `OR`). |

**Return Value:**

`self` - Returns the instance of the builder class.

**Throws:**

- [Luminova\Exceptions\InvalidArgumentException](/error-handlers/exceptions.md#invalidargumentexception) - Throws if list value is not empty.

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

`self` - Returns the current query builder instance.

**Throws:**

- [Luminova\Exceptions\InvalidArgumentException](/error-handlers/exceptions.md#invalidargumentexception) - Throws if an invalid type is provided.

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

- [Luminova\Exceptions\InvalidArgumentException](/error-handlers/exceptions.md#invalidargumentexception) - If an empty string is passed.

**Examples:**

Using RawExpression in a `SELECT` Query

```php
use Luminova\Database\Builder;

$users = Builder::table('users')
    ->select(['id', 'name', Builder::raw('COUNT(*) OVER() AS totalRecords')])
    //->find(['id', 'name', Builder::raw('COUNT(*) OVER() AS totalRecords')])
	->group('id')
	->limit(5);
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
	DateTimeZone|string|null $timezone = null, 
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

### escape

Escapes a value for safe use in SQL queries.

This method handles various types and formats them appropriately:

- `null`, `bool`, and numeric values are cast based on the `$strict` flag.
- Arrays and objects are encoded as JSON. Existing valid JSON strings are preserved.
- `RawExpression` instances are returned as-is, unescaped.
- `Resource`  are read using `stream_get_contents` and returned string contents.
- Strings can optionally be escaped with `addslashes()` and/or wrapped in quotes.

**$strict**

- If true:
 - `null` returns `null` instead of `'NULL'`
 - `bool` returns `true|false` instead of `1|0`
 - `resource` returns `content` instead of `base64` encoded
 - Empty arrays return `[]` instead of `'[]'`

- If false:
 - `null` returns `'NULL'` (as string)
 - `bool` returns `1|0`
 - `resource` returns `base64` encoded contents.
 - Empty arrays return `'[]'`

**$numericCheck**

- Enables `+0` cast and `JSON_NUMERIC_CHECK` for JSON encoding.
- If false, numeric strings are preserved as-is.

```php
public static escape(
	mixed $value, 
	bool $enQuote = false, 
	bool $strict = false,
	bool $numericCheck = false,
	bool $addSlashes = false
): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$value` | **mixed** | The value to escape. |
| `$enQuote` | **bool** | If true, wraps the value in single quotes (except for JSON). |
| `$strict` | **bool** | Whether to use strict type casting (default: false). |
| `$numericCheck` | **bool** | If true, numeric strings are cast to `int/float` (default: false). |
| `$addSlashes` | **bool** | If true, string values are passed through `addslashes()`. |

**Return Value:**

`string|float|bool|int|null` - Returns a properly escaped and type-safe value.

**Throws:**

- `\JsonException` - If JSON encoding fails for arrays or objects.
- `Luminova\Exception\DatabaseException` - If value is resource and failed to read content.

***

### cacheable

Globally enable or disabled all caching for subsequent select operations.

This method provides the flexibility to enable or disable all caches universally. This is particularly useful for debugging or temporarily disabling database caching throughout your application without the need to manually remove `cache()` method calls from your all code. 

```php
public cacheable(bool $enable): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$enable` | **bool** | The caching status action (e.g, `true` or `false`). |

**Return Value:**

`self` - Returns the current query builder instance.

> **Note:** By default caching is enabled once you call the `cache` method.

***

### distinct

Enable or disable flag distinct selection for query executions.

```php
public distinct(bool $distinct = true): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$distinct` | **bool** | Whether to apply distinct selection (default: `true`). |

**Return Value:**

`self` - Returns the current query builder instance.

***

### replace 

Enable or disable replace feature when inserting or copying records in the current database table.

**Applicable To:**

- `insert()` - Before calling insert() method.
- `copy()`  - Before to() method.

If enabled, `insert` method will replaces existing records, by first **deleting** existing rows with the same primary key or unique key before inserting new ones. 

> **Note:** This may lead to unintended data loss, especially if foreign key constraints exist.

```php
public replace(bool $useReplace = true): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$useReplace` | **bool** |Set to true to use `REPLACE` instead of `INSERT` (default: true). |

**Return Value:**

`self` - Returns the current query builder instance.

> **Warning:** Since `replace` removes and re-inserts data, it can reset auto-increment values and trigger delete/insert events instead of update events.

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

`self` - Returns the current query builder instance.

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

`self` - Returns the current query builder instance.

> **Note:** In other for caching to work, you must call `cache` method before `select`, `find`, `execute`, `fetch`, `sum`, `average` or `total` method is called.
> Additionally, using `stmt` method for returning statement object doesn't support caching, you will need to manually implement it if need.

**Throws:**

- [Luminova\Exceptions\CacheException](/error-handlers/exceptions.md#cacheexception) - Throws if an error occurs while creating cache or reading expired cache.

***

### insert

Insert one or many records into a database table.

This method accepts either:
- A single associative array (column => value).
- An array of multiple associative arrays (to insert many rows at once).

By default, it uses prepared statements for safety and performance.
You can also run a raw query if needed by setting `$usePrepare` to false.

Optionally use the `set()` method to to prepare inset values. 

> **Note:** 
> If the insert value is an array, it will be converted to `JSON` encoded string. 
> If wish to insert a serialized string, you should do that before passing the value.

```php
public insert(
    ?array<int,array<string,mixed>> $values = null, 
    bool $usePrepare = true, 
    bool $escapeValues = true
): int
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$values` | **array\|null** | The records to insert or build using (`set()` method).<br/> Each record must be an associative array where:<br/>- Keys are column names<br/>- Values are the values to insert. |
| `$usePrepare` | **bool** | Whether to use prepared statements (default: true). |
| `$escapeValues` | **bool** | Whether to escape values if `$usePrepare` is true (default: true). |

**Return Value:**

`int` - Returns the number of rows inserted.

**Throws:**

- [Luminova\Exceptions\DatabaseException](/error-handlers/exceptions.md#databaseexception) - If the data format is invalid (e.g., not associative arrays).
- [Luminova\Exceptions\JsonException](/error-handlers/exceptions.md#jsonexception) - If array values cannot be encoded to JSON.

**Examples:**

Insert a single row:

```php
Builder::table('logs')->insert([
    'message' => 'User login',
    'created_at' => Builder::raw('NOW()')
]);
```

Insert multiple rows:

```php
Builder::table('users')->insert([
    ['name' => 'Alice', 'age' => 28],
    ['name' => 'Bob', 'age' => 34],
    /* More rows... */
]);
```

Insert inside a transaction:

```php
$tbl = Builder::table('users')->transaction();

$inserted = $tbl->insert([
    ['name' => 'Charlie', 'age' => 40],
    ['name' => 'Diana', 'age' => 36]
]);

if ($inserted) {
    $tbl->commit();
} else {
    $tbl->rollback();
}
```

Use REPLACE instead of INSERT:

```php
Builder::table('logs')
    ->replace(true)
    ->insert([
        'id' => 1, // if row with same PK exists, it will be replaced
        'message' => 'System reboot',
        'created_at' => Builder::raw('NOW()')
    ]);
```

Insert with ON DUPLICATE KEY UPDATE:

```php
Builder::table('users')
    ->onDuplicate('last_login', '=', Builder::raw('NOW()'))
    ->insert([
        'user_id' => 1001,
        'name' => 'John Doe',
        'last_login' => Builder::raw('NOW()')
    ]);
```

Insert while ignoring duplicate key errors:

```php
Builder::table('users')
    ->ignoreDuplicate(true)
    ->insert([
        'user_id' => 1002,
        'name' => 'Jane Doe'
    ]);
```

***

### update

Update one or more records in the database table.

This builds and executes an `UPDATE` query with the provided values.
It will respect any `WHERE` conditions, joins, and limits you’ve applied
with the query builder before calling this method.

**Safety:**
- If strict mode is enabled (`strict(true)`), an update without `WHERE`
  conditions will throw an exception (to prevent accidental full-table updates).
- Values must be passed as an associative array (`column => value`).

```php
public update(?array<string,mixed> $values = null): int
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$values` | **array<string,mixed>\|null** | The array of columns and values to update, or build using (`set()` method). |

**Return Value:**

`int` - Return the number of rows affected.

**Throws:**

- [Luminova\Exceptions\DatabaseException](/error-handlers/exceptions.md#databaseexception) - If no values are provided, or if input is not an associative array. 
- [Luminova\Exceptions\JsonException](/error-handlers/exceptions.md#jsonexception) - If JSON encoding fails when binding values.

> **Note:** 
> If you pass an array value to an update field, it will be automatically converted to a `JSON` string. Ensure that your table field is designed to accept `JSON` strings before passing an array.

**Examples:**

Update specific row:
```php
Builder::table('users')
    ->where('id', '=', 1)
    ->update([
        'last_login' => Builder::datetime(),
        'attempts' => 0
    ]);
```

Update rows using set method:
```php
Builder::table('users')
    ->where('id', '=', 1)
    ->set('last_login', Builder::datetime())
    ->set('attempts', 0)
    ->update();
```

Update with raw expression:
```php
Builder::table('users')
    ->where('id', '=', 1)
    ->update([
        'score' => Builder::raw('score + 5')
    ]);
```

Update with joins and strict mode:
```php
Builder::table('orders', 'o')
    ->innerJoin('users', 'u')
         ->on('u.id', '=', 'o.user_id')
    ->where('u.status', '=', 'inactive')
    ->strict(true) /* prevents missing WHERE clause accidents */
    ->update(['o.cancelled' => 1]);
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

`self` - Returns the current query builder instance.

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

`self` - Returns the current query builder instance.

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

### copy 

Prepares a selection query to copy data from the current table.

This method selects the specified columns in preparation for copying them into another table using the `to()` method to execute copy operation.

```php
public copy(array $columns = ['*']): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$columns` | **array<int,string>** | List of columns to select for copying (defaults: `['*']`). |

**Return Value:**

`self` - Returns the current query builder instance.

**Example:** 

Prepare a copy of specific columns

```php
use Luminova\Database\RawExpression;

Builder::table('users')
	->copy(['id', 'email', 'created_at'])
	->where('id', '=', 1)
	->onDuplicate('email', '=', RawExpression::values('email'))
	/* ->replace(true) // Optionally use REPLACE instead of INSERT */
	->to(
		'backup_users', /* Destination table */
		['id', 'email', 'created_at']
	);
```

***

### to 

Executes a prepared `copy()` query and inserts its result into the specified target table.

This method finalizes a `copy()` operation by executing the selection and inserting the results into another table using either `INSERT`, `INSERT IGNORE`, or `REPLACE`.

```php
public to(string $table, array $columns = ['*']): int
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$table` | **string** | The target table to insert copied data into. |
| `$columns` | **array<int,string>** | Target table columns to insert data into. |

**Return Value:**

`int` - Return the number of affected rows, or 0 if no rows were inserted or if debugging is enabled.

**Throws:**

- [Luminova\Exceptions\DatabaseException](/error-handlers/exceptions.md#databaseexception) - If copy mode isn't active, or if column mismatch occurs.
- [Luminova\Exceptions\InvalidArgumentException](/error-handlers/exceptions.md#invalidargumentexception) - If the target table name is empty.
- [Luminova\Exceptions\JsonException](/error-handlers/exceptions.md#jsonexception) - If copy operation involves JSON-encodable values and encoding fails.

> warning Ensure that source and destination columns match in count and structure.

***

### execute

Run a SQL query that was set earlier with `query()`.

You can use this method to execute prepared SQL statements with optional placeholder values and control how the result is returned. 

> **Note:**
> - If placeholders are provided, the query is executed using prepared statements.  
> - If no placeholders are given, the query is executed directly.
> Ensure that all values in the query are properly escaped to prevent SQL injection. 

**Fetch and Return Modes**

- `RETURN_ALL` → Returns all rows (default).
- `RETURN_NEXT` → Returns a single row or the next row from the result set.
- `FETCH_OBJ` → Fetch result as object.
- Refer to the [Luminova constants documentation](/global/constants.md) for details on database fetch and return modes.

```php
public execute(
	?array<string,mixed> $placeholder = null, 
	int $returnMode = RETURN_ALL, 
	int $fetchMode = FETCH_OBJ, 
	bool $escapePlaceholders = false 
): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$placeholder` | **array<string,mixed>&#124;null** | Optional key-value pairs for query placeholders. |
| `$returnMode` | **int** | Defines what the query should return (default: `RETURN_ALL`). |
| `$fetchMode` | **int** | Defines how results should be fetched (default: `FETCH_OBJ`). |
| `$escapePlaceholders` | **bool** | Whether to escape placeholder values (default: `false`). |

**Return Value:**

`mixed` - Returns query result, a statement object, or `false` on failure.

**Throws:**

- [Luminova\Exceptions\DatabaseException](/error-handlers/exceptions.md#databaseexception) - If called before setting a query with `query()`.

**Examples:** 

Running Raw SQL query:
```php
$result = Builder::query("SELECT * FROM users LIMIT 10")
     ->execute();
```

**Running SQL query with parameter binding:**

This method supports placeholder binding via an associative array. The key represents the placeholder name, and the value is the corresponding value to bind.

```php
$user = Builder::query("SELECT * FROM users WHERE id = :id LIMIT 1")
    ->execute(['id' => 100], RETURN_NEXT, FETCH_ASSOC);
```

**Using cache:**

To cache the query result, call the `cache()` method before invoking `execute()`. If caching is enabled and a cached result exists, the method returns the cached value immediately.  

```php
$user = Builder::query("SELECT * FROM users WHERE id = :id")
    ->cache()
    ->execute(['id' => 1]);
```

***

### count

Add a COUNT query to calculate the number of records in the table.

When `get`, `promise`, `stmt` or `fetch` method is called, it returns an `int` representing the total number of matching records. If no rows match, it returns `0`.

```php
public count(string $column = '*'): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$column` | **string** | The column to count (default: `*`). |

**Return Value:**

`self` - Returns the current query builder instance.

**Example:**

Get the number of users in country `NG`.

```php
$total = Builder::table('users')
    ->count()
    ->where('country', '=', 'NG')
    ->get();
```

***

### exists

Determine if a database table exists.

This method determines whether the specified table name exists in the database.

```php
public exists(): bool
```

**Return Value:**

`bool` - Return true if table exists in database, otherwise false.

**Throws:**

- [Luminova\Exceptions\DatabaseException](/error-handlers/exceptions.md#databaseexception) - Throw if an error occurs.

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

This method executes the current query to determine if a records exists in current table name.

```php
public has(): bool
```

**Return Value:**

`bool` - Return true if records exists in table, otherwise false.

**Throws:**

- [Luminova\Exceptions\DatabaseException](/error-handlers/exceptions.md#databaseexception) - Throw if an error occurs.

**Example:**

Check if users in country Nigeria exists in table:

```php
if(Builder::table('users')->where('country', '=', 'NG')->has()){
    echo 'Users in Nigeria exists';
}
```

***

### sum

Add query to calculate the total sum of a numeric column in the current table.

When `get`, `promise`, `stmt` or `fetch` method is called, it returns `int|float`, the total sum columns, otherwise 0 if no result.

```php
public sum(string $column): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$column` | **string** | The column to calculate the sum. |

**Return Value:**

`self` - Returns the current query builder instance.

***

### average

Add query to calculate the average value of a numeric column in the current table.

When `get`, `promise`, `stmt` or `fetch` method is called, it returns `int|float`, the total average of columns, otherwise 0 if no result.

```php
public average(string $column): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$column` | **string** | The column to calculate the average. |

**Return Value:**

`self` - Returns the current query builder instance.

***

### find

Add query to select a single/next record from current table.

When `get`, `promise`, `stmt` or `fetch` method is called, it returns `object|null|array|int|float|bool`, the selected single row, otherwise false if execution failed.

```php
public find(array<int,string> $columns = ['*']): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$columns` | **array<int,string>** | An array of table columns to return (default: `[*]`). |

**Return Value:**

`self` - Returns the current query builder instance.

***

### select

Add query to select multiple records from the current table.

When `get`, `promise`, `stmt` or `fetch` method is called, it returns `object|null|array|int|float|bool`, the selected rows, otherwise false if execution failed.

```php
public select(array<int,string> $columns = ['*']): self

```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$columns` | **array<int,string>** | An array of table columns to return (default: `[*]`). |

**Return Value:**

`self` - Returns the current query builder instance.

***

### columns

Sets the columns to be used in a `UNION` or `UNION ALL` operation.

This method specifies which columns should be included when combining results from multiple queries using **union** or **unionAll**. By default, all columns (`*`) are included.

**Samples:**

- Basic: `['id', 'name', 'email']`
- Alias: `['foo_id AS id', 'foo_name AS name', 'foo_email AS']`

```php
public columns(array $columns = ['*']): self

```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$columns` | **array<int,string>** | The columns to include in the union operation (default: `[*]`). |

**Return Value:**

`self` - Returns the current query builder instance.

***

### union

Combines the current query with another using the `UNION` operator.

Ensures both queries have the same number of columns. Resulting rows will be distinct.

```php
public union(Luminova\Database\Builder|\Closure $union): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$union` | **Builder\|Closure** | Another Builder instance or closure that return (`Builder` or `null`) to union with. |

**Return Value:**

`self` - Return current parent Builder object.

**Throws:**

- [Luminova\Exceptions\DatabaseException](/error-handlers/exceptions.md#databaseexception) - If error occurs.

**Example:**

`unionAll` and `union` have similar implementation and both support defining union in closure.

```php
$active = Builder::table('users')->select(['id', 'email'])
    ->where('status', '=', 'active');

$inactive = Builder::table('users')->select(['id', 'email'])
    ->where('status', '=', 'inactive');

$all = $active->union($inactive)
    ->descending('id')
	//->columns(...)
	//->where(...)
	//->or(...)
	//->in(...)
	//->conjoin(...)
	//->group(...)
	//->order(...)
    ->limit(10)
    ->get();
```

> **Note:** When using union tables, do not call `get`, `fetch` or `stmt` before adding table to another.
> Always call after all tables has been added.

***

### unionAll

Combines the current query with another using the `UNION ALL` operator.

Unlike `UNION`, this includes duplicate rows from both queries.

```php
public unionAll(Luminova\Database\Builder|\Closure $union): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$union` | **Builder\|Closure** | Another Builder instance or closure that return (`Builder` or `null`) to union with. |

**Return Value:**

`self` - Return current parent Builder object.

**Throws:**

- [Luminova\Exceptions\DatabaseException](/error-handlers/exceptions.md#databaseexception) - If error occurs.

> **Note:** When using union tables, do not call `get`, `fetch` or `stmt` before adding table to another.
> Always call after all tables has been added.

***

### unionAlias

Sets the alias for the combined unions subquery.

Useful when applying filters, sorting, or pagination to the result of a UNION query.

```php
public unionAlias(string $alias): self 
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$alias` | **string** | The alias to assign to the UNION result set. |

**Return Value:**

`self` - Returns the instance of the builder class.

**Throws:**

- [Luminova\Exceptions\InvalidArgumentException](/error-handlers/exceptions.md#invalidargumentexception) - If the invalid alias was provided.

> **Note:** This alias is used to wrap the UNION result in an outer SELECT statement like: `SELECT [alias].column FROM ( ... UNION ... ) AS [alias]`

***

### match

Defines columns to be used for a full-text search match.

This method registers a set of columns for a subsequent `AGAINST` clause in a full-text query. Multiple calls to this method will stack multiple `MATCH (...) AGAINST (...)` clauses.

```php
public match(array $columns): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$columns` | **array<int,string\|int>** | An array of column names to include in the full-text match.<br/>All column names must be valid SQL column identifiers.. |

**Return Value:**

`self` - Return current parent Builder object.

**Throws:**

- [Luminova\Exceptions\InvalidArgumentException](/error-handlers/exceptions.md#invalidargumentexception) - If the columns array is empty or contains invalid entries.

**Examples:**

Add match columns for full-text search.

```php
Builder::table('blogs')
	->select()
	->match(['title', 'description'])
		->against('fast laptop', Builder::MATCH_BOOLEAN)
	->match(['title', 'description'])
		->against('low laptop', Builder::MATCH_NATURAL)
	->get();
```

Match against title/description and order by relevance score.

```php
Builder::table('blogs')
	->select()
	->match(['title', 'description'])
		->orderAgainst('wireless keyboard', Builder::MATCH_BOOLEAN, 'DESC')
	->get();
```

***

### get

Execute the current SELECT query and return the result.

This method is used after building a query with methods like:
- `select()`
- `find()`
- `count()`
- `sum()`
- `average()`

It automatically runs the query and returns the result in the format you specify.

```php
public get(int $fetchMode = FETCH_OBJ, ?int $returnMode = null): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$fetchMode` | **int** | Defines how the result should be fetched (e.g., `FETCH_OBJ`, `FETCH_ASSOC`). |
| `$returnMode` | **int** | Optional return mode e.g., `RETURN_ALL`, `RETURN_NEXT` (default: `RETURN_ALL`). |

**Return Value:**

`mixed` - Returns query result on success, or `false`/`null` on failure.

**Throws:**

- [Luminova\Exceptions\DatabaseException](/error-handlers/exceptions.md#databaseexception) - If no query is set or execution fails.

**Examples:**

Basic SELECT example:
```php
$result = Builder::table('users')
    ->select(['email', 'name'])
    ->where('country', '=', 'NG')
    ->get(FETCH_OBJ);
```

Fetching a single row:

```php
$user = Builder::table('users')
    ->find(['email', 'name'])
    ->get(FETCH_ASSOC, RETURN_NEXT);
```

***

### fetch

Executes and fetches one row at a time — suitable for streaming or while loops.

**Suitable for:**
- `select()` When returning more than one results.

```php
public fetch(int $mode = FETCH_OBJ): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$mode` | **int** | Database fetch mode `FETCH_*` constant,  (default: `FETCH_OBJ`). |

**Return Value:**

`mixed` - Returns the fetched row, or `false`/`null` if execution fails.

**Throws:**

- [Luminova\Exceptions\DatabaseException](/error-handlers/exceptions.md#databaseexception) - If no query is available or execution fails.

**Example:**

For result statement:
```php
$stmt = Builder::table('users')
    ->select(['email', 'name'])
    ->where('country', '=', 'NG');

while ($row = $stmt->fetch(FETCH_OBJ)) {
    echo $row->email;
}
$stmt->freeStmt();
```

> **Note:** 
> The fetch method executes statements directly, so query result caching is not supported.

***

### stmt

Executes the current query and returns a prepared statement object.

This method runs the query and returns an instance of the database driver that
wraps the prepared statement. It allows you to work directly with the underlying
database driver object, using `PDOStatement`, `mysqli_stmt`, or `mysqli_result` depending on your database driver.

For more information about the methods available in the `DatabaseInterface` statement object, see the [Database Driver Documentation](/database/drivers.md).

```php
public stmt(): ?Luminova\Interface\DatabaseInterface
```

**Return Value:**

`Luminova\Interface\DatabaseInterface|null` - Returns a prepared statement object on success, or `null` if execution fails.

**Throws:**

- [Luminova\Exceptions\DatabaseException](/error-handlers/exceptions.md#databaseexception) - If no query is set or execution fails.

**Examples:**

Execute query in statement method:

```php
$stmt = Builder::table('users')
    ->find(['email', 'name'])
    ->where('id', '=', 1)
    ->stmt();

// Fetch as class object:
$user = $stmt->fetchObject(User::class);

// Fetch Next
$result = $stmt->fetchNext(FETCH_OBJ);
```

Execute query in statement method:

```php
$stmt = Builder::table('programming')
	->select(['name', 'type'])
	->where('language', '=', 'PHP')
	->stmt();

// Fetch All
$result = $stmt->fetchAll(FETCH_OBJ);

// Fetch and Read next
while($row => $stmt->fetch(RETURN_STREAM, FETCH_OBJ)){
	echo $row->name;
}

$stmt->free();
```

Accessing the raw PDO statement:
```php
$stmt = Builder::table('users')
    ->find(['email', 'name'])
    ->where('id', '=', 1)
    ->stmt();

$user = $stmt->getStatement()
    ->fetchAll(\PDO::FETCH_DEFAULT);
$stmt->freeStmt();
```

> **Note:** 
> Query result caching is not supported when using `stmt()`.

***

### promise

Executes the current query and returns results wrapped in a Promise.

Useful for asynchronous-style handling of database operations.
Resolves with the query results or rejects on error.

**Applies to:**
- `select()`
- `find()`
- `count()`
- `sum()`
- `average()`

```php
public promise(int $fetchMode = FETCH_OBJ, ?int $returnMode = null): PromiseInterface
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$fetchMode` | **int** | Fetch mode e.g, `FETCH_ASSOC`, `FETCH_OBJ`, or `FETCH_CLASS`, (default: `FETCH_OBJ`). |
| `$returnMode` | **int\|null** | Optional return mode e.g. `RETURN_ALL`, `RETURN_NEXT`, (default: `RETURN_ALL`). |

**Return Value:**

`Luminova\Interface\PromiseInterface` - Returns promise object that resolves with query results or rejects with a `Throwable`.

**Example:**

Execute query in promise resolver method:

```php
Builder::table('users')
    ->find(['email', 'name'])
    ->where('country', '=', 'NG')
    ->promise(FETCH_OBJ)
    ->then(function (mixed $result) {
		if($result){
        	echo $result->name;
		}
    })
    ->catch(function (\Throwable $e) {
        echo $e->getMessage();
    });
```

***

### delete

Execute query to delete records from the current table.

This method constructs and executes a `DELETE` statement based on the current query conditions. 
It ensures that strict mode prevents execution without a `WHERE` clause, reducing the risk of accidental deletions.

```php
public delete(): int
```

**Return Value:**

`int` - Return number of affected rows.

**Throws:**

- [Luminova\Exceptions\DatabaseException](/error-handlers/exceptions.md#databaseexception) - Throw if error occurs.

**Example:**

Delete table column:

 ```php
Builder::table('users')
	->where('id', '=', 1)
	->strict(true) /* Enable or disable strict where clause check */
	->delete();
```

***

### rename

Rename the current table to a new name.

This method constructs and executes a `DELETE` statement based on the current query conditions. 
It ensures that strict mode prevents execution without a `WHERE` clause, reducing the risk of accidental deletions.

```php
public rename(string $to): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$to` | **string** | The new table name. |

**Return Value:**

`bool` - Return true if the rename operation was successful, false otherwise.

**Throws:**

- [Luminova\Exceptions\DatabaseException](/error-handlers/exceptions.md#databaseexception) - If the database driver is unsupported or error occur.

**Example:**

Rename table name.

 ```php
Builder::table('users')
	->rename('new_users');
```

***

### transaction

Begins a transaction with optional read-only isolation level and `SAVEPOINT` for `PDO`.

**Transaction Flags:**

- For MySQLi: use `MYSQLI_TRANS_START_READ_ONLY` to set transaction as read-only.
- For PDO: No predefined flags, specify `4` to create read-only isolation.

```php
public transaction(int $flags = 0, ?string $name = null): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$flags` | **int** | Optional flags to set transaction properties (default: `0`). |
| `$name` | **string&#124;null** | Optional transaction name to use.<br> If provided in `PDO`, `SAVEPOINT` will be created with name instead. |

**Return Value:**

`bool` - Returns true if the transaction was successfully started, otherwise false.

**Throws:**

- [Luminova\Exceptions\DatabaseException](/error-handlers/exceptions.md#databaseexception) - Throws exception on `PDO` if failure to set transaction isolation level or create `savepoint`.

> **Note:** 
> - If `$flags` is set to `4` in `PDO`, which is equivalent to `MYSQLI_TRANS_START_READ_ONLY`, a read-only isolation level will be established. If the transaction starts successfully, it will return true.
> 
> - If `$name` is specified in `PDO`, a `SAVEPOINT` will be created. If the savepoint creation is successful, the transaction will return true.

**Example:**

```php
$tbl = Builder::table('users')
	->transaction()
	->where('country', '=', 'NG');

if($tbl->update(['suburb' => 'Enugu'])){
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

- [Luminova\Exceptions\DatabaseException](/error-handlers/exceptions.md#databaseexception) - Throws exception on `PDO` if failure to create `SAVEPOINT`.

***

### safeMode

Enables or disables safe mode for write and altering current table.

When enabled, supported operations will automatically run inside an internal transaction **only if no transaction is already active**. This protects against partial updates and makes operations more fault-tolerant during development or testing.

```php
public safeMode(bool $enable = true): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$enable` | **bool** | Whether to enable or disable safe mode (default: true). |

**Return Value:**

`self` - Returns the current builder instance.

**Applies to:**

* `insert()`
* `update()`
* `delete()`
* `drop()`
* `truncate()`
* `copy()`
* `temp()`

***

### truncate

Truncate database table records.

This method will attempt to clear all table records and optionally reset table auto-increment.

```php
public truncate(?int $restIncrement = null): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$restIncrement` | **bool** | Index to reset auto-increment if applicable (default `null`). |

**Return Value:**

`bool` - Return true truncation was completed, otherwise false.

**Throws:**

- [Luminova\Exceptions\DatabaseException](/error-handlers/exceptions.md#databaseexception) - Throws if execution encountered error.

**Example:**

```php
Builder::table('users')->truncate();
```

***

### temp

Creates a temporary table and copies all records from the main table to the temporary table.

```php
public temp(): bool
```

**Return Value:**

`bool` - Returns true if the operation was successful; false otherwise.

**Throws:**

- [Luminova\Exceptions\DatabaseException](/error-handlers/exceptions.md#databaseexception) - Thrown if a database error occurs during the operation.

> **Note:** 
> Temporary tables are automatically deleted when the current session ends. 
> You won't find these tables in `phpMyAdmin` or any other database manager as they are session-specific. 
> To query a temporary table, use the `temp_` prefix before the main table name.

**Example**

```php
if (Builder::table('users')->temp()) {
   $data = Builder::table('temp_users')->select();
}
```

***

### drop

Drops a database table or a temporary table if it exists.

When `$isTemporalTable` is set to `true`, the temporal created table of the current table will be dropped instead.

```php
public drop(bool $isTemporalTable = false): bool
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$isTemporalTable` | **bool** | Whether the table is a temporary table (default false). |

**Return Value:**

`bool` - Return true if table was successfully dropped, false otherwise.

**Throws:**

- [Luminova\Exceptions\DatabaseException](/error-handlers/exceptions.md#databaseexception) - Throws if error occurs.

**Examples:**

This will drop the `users` table from the database.

```php
Builder::table('users')
    ->drop(); // Table 'users' was dropped
```

This will drop the `users` temporal table from the database.

```php
Builder::table('users')
    ->drop(true); // Table 'temp_users' was dropped
```

---

### lockFor

Apply a row-level lock to the query for concurrency control.

Call this method before executing `find()` or similar fetch operations. Must be used within a transaction.

**Lock Modes:**

- `'update'`: Exclusive lock. Allows reads, blocks writes by others.
- `'shared'`: Shared lock. Allows others to read, but not write.

```php
public lockFor(string $mode = 'update'): self 
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$mode` | **string** | The lock mode: 'update' or 'shared' (default: `update`). |

**Return Value:**

`self` - Returns the current query builder instance.

**Throws:**

- [Luminova\Exceptions\InvalidArgumentException](/error-handlers/exceptions.md#invalidargumentexception) - If invalid lock type is given.

> **Note:** Must be used inside a transaction.

**Examples:**

Lock rows for update (exclusive lock):
```php
$tbl = Builder::Table('users');
 
$tbl->transaction();

$rows = $tbl->where('id', '=', 123)
    ->lockFor('update') /* Prevents others from reading or writing */
    ->find();

$tbl->commit();
```

Lock rows for shared read (shared lock):

```php
$tbl = Builder::Table('users');

$tbl->transaction();

$rows = $tbl->where('id', '=', 123)
    ->lockFor('shared') /* Allows others to read, but not write */
    ->find();

$tbl->commit();
```

---

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

- [Luminova\Exceptions\DatabaseException](/error-handlers/exceptions.md#databaseexception) - If an invalid action is provided or an invalid PostgreSQL lock name is used.

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

- [Luminova\Exceptions\DatabaseException](/error-handlers/exceptions.md#databaseexception) - If an invalid action is provided or an invalid PostgreSQL lock name is used.

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

- [Luminova\Exceptions\DatabaseException](/error-handlers/exceptions.md#databaseexception) - If an invalid action is provided or an invalid PostgreSQL lock name is used.

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

- [Luminova\Exceptions\DatabaseException](/error-handlers/exceptions.md#databaseexception) - Throws if database connection failed.

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

- [Luminova\Exceptions\DatabaseException](/error-handlers/exceptions.md#databaseexception) - If an invalid format is specified or if the export operation fails.

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

- [Luminova\Exceptions\DatabaseException](/error-handlers/exceptions.md#databaseexception) - If unable to create the backup directory or if failed to create the backup.

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

Enables debugging for query execution.

Supports multiple debug modes for tracking or dumping query strings and parameters. 
To print debug information call `dump(...)` or `printDebug(...)`.

**Modes:**

- `Builder::DEBUG_BUILDER`: Collects metadata (e.g., query strings and parameters).
- `Builder::DEBUG_BUILDER_DUMP`: Immediately outputs query strings and parameters.
- `Builder::DEBUG_DRIVER`: Enables driver-level debugging (e.g., PDO, MySQLi).
- `Builder::DEBUG_NONE`: Disable debugging.

```php
public debug(int $mode = self::DEBUG_BUILDER_DUMP): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$mode` | **int** | Debug mode to activate (default: `Builder::DEBUG_BUILDER_DUMP`). |

> **Note:** When debug is enabled, execution will not succeed, the result response can either be (`false`, `null` or `0`), depending on the result execution method called.

**Return Value:**

`self` - Returns the instance of the builder class.

> **Note:** In production, debug information is logged using the `debug` level when applicable.

**Example:**

```php
$tbl = Builder::table('users')
	->debug(Builder::DEBUG_BUILDER)
	->where('id', '=', 1001);

$result = $tbl->select();

// Get an array of debug information
print_r($result->getDebug());

// Or print the debug information
$result->dump('html');
```

Using the debug mode `DEBUG_BUILDER_DUMP` will print the sql query and params as it process

```php
$tbl = Builder::table('users')
	->debug(Builder::DEBUG_BUILDER_DUMP)
	->where('id', '=', 1001);

$result = $tbl->select();
```
**Output Format:**

```text
SQL QUERY

SELECT * FROM users
WHERE id = :id

QUERY PARAMS

:id = 1001
```

***

### dump

Print the debug query information in the specified format.

This method prints a detailed debug information about the query execution and for the last statement execution. It includes formats for `MySQL` and `PDO` SQL queries, as well as the binding mappings for each column.

If no format is provided or running is CLI/command mode, defaults to `print_r` without any formatting.

**Supported formats:**

- `null` → Print a readable array (default), 
- `html` → Format output in html `pre`. 
- `json` → Output as json-pretty print.

```php
public dump(?string $format = null): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$format` | **string&#124;null** | Optional output format (e.g, `html`, `json` or `NULL`).<br/>The format is only applied when debug mode is `Builder::DEBUG_BUILDER_DUMP` |

***

### reset

Reset query builder executions to default as needed.

This method frees the database statement cursor if not in transaction and `returns` is not a `statement` object. 

```php
public reset(): true
```

**Return Value:**

`true` - Always return true. 

> **Note:** It automatically closes database connection if `closeAfter` is enabled.

***

### free

Frees up the statement cursor and sets the statement object to null.

If in transaction, it will return false, you can free when transaction is done (e.g, `committed` or `rollback`).

```php
public free(): bool
```

**Return Value:**

`bool` - Return true if successful, otherwise false.

> **Note:** It will automatically closes database connection if `closeAfter` is enabled.

***

### close

Close database connection.

This method closes the current connection attached to query instance and also all open connection in pool.

```php
public close(): bool
```

**Return Value:**

`bool` - Return true if database connection is close, otherwise false. 

***

### close

Free statement cursor after executing result using `stmt` method.

```php
public freeStmt(): true
```

**Return Value:**

`true` - Always return true. 
