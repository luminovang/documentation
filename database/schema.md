# Table Schema Definitions and Design Guidelines

***

## Overview

Luminova offers convenient and flexible methods for defining and managing database table schemas. This guide covers how to create, modify, and manage database tables using the schema builder.

***

## Introduction

The Luminova Database Table Schema allows developers to define database tables and their columns using an expressive, fluent syntax. It supports a wide range of data types and column modifiers, making it easy to create complex table structures.

***
[Column Modifiers](/database/schema.md)
[Additional Column Data Type Methods](/database/schema.md)

Table colum creation methods.

* Class namespace: `Luminova\Database\Table`

## Constants
These constant variables allows you to easily use predefine values.

| Constant Name          | Description                        | Value                  |
|------------------------|------------------------------------|------------------------|
| DEFAULT_NONE           | Default field value none.          | `__NONE__`             |
| DEFAULT_TIMESTAMP      | Default field value timestamp.     | `CURRENT_TIMESTAMP`    |
| DEFAULT_NULL           | Default field value null.          | `NULL`                 |
| INDEX_PRIMARY_KEY      | Primary key.                       | `PRIMARY KEY`          |
| INDEX_FOREIGN_KEY      | Foreign key.                       | `FOREIGN KEY`          |
| INDEX_UNIQUE           | Unique table index.                | `UNIQUE`               |
| INDEX_DEFAULT          | Indexed table index.               | `INDEX`                |
| INDEX_FULLTEXT         | Fulltext table index.              | `FULLTEXT`             |
| INDEX_SPATIAL          | Spatial table index.               | `SPATIAL`              |
| MYSQL                  | Generate schema for MySQL database.| `mysql`                |
| SQL_SERVER             | Generate schema for SQL Server database. | `sql-server`    |
| MS_ACCESS              | Generate schema for MS Access database. | `ms-access`    |
| ORACLE                 | Generate schema for Oracle database.| `oracle`               |
| COLUMN_NAME            | Replaces format to column name.    | `{__REPLACE_COLUMN_NAME__}` |

***

## Properties

These properties can be use to specify database type, global, session, collection, comment or type of database table creation `SQL` schema to generate.

### database

Specify the database type to generate schema for.

```php
public string $database = 'mysql';
```

***

### session

Initialize database sessions that will be created during migration.
The array key as the session identifier/name while the value is the session value to apply.

```php
public array&lt;string,mixed&gt; $session = [];
```

***

### global

Initialize database globals that will be created during migration.
The array key as the global identifier/name while the value is the global value to apply.

```php
public array&lt;string,mixed&gt; $global = [];
```

***

### collation

Set the table collation type.

```php
public ?string $collation = null;
```

***

### charset

Set the database default charset.

```php
public ?string $charset = null;
```

***

### comment

Set the table command.

```php
public ?string $comment = null;
```

***

### ifNotExists

Specify weather to use create if not exists or just create table.

```php
public bool $ifNotExists = false;
```

***

### prettify

Specify if table query should be print in new line.

```php
public bool $prettify = true;
```

***

### engine

Set storage engines to handle SQL operations.

```php
public ?string $engine = null;
```

***

## Methods

These methods cover a wide range of database column types and useful column modifiers. To add a column, call the method corresponding to the desired data type, passing the column name as the first parameter followed by any additional required parameters (e.g., length or value) depending on the data type.

### Examples

Add a column for `NUMBER` data type:

```php
$table->number('product_id', 10);
```

Add a column for `GEOMETRYCOLLECTION` data type:

```php
$table->geometryCollection('geometry');
```

***

### constructor

Constructor to initialize the table with a name.

```php
public __construct(string $tableName, bool $ifNotExists = false, ?string $collation = null, ?string $comment = null): mixed
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$tableName` | **string** | The name of the table. |
| `$database` | **string** | The database type (default: `MYSQL`). |
| `$ifNotExists` | **bool** | Whether to add `if not exists` (default: false). |
| `$collation` | **string&#124;null** | Optional table collation type (default: null). |
| `$comment` | **string&#124;null** | Optional table comment (default: null). |

**Throws:**

- [Luminova\Exceptions\DatabaseException](/error-handlers/exceptions.md#databaseexception) - If the table name is an empty string.

***

### string

Create a new column to store string values.

```php
public string(string $name, string $type = 'VARCHAR', int|null $length = 255): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$name` | **string** | The name of the column. |
| `$type` | **string** | The column data type (default: VARCHAR(255)). |
| `$length` | **int&#124;null** | (Optional) The length or size of the column (default: 255). |

**Return Value:**

`self` - Return table class instance.

***

### integer

Alias for `int` method, to creates an `INT` column with optional length.

```php
public integer(string $name, ?int $length = null): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$name` | **string** | The name of the column. |
| `$length` | **int&#124;null** | The optional length of the column. |

**Return Value:**

`self` - Return table class instance.

***

### number

Creates an `NUMBER` column with optional length.

```php
public number(string $name, ?int $precision = null, ?int $scale = null): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$name` | **string** | The name of the column. |
| `$precision` | **int&#124;null** | The optional precision length of the column (default: `NULL`). |
| `$scale` | **int&#124;null** | The optional scale length of the column (default: `NULL`). |

**Return Value:**

`self` - Return table class instance.

***

### decimal

Creates a `DECIMAL` column with precision and scale length.

```php
public decimal(string $name, int $precision = 10, int $scale = 0): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$name` | **string** | The name of the column. |
| `$precision` | **int** | The precision of the decimal (default: 10). |
| `$scale` | **int** | The scale of the decimal (default: 0). |

**Return Value:**

`self` - Return table class instance.

***

### float

Creates a `FLOAT` column with precision and scale length.

```php
public float(string $name, int $precision = 10, int $scale = 2): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$name` | **string** | The name of the column. |
| `$precision` | **int** | The precision of the float (default: 10). |
| `$scale` | **int** | The scale of the float (default: 2). |

**Return Value:**

`self` - Return table class instance.

***

### double

Creates a `DOUBLE` column with precision and scale length.

```php
public double(string $name, int $precision = 10, int $scale = 2): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$name` | **string** | The name of the column. |

**Return Value:**

`self` - Return table class instance.

***

### text

Creates a `TEXT` column with optional length.

```php
public text(string $name, int|null $length = 255): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$name` | **string** | The name of the column. |
| `$length` | **int&#124;null** | The optional length of the column (default: 255). |

**Return Value:**

`self` - Return table class instance.

***

### date

Creates a `DATE` column with optional length.

```php
public date(string $name, int|null $length = null): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$name` | **string** | The name of the column. |
| `$length` | **int&#124;null** | The optional length of the column. |

**Return Value:**

`self` - Return table class instance.

***

### enum

Creates an `ENUM` column with default values.

```php
public enum(string $name, array $values): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$name` | **string** | The name of the column. |
| `$values` | **array** | The values for the `ENUM` column. |

**Return Value:**

`self` - Return table class instance.

***

### set

Creates a `SET` column with default values.

```php
public set(string $name, array $values): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$name` | **string** | The name of the column. |
| `$values` | **array** | The values for the `SET` column. |

**Return Value:**

`self` - Return table class instance.

***

### id

An alias for `bigInt` method, to creates a primary key column `id` set to auto increment,  `BIGINT` and `UNSIGNED` attribute.

```php
public id(string $name = 'id'): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$name` | **string** | Optional column name for id (default: `id`). |

**Return Value:**

`self` - Return table class instance.

***

### ulid

Creates a new column for `ULID` using `CHAR(25)`.
This method defines a column for storing `ULIDs` (Universally Unique Lexicographically Sortable Identifiers).

```php
public ulid(string $name): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$name` | **string** | The name of the ulid column (default: `ulid`). |

**Return Value:**

`self` - Return table class instance.

***

### uuid

Creates a new column for `UUID` using `CHAR(36)`.
This method defines a column for storing `UUIDs` (Universally Unique Identifiers).

```php
public uuid(string $name): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$name` | **string** | The name of the UUID column (default: `uuid`). |

**Return Value:**

`self` - Return table class instance.

***

### timestamps

Creates columns for timestamps with the names `created_on` and `updated_on`.
Both columns are set to be `NULLABLE` with a default value of `NULL`.

```php
public timestamps(?string $precision = null): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$precision` | **int&#124;null** | Optional precision for the timestamp columns (default: `NULL`). |

**Return Value:**

`self` - Return table class instance.

> *Note:* Trying to add other attributes to this column will not take any effect on `created_on`, as it will only add to `updated_on`

***

### datetime

Creates a `DATETIME` column with optional precision length.

```php
public datetime(string $name, int|null $precision = null): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$name` | **string** | The name of the column. |
| `$precision` | **int&#124;null** | The optional precision length of the column. |

**Return Value:**

`self` - Return table class instance.

***

### timestamp

Creates a `TIMESTAMP` column with optional precision length.

```php
public timestamp(string $name, int|null $precision = null): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$name` | **string** | The name of the column. |
| `$precision` | **int&#124;null** | The optional precision length of the column. |

**Return Value:**

`self` - Return table class instance.

***

### time

Creates a `TIME` column with optional precision length.

```php
public time(string $name, int|null $precision = null): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$name` | **string** | The name of the column. |
| `$precision` | **int&#124;null** | The optional precision length of the column. |

**Return Value:**

`self` - Return table class instance.

***

### year

Creates a `YEAR` column with optional precision length.

```php
public year(string $name, int|null $precision = null): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$name` | **string** | The name of the column. |
| `$precision` | **int&#124;null** | The optional precision length of the column. |

**Return Value:**

`self` - Return table class instance.

***

### json

Creates a `JSON` column.

```php
public json(string $name): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$name` | **string** | The name of the column. |

**Return Value:**

`self` - Return table class instance.

***

## Column Modifiers

In addition to column types, you can also apply modifiers to columns to define constraints and default values. Here are  the predefined modifiers.

### type

Updates the column data type.
Use this method to modify or specify the data type of the column if necessary.

```php
public type(string $type): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$type` | **string** | The new data type for the column. |

**Return Value:**

`self` - Return table class instance.

***

### attribute

Adds additional attributes to the current column.
This method allows for specifying additional column attributes such as `UNSIGNED`, `AUTO_INCREMENT`, etc.

```php
public attribute(string $attribute): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$attribute` | **string** | The attribute to add to the column definition. |

**Return Value:**

`self` - Return table class instance.

 > Multiple attributes can be chained together for the same column.

**Example Usage**

Adds the `UNSIGNED` and `AUTO_INCREMENT` attribute to the column.

```php 
<?php
$table->attribute('UNSIGNED')->attribute('AUTO_INCREMENT');
```

***

### entry

Define additional column entry that will be added to the table definition.

```php
public entry(string $query): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$query` | **string** | The entry query string to add. |

**Return Value:**

`self` - Returns the table class instance.

**Example Usage**

This method allows adding custom column entries that will appear in the `CREATE TABLE` statement.

```php 
<?php
$table->entry("CONSTRAINT fk_foo_bar FOREIGN KEY (foo_id) REFERENCES bar(bar_id)");
```

Passing fields string into the table definitions with.

```php 
<?php
$table->entry("name varchar(50) NOT NULL DEFAULT 'default value'");
```

***

### exec

Define additional execution constraint to execute after table creation.

```php
public exec(string $query): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$query` | **string** | The execution query string to add. |

**Return Value:**

`self` - Returns the table class instance.

**Example Usage**

This method adds execution queries that will be executed after the table is created.

```php 
<?php
$table->exec("ALTER TABLE foo ADD CONSTRAINT ...");
```
Alter column after table creation definition.
```php 
<?php
$table->exec("ALTER TABLE users MODIFY COLUMN column_name INT VISIBLE");
```

***

### check

Define a CHECK constraint for a column.

```php
public check(string $column, string $operator, string|float|int|null $value): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$column` | **string** | The name of the column to apply the CHECK constraint on. |
| `$operator` | **string** | The comparison operator for the CHECK constraint (e.g., '=', '&gt;', '&lt;='). |
| `$value` | **string&#124;float&#124;int&#124;null** | The value to compare against in the CHECK constraint. |

**Return Value:**

`self` - Returns the table class instance.

**Example Usage**

This method adds a CHECK constraint for the specified column, with an operator and value.

```php 
<?php
$table->check("column_name", ">=", 100);
```

***

### autoIncrement

Sets the column to auto increment.
This method adds the `AUTO INCREMENT` attribute to an integer column,
suitable for primary key columns to automatically generate unique values.

```php
public autoIncrement(int $start = 1, int $increment = 1): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$start` | **int** | The starting value for the auto increment. |
| `$increment` | **int** | The increment value for each auto increment step. |

**Return Value:**

`self` - Return table class instance.

***

### nullable

Sets the column to nullable.
This method sets the column to allow `NUL`L values in the database.

```php
public nullable(bool $nullable = true): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$nullable` | **bool** | Optional pass false for not nullable (default: true). |

**Return Value:**

`self` - Return table class instance.

***

### invisible

Sets the column to invisible in `MYSQL`.
Invisible columns are not shown in `SELECT *` queries but can still be accessed when explicitly specified in the query.

```php
public invisible(): self
```

**Return Value:**

`self` - Return table class instance.

> The `INVISIBLE` attribute can only be used in `MySQL 8.0.23` and later.

***

### visible

Sets the column to visible in `MYSQL`.
This is useful for alter when column was previously set to `INVISIBLE`.

```php
public nullable(): self
```

**Return Value:**

`self` - Return table class instance.

> The `VISIBLE` attribute can only be used in `MySQL 8.0.23` and later.

***

### default

Sets the default value for the column, optionally you can use predefine defaults `Table::DEFAULT_*`.
This method assigns a default value to the column, which can be a string, integer, float, boolean, array, json object or null.
It is often used to provide a fallback value when no explicit value is provided during record insertion.

```php
public default(mixed $value = null): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$value` | **mixed** | The default value for the column (default: NULL). |

**Return Value:**

`self` - Return table class instance.

***

### length

Sets the precision and optional scale length for a column.

```php
public length(int $precision, int|null $scale = null): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$precision` | **int** | The precision length of the column. |
| `$scale` | **int&#124;null** | Optional scale length of decimal, float, or double column types (default: NULL). |

**Return Value:**

`self` - Return table class instance.

***

### collation

Sets the collation for column.

```php
public collation(string $collate): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$collate` | **string** | The column collation to use (e.g. `utf8_unicode_ci`). |

**Return Value:**

`self` - Return table class instance.

***

### charset

Sets the charset for column.

```php
public charset(string $charset): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$charset` | **string** | The column charset to use (e.g. `utf8`). |

**Return Value:**

`self` - Return table class instance.

***

### index

Define an index constraint on a column.
This method allows you to specify various types of index constraints on a table column,
such as default, unique, `fulltext`, `spatial`, or `primary key`.

```php
public index(string $index = self::INDEX_INDEX): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$index` | **string** | The type of index constraint to apply (default: `INDEX`). |

**Return Value:**

`self` - Return table class instance.

**Throws:**

- [Luminova\Exceptions\DatabaseException](/error-handlers/exceptions.md#databaseexception) - If an unsupported index type is provided.

**Constants Index Types:**

- `Table::INDEX_DEFAULT`: Creates a standard index on the column.
- `Table::INDEX_UNIQUE`: Creates a unique index on the column.
- `Table::INDEX_FULLTEXT`: Creates a fulltext index on the column (MySQL specific).
- `Table::INDEX_SPATIAL`: Creates a spatial index on the column (MySQL specific).
- `Table::INDEX_PRIMARY_KEY`: Sets the column as a primary key.

***

### defaultIndex

Sets a column index as `INDEX` (e.g., `column_name INDEX`).
This method adds a regular index to the column, optimizing data retrieval and query performance.

```php
public defaultIndex(string|null $columns = null): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$columns` | **string&#124;null** | The column names to apply the index on, separated by commas (e.g., `column1,column2`) (optional). |

**Return Value:**

`self` - Returns the table class instance.

***

### fullText

Sets a column index as `FULLTEXT` (e.g., `column_name FULLTEXT INDEX`).
This method specifies that the column is optimized for full-text searches, allowing efficient keyword-based searching of text within the column.

```php
public fullText(string|null $columns = null): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$columns` | **string&#124;null** | The column names to apply the index on, separated by commas (e.g., `column1,column2`) (optional). |

**Return Value:**

`self` - Return table class instance.

***

### spatial

Sets a column index as `SPATIAL` (e.g., `column_name SPATIAL INDEX`).
This method specifies that the column is used for spatial data types, enabling efficient querying of spatial data like points, lines, and polygons.

```php
public spatial(string|null $columns = null): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$columns` | **string&#124;null** | The column names to apply the index on, separated by commas (e.g., `column1,column2`) (optional). |

**Return Value:**

`self` - Return table class instance.

***

### unique

Sets a column as a `UNIQUE` key inline with the column definition (e.g., `column_name UNIQUE INDEX`).
This method specifies that the column must contain unique values across all rows in the table.

```php
public unique(string|null $columns = null): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$columns` | **string&#124;null** | The column names to apply the index on, separated by commas (e.g., `column1,column2`) (optional). |

**Return Value:**

`self` - Return table class instance.

***

### primary

Sets the column as `PRIMARY KEY`.
This method designates the column as the primary key of the table, ensuring each row is uniquely identified by its values in this column.

```php
public primary(bool $constraint = false): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$constraint` | **bool** | Whether to use a primary key constraint (default: false). |

**Return Value:**

`self` - Return table class instance.

**Throws:**

- [Luminova\Exceptions\DatabaseException](/error-handlers/exceptions.md#databaseexception) - If attempting to add a primary key constraint while multiple columns are already designated as primary key.

**Example Usages:*

Sets column as primary key: PRIMARY KEY (`column_name`).

```php
<?php
$table->primary();
```

Sets column as primary key constraint: CONSTRAINT pk_table_name PRIMARY KEY (`column_name`).

```php
<?php
$table->primary(true);
```

***

### foreign

Define a foreign key relationship.

```php
public foreign(string $table, string $column, bool $constraint = false): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$table` | **string** | The name of the referenced table. |
| `$column` | **string** | The column in the referenced table. |
| `$constraint` | **bool** | Whether to include a named constraint (default: false). |

**Return Value:**

`self` - Returns the table class instance.

***

### unsigned

Add `UNSIGNED` attribute to a column definition.
For `non-MySQL` databases, a `CHECK (column >= 0)` constraint is used to enforce non-negative values.

```php
public unsigned(): self
```

**Return Value:**

`self` - Return table class instance.

***

### comment

Sets a comment for the column.

```php
public comment(string $comment): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$comment` | **string** | The comment to set. |

**Return Value:**

`self` - Return table class instance.

***

### virtual

Define a `VIRTUAL` generated column.

```php
public virtual(string $expression): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$expression` | **string** | The SQL expression defining the column's value. |

**Return Value:**

`self` - Return table class instance.

**Example Usages:*

This would create a virtual generated column that concatenates `first_name` and `last_name`.

```php
<?php
$table->virtual("CONCAT(first_name, ' ', last_name)");
```

This would create a virtual generated column that computes `quantity * price`.

```php
<?php
$table->virtual("quantity * price");
```

This would create a virtual generated column in Oracle that concatenates `first_name` and `last_name`.

```php
<?php
$table->virtual("first_name || ' ' || last_name");
```

***

### stored

Define a STORED generated column.

```php
public stored(string $expression): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$expression` | **string** | The SQL expression defining the column's value. |

**Return Value:**

`self` - Returns the table class instance.

**Example Usages:*

This would create a stored generated column that concatenates `first_name` and `last_name`.

```php
<?php
$table->stored("CONCAT(first_name, ' ', last_name)");
```

This would create a stored generated column that computes `quantity * price`.

```php
<?php
$table->stored("quantity * price");
```

***

### mediaType

Sets the media type for the column.

```php
public mediaType(string $type): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$type` | **string** | The media type to set. |

**Return Value:**

`self` - Return table class instance.

***

### browserDisplay

Sets browser display transformation for the column.

```php
public browserDisplay(string $type, array|null $option = null): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$type` | **string** | The transformation type. |
| `$option` | **array&#124;null** | Optional transformation option. |

**Return Value:**

`self` - Return table class instance.

***

### inputTransformation

Sets input transformation for the column.

```php
public inputTransformation(string $type, array|null $option = null): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$type` | **string** | The transformation type. |
| `$option` | **array&#124;null** | Optional transformation option. |

**Return Value:**

`self` - Return table class instance.

***

### first

Reorder column at the beginning.

```php
public first(): self
```

**Return Value:**

`self` - Return table class instance.

***

### after

Reorder column after a specific column name.

```php
public after(string $column): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$column` | **string** | The column name to move after it. |

**Return Value:**

`self` - Return table class instance.

***

### Other Column Data Type Methods

These methods are used to add other types of column data types, with names derived from the corresponding `SQL` data types they represent.

***

### varchar

Creates a `VARCHAR` column with optional length.

```php
public varchar(string $name, int|null $length = 255): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$name` | **string** | The name of the column. |
| `$length` | **int&#124;null** | The optional length of the column (default: 255). |

**Return Value:**

`self` - Return table class instance.

***

### tinyInt

Creates a `TINYINT` column with optional length.

```php
public tinyInt(string $name, int|null $length = null): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$name` | **string** | The name of the column. |
| `$length` | **int&#124;null** | The optional length of the column. |

**Return Value:**

`self` - Return table class instance.

***

### smallInt

Creates a `SMALLINT` column with optional length.

```php
public smallInt(string $name, int|null $length = null): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$name` | **string** | The name of the column. |
| `$length` | **int&#124;null** | The optional length of the column. |

**Return Value:**

`self` - Return table class instance.

***

### mediumInt

Creates a `MEDIUMINT` column with optional length.

```php
public mediumInt(string $name, int|null $length = null): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$name` | **string** | The name of the column. |
| `$length` | **int&#124;null** | The optional length of the column. |

**Return Value:**

`self` - Return table class instance.

***

### bigInt

Creates a `BIGINT` column with optional length.

```php
public bigInt(string $name, int|null $length = null): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$name` | **string** | The name of the column. |
| `$length` | **int&#124;null** | The optional length of the column. |

**Return Value:**

`self` - Return table class instance.

***

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$name` | **string** | The name of the column. |
| `$precision` | **int** | The precision of the double (default: 10). |
| `$scale` | **int** | The scale of the double (default: 2). |

**Return Value:**

`self` - Return table class instance.

***

### real

Creates a `REAL` column with optional length.

```php
public real(string $name, int|null $length = null): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$name` | **string** | The name of the column. |
| `$length` | **int&#124;null** | The optional length of the column. |

**Return Value:**

`self` - Return table class instance.

***

### bit

Creates a `BIT` column with optional length.

```php
public bit(string $name, int|null $length = null): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$name` | **string** | The name of the column. |
| `$length` | **int&#124;null** | The optional length of the column. |

**Return Value:**

`self` - Return table class instance.

***

### boolean

Creates a `BOOLEAN` column, a synonym for `TINYINT(1)`.

```php
public boolean(string $name): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$name` | **string** | The name of the column. |

**Return Value:**

`self` - Return table class instance.

***

### serial

Creates a `SERIAL` column, an alias for `BIGINT UNSIGNED NOT NULL AUTO_INCREMENT UNIQUE`.

```php
public serial(string $name): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$name` | **string** | The name of the column. |

**Return Value:**

`self` - Return table class instance.

***

### char

Creates a `CHAR` column with optional length.

```php
public char(string $name, int|null $length = 255): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$name` | **string** | The name of the column. |
| `$length` | **int&#124;null** | The optional length of the column (default: 255). |

**Return Value:**

`self` - Return table class instance.

***

### tinyText

Creates a `TINYTEXT` column.

```php
public tinyText(string $name): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$name` | **string** | The name of the column. |

**Return Value:**

`self` - Return table class instance.

***

### mediumText

Creates a `MEDIUMTEXT` column.

```php
public mediumText(string $name): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$name` | **string** | The name of the column. |

**Return Value:**

`self` - Return table class instance.

***

### longText

Creates a `LONGTEXT` column.

```php
public longText(string $name): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$name` | **string** | The name of the column. |

**Return Value:**

`self` - Return table class instance.

***

### binary

Creates a `BINARY` column with optional length.

```php
public binary(string $name, int|null $length = 255): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$name` | **string** | The name of the column. |
| `$length` | **int&#124;null** | The optional length of the column (default: 255). |

**Return Value:**

`self` - Return table class instance.

***

### varBinary

Creates a `VARBINARY` column with optional length.

```php
public varBinary(string $name, int|null $length = 255): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$name` | **string** | The name of the column. |
| `$length` | **int&#124;null** | The optional length of the column (default: 255). |

**Return Value:**

`self` - Return table class instance.

***

### tinyBlob

Creates a `TINYBLOB` column with optional length.

```php
public tinyBlob(string $name, int|null $length = null): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$name` | **string** | The name of the column. |
| `$length` | **int&#124;null** | The optional length of the column. |

**Return Value:**

`self` - Return table class instance.

***

### blob

Creates a `BLOB` column.

```php
public blob(string $name): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$name` | **string** | The name of the column. |

**Return Value:**

`self` - Return table class instance.

***

### mediumBlob

Creates a `MEDIUMBLOB` column.

```php
public mediumBlob(string $name): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$name` | **string** | The name of the column. |

**Return Value:**

`self` - Return table class instance.

***

### longBlob

Creates a `LONGBLOB` column.

```php
public longBlob(string $name): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$name` | **string** | The name of the column. |

**Return Value:**

`self` - Return table class instance.

***

### geometry

Creates a `GEOMETRY` column.

```php
public geometry(string $name): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$name` | **string** | The name of the column. |

**Return Value:**

`self` - Return table class instance.

***

### point

Creates a `POINT` column.

```php
public point(string $name): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$name` | **string** | The name of the column. |

**Return Value:**

`self` - Return table class instance.

***

### lineString

Creates a `LINESTRING` column.

```php
public lineString(string $name): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$name` | **string** | The name of the column. |

**Return Value:**

`self` - Return table class instance.

***

### polygon

Creates a `POLYGON` column.

```php
public polygon(string $name): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$name` | **string** | The name of the column. |

**Return Value:**

`self` - Return table class instance.

***

### multiPoint

Creates a `MULTIPOINT` column.

```php
public multiPoint(string $name): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$name` | **string** | The name of the column. |

**Return Value:**

`self` - Return table class instance.

***

### multiLineString

Creates a `MULTILINESTRING` column.

```php
public multiLineString(string $name): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$name` | **string** | The name of the column. |

**Return Value:**

`self` - Return table class instance.

***

### multiPolygon

Creates a `MULTIPOLYGON` column.

```php
public multiPolygon(string $name): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$name` | **string** | The name of the column. |

**Return Value:**

`self` - Return table class instance.

***

### geometryCollection

Creates a `GEOMETRYCOLLECTION` column.

```php
public geometryCollection(string $name): self
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$name` | **string** | The name of the column. |

**Return Value:**

`self` - Return table class instance.