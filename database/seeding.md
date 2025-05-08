# Automated Database Seeding and Execution

***

## Overview

Luminova simplifies database management with the novakit command and its associated methods. This ensures an easy process for executing seeders and convenient table truncation.

***

## Introduction

Database seeding is a process that allows you to populate your database with initial data, test data, or additional data required for your application. It is an essential part of database management, especially during development and testing phases. Luminova offers built-in support for database seeding through the `novakit` command line tool, making it easy to manage and apply seed data in a structured and repeatable manner.

***

### How Seeding Works

1. **Seeder Classes**: Seeders are classes that extend the `\Luminova\Database\Seeder` abstract class. Each seeder class contains a `run` method where you define the data to be inserted into your database tables. This structure helps maintain clean and organized code.

2. **Run Method**: The `run` method within a seeder class is where the actual seeding logic resides. Here, you can specify which tables to populate and the data to be inserted. This method ensures that your seed data is applied consistently every time it is executed.

3. **Calling Other Seeders**: Within a seeder's `run` method, you can call other seeders using the `invoke` method. This allows you to break down complex seeding operations into smaller, manageable tasks and maintain a clear seeding hierarchy.

4. **Seeder Setup**: Create a class that extends `\Luminova\Database\Seeder`. Ensure all seed classes are placed in the `/app/Database/Seeders/` directory for proper run.

5. **Seeding Management**: Luminova provides `novakit` commands to run, rollback, and manage seeders. These commands ensure that your seed data is applied in the correct order and allow you to easily revert changes if necessary.

***

* Class namespace: `\Luminova\Database\Seeder`
* This class is an **Abstract class**

***

## Methods

See documentation reference for  [Database Builder](/database/builder.md).

### run

Run database table seeder.

```php
public run(\Luminova\Database\Builder $builder): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$builder` | **\Luminova\Database\Builder** | Database builder instance. |

***

### Run Example

This example demonstrates how to create a `seed` class to insert data into a database table. You can implement various insertion methods based on your needs, such as using the `insert` method for array-based insertion or the `exec` method for SQL query string insertion.

```php
<?php
namespace App\Database\Seeders;

use \Luminova\Database\Seeder;
use \Luminova\Database\Builder;

class UsersTableSeeder extends Seeder
{
    public function run(Builder $builder): void
    {
        $builder->table('users')->insert([
            'name' => 'Peter',
            'phone' => '080673653',
            'email' => 'peter@example.com',
        ]);
    }
}
```

***

### invoke

Invokes another seeder within the current seeder's run method.

```php
final protected invoke(class-string<\Luminova\Database\Seeder> $seed): void
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `$seed` | **class-string<\Luminova\Database\Seeder>** | The seed class name. |

> **Note:** When you use the `invoke` method to specify another seeder class, it will also be executed during the seeding process or when rolling back seeds.

***

### Seeding Command

To executes all pending database seeders.

```bash
php novakit db:seed
```

To executes a specific pending database seeder class.

```bash
php novakit db:seed --class=TestSeeder
```

***

#### Command Options

- `-c, --class`
  - **Description:** Specify the seeder class to run.
  - **Usage:** `php novakit db:seed --class=TestSeeder`
  
 - `-t, --table`
  - **Description:** Specify the seeder class table name to automatically create a backup which can be restored back if rollback fails.
  - **Usage:** `php novakit db:seed --table=foo`
  
- `-n, --no-backup`
  - **Description:** Run seeder without creating a backup. This should be used with caution as it will create not backup to rollback to.
  - **Usage:** `php novakit db:seed --no-backup`

- `-r, --rollback`
  - **Description:** Rollback the seeder to the previous version. This allows you to revert changes that were applied.
  - **Usage:** `php novakit db:seed --rollback`

- `-i, --invoke`
  - **Description:** Run seeder and also invoke other invokable seeders classes.
  - **Usage:** `php novakit db:seed --invoke`

***

## Truncate  Documentation

The `db:truncate` command is used to truncate a database table, effectively removing all records within the table. Unlike traditional deletion methods, truncation is a quick operation that empties the table without logging individual row deletions, making it significantly faster for large datasets.

### Table Truncation

Truncation clears all records from the specified database table. It does not trigger the execution of seeders and does not update the seeder version after truncation. This means that if you intend to reseed after truncation, you must ensure that your seeder is updated accordingly.

### Usage

To use the `db:truncate` command, follow these examples:

#### Truncate a Specific Table

```bash
php novakit db:truncate --table=TestTable
```

This command will truncate the `TestTable` in the database, removing all its records.

***

#### Options

- `-t, --table`: Specifies the name of the database table to truncate.
- `-n, --no-transaction`: Runs the database truncation without wrapping it in a transaction. This option can be useful when you do not need the rollback capability typically provided by transactions.

***

### Examples

To truncate a Table Without Transaction.

```bash
php novakit db:truncate --table=TestTable --no-transaction
```

In this example, the `--no-transaction` option is used to truncate `TestTable` without using a transaction, which can be beneficial in certain scenarios where transactions are not required or desired.

***

### Considerations

- **Seeder Locks**: Truncation ignores seeder locks. It's essential to manage and update your seeders appropriately after truncating tables to avoid inconsistencies.
- **Reseeding**: After truncation, ensure your seeder class has been updated from the last version. Alternatively, use the `db:clear` command to clear the seeder if necessary before reseeding to avoid conflicts or errors.