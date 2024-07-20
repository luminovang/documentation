# Database Migration

***

## Overview

Luminova database migration is a process that allows you to manage your database schema changes in a structured and version-controlled way.

***

## Introduction

Database migration is a process that allows you to manage your database schema changes in a structured and version-controlled way. It tracks the migrations that have already been run, making it a crucial part of database management. Luminova provides built-in support for database migrations through the `novakit` command line tool, making it easier to track, versions and apply changes to the database schema.

### How Migration Works

1. **Version Control**: Migration backups and lock files are stored in your project's `writable` directory. Each migration file contains a timestamp, ensuring a unique and ordered application of schema changes.

2. **Up, Down, and Alter Methods**: Migration files in Luminova typically include three methods: `up`, `down`, and optional `alter`. 

   - The `up` method applies changes to the database schema (e.g., creating tables, adding columns).
   - The `down` method reverts the changes made by the `up` or `modify` method (e.g., dropping tables), allowing for rollback if needed.
   - The `modify` method is an additional method introduced by Luminova. It allows for altering existing database tables without fully migrating them again, providing flexibility for incremental changes.

These methods ensure that database schema changes can be applied and reverted in a controlled manner, maintaining database integrity across different stages of development and deployment.

3. **Migration Setup**: Create a class that extends `\Luminova\Database\Migration`. Ensure all migration classes are placed in the `/app/Controllers/Database/Migrations/` directory for proper execution.

4. **Migration Management**: Luminova provides `novakit` commands to create, apply, and rollback migrations. These commands ensure that migrations are executed in the correct order and only once.

***

* Class namespace: `\Luminova\Database\Migration`
* This class is an **Abstract class**

***

## Methods
To get started to documentation reference for  [Database Table Schema](/database/schema.md).

### up

Defines the database table schema for migrations.

```php
public abstract up(): void
```

#### Up Examples

Define your database table schema within the `up` method.

```php
<?php
public function up(): void
{
  Schema::create('users', static function (Table $table): Table {
    $table->id();
    $table->string('name')->nullable(false);
    $table->string('phone')->nullable(false)->unique();
    $table->string('email')->nullable(false)->unique();
    $table->timestamps();

    return $table;
  });
}
```

***

### down

Defines the migration reversion.

```php
public abstract down(): void
```

#### Down Examples

Drop a table during rollback or migration within the `down` method.

```php
<?php
public function down(): void
{
  Schema::dropIfExists('users');
}
```

Or use the drop method.

```php
<?php
public function down(): void
{
  Schema::drop('users');
}
```

***

### alter

Defines the database table schema modifications to alter columns or tables.

The `alter` method is particularly useful for making targeted changes to database tables mostly in production environments. Unlike migrating an entire table and reseeding, `alter` allows you to apply specific alterations efficiently.

The `alter` method will create a lock version only if the current migration class isn't already locked. Otherwise, it executes the alteration without changing the latest version code.

It's crucial to use the `--drop-columns` flag with caution. This option drops any previous columns that do not exist in the new altering columns, potentially affecting data integrity.

```php
public alter(): void
```

***

#### Alter Examples

Define your database table or column schema modifiers within the `alter` method:
This example will add a `VARCHAR` column with `country` as the name, `NULLABLE` and default value `Nigeria`.

```php
<?php
public function alter(): void
{
    Schema::modify('users', static function (Table $table): Table {
        $table->string('country')->nullable()->default('Nigeria');
        return $table;
    });
}
```

To rename database table,  call `rename` method within the `alter` method:

```php
<?php
public function alter(): void
{
    Schema::rename('users', 'new-users');
}
```

***

### invoke

Invoke another migration class within the current migration `up` or `down` method.

```php
final protected invoke(class-string<\Luminova\Database\Migration> $migrate): void
```

**Parameters:**

| Parameter  | Type                                        | Description           |
|------------|---------------------------------------------|-----------------------|
| `$migrate` | **class-string<\Luminova\Database\Migration>** | The migration class name |

> **Note:** 
> If you specify other migration class using `invoke` method, it will also be executed while running `drop`, `up` or rollback migration.

***

### Command Usages

To executes database table migrations, applying the necessary changes to your database schema. These commands helps manage and apply schema changes in a version-controlled manner, ensuring consistency and ease of migration rollback if needed.

#### Migration Commands

To execute all pending migrations. 

```shell
php novakit db:migrate
```

To execute a specific migration class.
```shell
php novakit db:migrate --class=TestMigration
```

To reverse changes and rollback to earlier version, you need to specify the migration class and  use `rollback` flag.

```shell
php novakit db:migrate --class=TestMigration --rollback
```

***

### Drop Command 

You can also use the `db:drop` command to drop migrations, which is equivalent to executing `php novakit db:migrate --drop`. You can specify the `--no-backup` flag to skip creating a backup.

Drop all migrations table.

```shell
php novakit db:drop
```
Drop a specific migration class table.

```shell
php novakit db:drop --class=TestMigration
```

***

#### Migration Command Options

- `-c, --class`
  - **Description:** Specify the migration class to run.
  - **Usage:** `php novakit db:migrate --class=TestMigration`
  
- `-n, --no-backup`
  - **Description:** Run migration without creating a backup. This option is useful for speeding up the process but should be used with caution.
  - **Usage:** `php novakit db:migrate --no-backup`

- `-d, --drop`
  - **Description:** Drop the table by calling the `down` method during migration. This option helps in cleaning up old or deprecated tables.
  - **Usage:** `php novakit db:migrate --drop`

- `-r, --rollback`
  - **Description:** Rollback the migration to the previous version. This is helpful for reverting changes that were applied by mistake or need adjustments.
  - **Usage:** `php novakit db:migrate --rollback`

- `-b, --debug`
  - **Description:** Display `SQL` query string that was complied to execute in migration.
  - **Usage:** `php novakit db:migrate --debug`

- `-i, --invoke`
  - **Description:** Run migration and also invoke other invokable migration classes.
  - **Usage:** `php novakit db:migrate --invoke`

***

#### Alter Commands

Execute pending database table alterations.

```shell
php novakit db:alter
```

Execute alterations for a specific migration class.

```shell
php novakit db:alter --class=TestMigration
```

***

#### Alter Command Options

- `-c, --class`
  - **Description:** Specify the migration class for alteration.
  - **Example:** `php novakit db:alter --class=TestMigration`
  
- `-n, --no-backup`
  - **Description:** Execute alteration without creating a backup. Useful for speeding up the process.
  - **Example:** `php novakit db:alter --no-backup`

- `-d, --drop-columns`
  - **Description:** Drop previous columns not present in the new alteration.
  - **Example:** `php novakit db:alter --drop-columns`

- `-b, --debug`
  - **Description:** Display `SQL` query string that was complied to execute in migration alteration.
  - **Usage:** `php novakit db:alter --debug`

***

## Schema Methods

These methods can be called within the `up`, `down`, or `alter` methods of your migration class.

- **Full name:** `\Luminova\Database\Schema`
- **Final Class:** This class is marked as **final** and cannot be subclassed.

### create

Creates a new table using a callback function to build the table schema.

```php
public static create(string $tableName, \Closure $tableCallback): void
```

**Parameters:**

| Parameter      | Type       | Description                                        |
|----------------|------------|----------------------------------------------------|
| `$tableName`   | **string** | The name of the table (non-empty string).          |
| `$tableCallback` | **\Closure** | The callback function that defines the table structure. |

**Throws:**

- [\Luminova\Exceptions\DatabaseException](/exceptions/classes.md#databaseexception) - If any error occurred.

> The `Closure` table callback function must return an instance of `\Luminova\Database\Table`.
>
> Your callback function must must type-hint `\Luminova\Database\Table` which will be injected during migration execution.

***

### modify

Alters a table and its columns using a callback function to modify the table schema.
This is should be called within the `alter` method of your migration class.

```php
public static modify(string $tableName, \Closure $tableCallback): void
```

**Parameters:**

| Parameter      | Type       | Description                                        |
|----------------|------------|----------------------------------------------------|
| `$tableName`   | **string** | The name of the table (non-empty string).          |
| `$tableCallback` | **\Closure** | The callback function that defines the table structure. |

**Throws:**

- [\Luminova\Exceptions\DatabaseException](/exceptions/classes.md#databaseexception) - If any error occurred.

> The `Closure` table callback function must return an instance of `\Luminova\Database\Table`.
>
> Your callback function must must type-hint `\Luminova\Database\Table` which will be injected during migration alteration execution.

***

### rename

Alter rename a table in the specified database.
This is should be called within the `alter` method of your migration class.

```php
public static rename(string $from, string $to, string $database = 'mysql'): void
```

**Parameters:**

| Parameter      | Type       | Description                                        |
|----------------|------------|----------------------------------------------------|
| `$from`   | **string** | The current name of the table.          |
| `$to` | **string** | The new name of the table. |
| `$database` | **string** | The type of database (e.g., 'sql-server', 'mysql', 'oracle', 'ms-access') (default: `mysql`). |

**Throws:**

- [\Luminova\Exceptions\DatabaseException](/exceptions/classes.md#databaseexception) - If any error occurred.

***

### dropIfExists

Drops a table if it exists.
This is should be called within the `down` method of your migration class.

```php
public static dropIfExists(string $tableName): void
```

**Parameters:**

| Parameter    | Type       | Description                                        |
|--------------|------------|----------------------------------------------------|
| `$tableName` | **string** | The name of the table (non-empty string).          |

***

### drop

Drops a table.
This is should be called within the `down` method of your migration class.

```php
public static drop(string $tableName): void
```

**Parameters:**

| Parameter    | Type       | Description                                        |
|--------------|------------|----------------------------------------------------|
| `$tableName` | **string** | The name of the table (non-empty string).          |
